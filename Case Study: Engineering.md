
# Case Study: Preventing Silent Payment State Corruption by Fixing `UnitOfWork`Lifetime Ownership

> **Status**: Draft (The code snippets should be revised)
> **Confidentiality**: This case study is following Non-Disclosure Agreement. No employer, product, vendor, or domain-specific names are included.
---

## Summary

I diagnosed and fixed a concurrency bug in an async Python payment service where a shared SQLAlchemy `UnitOfWork` instance caused intermittent lost database writes under concurrent requests. The root cause was that the service used only one `UnitOfWork` instance shared across requests through Python's default-argument evaluation and boot-time dependency injection. Binding new ORM session to the `UnitOfWork` instance per request did overwrite current session, causing critical payment state corruption.

I fixed this issue by instantiating and injecting a new `UnitOfWork` object per request. This ensured every command handled its own database session. After the fix, the deliberately-racy reproducer no longer produced shared session IDs or lost writes.

---
## Table of Contents

1. [System Context](#1-system-context)
2. [Production Symptom](#2-production-symptom)
3. [Diagnostic Strategy](#3-diagnostic-strategy)
4. [Root Cause](#4-root-cause)
5. [Why the Bug Caused Lost Writes](#5-why-the-bug-caused-lost-writes)
6. [Why Low Traffic Concealed the Bug](#6-why-low-traffic-concealed-the-bug)
7. [Why the Failure Was Often Silent](#7-why-the-failure-was-often-silent)
8. [The Fix](#8-the-fix)
9. [Verification](#9-verification)
10. [Architecture Before and After](#10-architecture-before-and-after)
11. [Lessons Learned](#11-lessons-learned)
12. [What I Would Do Differently Next Time](#12-what-i-would-do-differently-next-time)

---
## 1. System Context

The service was a backend payment-processing component written with **FastAPI** and **SQLAlchemy**. The architecture followed a Domain-Driven Design style similar to the structure popularized by *Architecture Patterns with Python*.

The main components were:

- **Repository**: wrapped SQLAlchemy and exposed access to domain aggregates.
- **`UnitOfWork`**: wrapped a SQLAlchemy `Session` and provided atomic commit/rollback behavior with the context-manager.
- **MessageBus**: dispatched commands and events to registered handlers.
- **Bootstrap**: wired concrete dependencies such as `UnitOfWork`, external clients, and producers into handlers.

The endpoints were defined as `async def`, but the SQLAlchemy ORM session and database driver were synchronous. Async behavior occurred around downstream network calls, such as payment gateways and internal services.

This distinction mattered. In a single-threaded asyncio runtime, concurrency is cooperative. Coroutines yielded control at `await` expressions. Code between two `await`s is not interrupted by the event loop, but any mutable state held across an `await` can still be accessed by another coroutine if that state is shared.

---

## 2. Production Symptom

The service showed a small but persistent stream of corrupted payment records.

A typical user-facing symptom was:

> "I paid, but the order did not update."

From the system's perspective, the payment charge succeeded, but the corresponding domain record's status sometimes failed to be committed to the database.

The bug had four important properties:

- **Non-deterministic**: Replaying a single request did not reproduce it.
- **Volume-dependent**: It appeared under concurrent production load, but almost never appeared in staging or dev environment.
- **Silent**: There was usually no exception, rollback log, or obvious 5xx response.

This combination pointed toward a concurrency issue involving shared mutable state. It also explained why ordinary single-request tests did not catch the problem.

---

## 3. Diagnostic Strategy

First, I hypothesized that the ORM `Session` was being shared across concurrent requests.

Instead of trying to infer the root cause from production logs, I built a deliberately-racy diagnostic test. The goal was to widen the race window and make the suspected bug deterministic under modest concurrent load.

The test had three parts:

1. A temporary HTTP endpoint.
2. A handler that opened the `UnitOfWork`, loaded or created a record, awaited a long sleep inside the `UnitOfWork` block, and then committed.
3. Logging that compared the `UnitOfWork`'s current session identity against the ORM entity's owning session identity.

```python
async def diagnostic_handler(cmd, *, `UnitOfWork`):
    with `UnitOfWork`:
        session_id = `UnitOfWork`.session.hash_key

        record = `UnitOfWork`.records.filter(...).get()
        if not record:
            record = make_record(...)
            await asyncio.sleep(10)  # Intentionally widen the race window

        logger.info(
            f"{session_id} | entity session = "
            f"{record._sa_instance_state.session_id}"
        )

        `UnitOfWork`.commit()
````

The `await asyncio.sleep(10)` was intentional. It made overlapping coroutine execution likely during a short load test.

The test also logged two separate identifiers:

- the current ORM session owned by the `UnitOfWork`
    
- the session associated with the loaded ORM entity
    

Because this was temporary diagnostic code, I intentionally inspected SQLAlchemy internal state through `_sa_instance_state.session_id`. It was useful evidence for confirming whether entities were bound to the expected session.

When I ran several concurrent requests against the test, multiple coroutines reported the same session identity. That confirmed that requests which should have had independent units of work were sharing the same underlying state.

I then narrowed the issue further by bypassing the MessageBus and calling the service function directly. The race persisted only when the MessageBus path was used. That reduced the suspect area from "the `UnitOfWork` factory" to "the Bootstrap and dependency-injection layer."

---

## 4. Root Cause

The `Bootstrap` class stored `UnitOfWork` dependencies as default-valued constructor parameters:

```python
class Bootstrap:
    def __init__(
        self,
        unit_of_work: UnitOfWork = UnitOfWork(),
        ...
    ):
        self.unit_of_work = unit_of_work
```

This is the `mutable default argument anti-pattern`.

In Python, default expressions are evaluated once when the function object is defined, typically during module import. The resulting objects are stored in the function object's `__defaults__`. Every call to `Bootstrap()` without explicit arguments receives the same `UnitOfWork` instance.


The problem was amplified by boot-time dependency injection:

```python
def __call__(self):
    dependencies = {
        "unit_of_work": self.unit_of_work,
        ...
    }
    
    return MessageBus(
        ...,
        dependencies=dependencies,
    )
```

The MessageBus instance receives the same `UnitOfWork` instance. Because those `UnitOfWork` objects came from default constructor instances, all concurrent requests use `UnitOfWork` with the same reference.

The effective result was one `UnitOfWork` instance living for the lifetime of the process.

---

## 5. Why the Bug Caused Lost Writes

The SQLAlchemy `Session` was stored as a mutable attribute on the `UnitOfWork` and assigned inside `__enter__`:

```python
class UnitOfWork:
    def __enter__(self):
        self.session = self.session_factory()
        self.session.expire_on_commit = False
        ...

    def __exit__(self, *args):
        self.rollback()
        self.session.close()
```

That design is safe only if each request owns its own `UnitOfWork` instance.

But with a single shared `UnitOfWork` throughout the system, coroutines could overwrite each other's `self.session`.

Example interleaving:

| Step | Coroutine A                     | Coroutine B                     | Shared `UnitOfWork.session`                        |
| ---- | ------------------------------- | ------------------------------- | -------------------------------------------------- |
| 1    | `__enter__` creates `session_A` | -                               | `session_A`                                        |
| 2    | Adds `record_X` to `session_A`  | -                               | `session_A`                                        |
| 3    | Awaits downstream payment call  | -                               | `session_A`                                        |
| 4    | -                               | `__enter__` creates `session_B` | `session_B`                                        |
| 5    | -                               | Adds `record_Y` to `session_B`  | `session_B`                                        |
| 6    | -                               | Commits                         | `session_B`                                        |
| 7    | Coroutine A resumes             | -                               | `session_B`                                        |
| 8    | Coroutine A calls `commit()`    | -                               | Operates through the overwritten session reference |

Once Coroutine A resumed, it no longer operated through the session it originally opened.

Depending on the exact timing, this could cause several failure modes:

- **Lost writes**: data added to `session_A` before the `await` was never committed.
- **Detached or inconsistent ORM state**: objects could remain in memory while their owning transaction was no longer the transaction being committed.
- **Wrong-session operations**: later mutations from Coroutine A could route through a `UnitOfWork` whose session reference had been overwritten by Coroutine B.
- **Rollback or cleanup of the wrong session**: the context manager's exit path could operate on a session different from the one opened at entry.

---

## 6. Why Low Traffic Concealed the Bug

The vulnerable point was the time spent inside `with `UnitOfWork`:` across an `await`.

At low traffic, two requests rarely overlapped inside that window. Under higher traffic, each request had more opportunities to overlap with another in-flight request.

Across many requests, the number of risky pairwise overlaps grows quickly with traffic volume. That explains why the issue appeared rare in staging but persistent in production.

---

## 7. Why the Failure Was Often Silent

The service used:

```python
session.expire_on_commit = False
```

This setting is often reasonable in FastAPI-style services because it allows ORM-backed domain objects to remain usable during response serialization after commit.

However, in this bug, it likely made the failure mode harder to detect.

Because objects retained their in-memory attribute values after commit, some stale or inconsistent states could continue through response serialization without forcing a database refresh. With expiration enabled, some paths may have failed earlier through refresh or detachment errors, but that would have depended on the exact interleaving and object state.

The fix did not depend on changing `expire_on_commit`. The correct fix was to enforce per-message `UnitOfWork` ownership. Under proper session ownership, `expire_on_commit=False` was not the root cause.

---

## 8. The Fix

The remediation had two main changes:

1. Replace instance defaults with factory defaults.
    
2. Move `UnitOfWork` creation from boot time to dispatch time.

Together, these changes enforced the key invariant:

> Every MessageBus instance gets a fresh `UnitOfWork` instance, and every `UnitOfWork` owns exactly one ORM Session for the duration of one atomic operation.

---

## 8.1 Replaced Instance Defaults with Factory Defaults

Before:

```python
class Bootstrap:
    def __init__(
        self,
        unit_of_work: UnitOfWork = UnitOfWork(),
        ...
    ):
        self.unit_of_work = unit_of_work
```

After:

```python
class Bootstrap:
    def __init__(
        self,
        unit_of_work_factory: Type[UnitOfWork] = UnitOfWork,
        ...
    ):
        self.unit_of_work_factory = unit_of_work_factory
```

The default values became class objects rather than one initialized instance.

A class object is safe to share as a factory. Calling `unit_of_work_maker()` later creates a fresh `UnitOfWork` instance with its own `self.session` attribute.

---

## 8.2 Moved Dependency Injection from Boot Time to Dispatch Time

Before the fix, handlers received pre-injected dependencies when the `MessageBus` was constructed.

After the fix, the `MessageBus` received:

- raw handler registries
    
- `UnitOfWork` factories
    
- shared stateless dependencies
    
- per-dispatch dependency creation logic
    

```python
class MessageBus:
    def __init__(
        self,
        *,
        unit_of_work_factory,
        handler,
        ...
    ):
        self.unit_of_work_factory = unit_of_work_factory
        self.handler = handler

    async def handle(self, message):
        ...
        
        self._get_new_unit_of_work()
		
		result = await handle_message(message)
		
		return result

    def _get_new_unit_of_work(self):
        # generate a new unit of work
        self.unit_of_work = unit_of_work_factory()
```

This changed the lifetime model:

| Dependency         |                         Old lifetime |                New lifetime |
| ------------------ | -----------------------------------: | --------------------------: |
| Bootstrap          |          Process/request composition | Process/request composition |
| MessageBus         |                          Per request |                 Per request |
| `UnitOfWork`       |                     Process lifetime |        Per message dispatch |
| SQLAlchemy Session | Shared through `UnitOfWork` mutation |   Owned by one `UnitOfWork` |

---

## 9. Verification

I re-ran the diagnostic test after the fix.

Before the fix:

- Concurrent requests had the same `UnitOfWork` session identity
- ORM entities could be associated with a session different from the current logical request
- The artificial 10-second interleaving reproduced lost writes

After the fix:

- Concurrent requests had distinct session identities
- ORM entity session IDs matched the `UnitOfWork` session that loaded them
- The deliberately-racy interleaving no longer produced lost writes

The diagnostic endpoint was retained temporarily while the fix was verified, then removed after the architectural change was confirmed stable.

---

## 10. Architecture Before and After

### Before

```text
Bootstrap.__init__()
    └── creates `UnitOfWork` instance once as a default argument
		            └── shared across Bootstrap() calls

Bootstrap.__call__()
    └── injects shared `UnitOfWork` into handlers

MessageBus.handle()
    └── calls handler with pre-bound shared `UnitOfWork`

Handler
    └── with `UnitOfWork`:
            └── `UnitOfWork`.session = new Session()
            └── await downstream call
            └── another coroutine can overwrite `UnitOfWork`.session
```

### After

```text
Bootstrap.__init__()
    └── stores `UnitOfWork` class as a factory

Bootstrap.__call__()
    └── passes factories and raw handlers into MessageBus

MessageBus.handle()
    └── creates fresh `UnitOfWork` for this message
    └── injects fresh `UnitOfWork` into handler

Handler
    └── with `UnitOfWork`:
            └── each `UnitOfWork` instance owns its own session
            └── await downstream call
            └── another coroutine cannot overwrite this `UnitOfWork`'s session
```

---

## 11. Lessons Learned

### 11.1 Never instantiate stateful objects in default arguments

This was the immediate root cause.

```python
def func(depends=SomeClass()):
    ...
```

does not create a new object per call. It creates one object when the function is defined and reuses it for all calls that omit the argument.

Safer alternative could be:

```python
def f(dep_maker=SomeStatefulClass):
    dep = dep_maker()
```

---

### 11.2 Bind dependency lifetime to the operation it belongs to

A `UnitOfWork` represents one atomic transaction. Its SQLAlchemy `Session` should not outlive that operation.

The old design effectively stored a request-scoped or message-scoped database resource inside a longer-lived dependency container. That created a lifetime mismatch.

The fix moved `UnitOfWork` construction to the message-dispatch boundary, where the lifetime naturally belonged.

---

### 11.3 Async Python is not race-free

Single-threaded asyncio avoids many preemptive threading problems, but it does not eliminate races from context switching.

The key question is not only:

```text
Is this object thread-safe?
```

The better question for asyncio services could be:

```text
Can two coroutines hold logical access to this mutable object across an await?
```

If the answer is yes, the object can still be a race condition.

---

### 11.4 Use deliberately-racy tests for intermittent concurrency bugs

Production logs often show the symptom after the cause has already disappeared downstream.

A small controlled reproducer can be more useful than more logging in the production path. In this case, adding an intentional `await asyncio.sleep(10)` inside the `UnitOfWork` block turned a rare production issue into a deterministic test scenario.

That made the root cause observable instead of speculative.

---

## 12. What I Would Do Differently Next Time

If I were designing a similar service from scratch, I would make several changes earlier.

### 12.1 Add lint protection against stateful default arguments

I would configure lint rules or static checks to flag constructed objects in default arguments, especially in dependency containers and bootstrap code.

This turns the lesson into a tool-enforced rule instead of relying only on code review memory.

---

### 12.2 Make session lifetime explicit at the framework boundary

For a FastAPI service, I would consider managing database session lifetime through a request-scoped dependency using `yield` in `Depends`.

That would make the intended lifecycle visible at the framework boundary and reduce the chance of accidentally storing a stateful session owner in a longer-lived object.

---

### 12.3 Add concurrency-focused regression tests

For shared-state surfaces such as a MessageBus or `UnitOfWork` path, I would add a focused concurrency test that intentionally places an `await` inside the critical lifecycle window and asserts that each concurrent operation owns a distinct `UnitOfWork`/session.

---

### 12.4 Consider using a transactional outbox for cross-service events

For events that cross service boundaries, I would avoid relying only on in-process event dispatch. A transactional outbox would make database state changes and outgoing integration events more reliable as a single persistence-backed workflow.

This is a separate architectural improvement, but this incident made the boundary between local transaction ownership and external side effects more visible.

---

## Appendix: Glossary

### Repository Pattern

An object that exposes domain entities as if they were an in-memory collection while hiding ORM and SQL details from the domain layer.

### UnitOfWork

An object that wraps a database session and provides atomic commit/rollback behavior, often through a context manager such as `with `UnitOfWork`:`.

### MessageBus

An object that maps commands or events to handlers and dispatches them with the dependencies they need.

### Bootstrap

The composition root that constructs concrete dependencies and wires them into the application.

### Mutable Default Argument

A Python behavior where default argument values are evaluated once when the function is defined. If the default is a mutable or stateful object, all calls that omit the argument share the same object.

### Cooperative Concurrency

A concurrency model where tasks voluntarily yield control at specific points. In Python asyncio, this happens at `await` expressions. This differs from preemptive threading, where a scheduler may interrupt execution at many points.
