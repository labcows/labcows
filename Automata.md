# Building and Maintaining Online Payment Systems for 10 Enterprise-Level Clients

**Backend Engineering Portfolio**

**Company Website:** [Automata](https://www.automata.ooo/)

Processing over $500K per month in online transactions across card payments, points, coupons, gift cards, subscriptions, refunds, cancellations, and backoffice operations.

| Metric | Value |
| --- | --- |
| Monthly online transaction volume | $500K+ |
| Payment success rate maintained | 99.9% |

## Company Introduction

Automata is a B2B online shopping platform service that provides branded online shopping mall infrastructure, including website operations and online payment systems, for major enterprise clients. In 2023, Automata served over 10 B2B clients and generated $2.7M in annual revenue.

## Background

From December 2022 to November 2023, I worked as Backend Engineer I and Backend Engineer II at Automata, receiving a promotion in April 2023. I contributed to the development and maintenance of core payment features, including card payments, points, coupons, gift cards, subscription payments, refunds, cancellations, and third-party API integrations for enterprise-level online shopping malls.

## Tech Stack

| Stack | Summary |
| --- | --- |
| **Python / FastAPI** | Built backend payment workflows for checkout, subscriptions, cancellations, refunds, validations, batch jobs, and provider integrations. |
| **PostgreSQL / SQLAlchemy / Alembic** | Modeled and migrated payment data structures to preserve transaction accuracy, auditability, and operational reporting. |
| **Pydantic** | Defined request and response contracts for payment, cancellation, subscription, and provider APIs to keep integrations predictable. |
| **External APIs** | Integrated 5+ payment gateway providers, 3+ external point providers, and gift card providers into checkout, cancellation, reward, and reconciliation workflows. |
| **Pytest / Mypy / Flake8 / Locust** | Maintained regression confidence with unit, integration, E2E, type, lint, and load tests around payment reliability. |
| **Azure DevOps** | Managed delivery through ticket triage, bug resolution, implementation coordination, and closure across the software development cycle. |

## System Context

Harmony Transaction was a backend payment-processing service built with FastAPI, SQLAlchemy, and PostgreSQL. The architecture followed a Domain-Driven Design style similar to the structure popularized by *Architecture Patterns with Python*, separating domain behavior, persistence, transaction boundaries, and application orchestration.

| Component | Role |
| --- | --- |
| **Domain** | Encapsulated core payment business logic and state transitions for transactions, payments, refunds, cancellations, subscriptions, point usage, coupons, and gift cards without depending directly on infrastructure concerns. |
| **Repository** | Wrapped SQLAlchemy queries and exposed access to domain aggregates. |
| **UnitOfWork** | Managed SQLAlchemy session life-cycles and provided atomic commit/rollback behavior through context-manager based transaction handling. |
| **MessageBus** | Dispatched commands and domain events to registered handlers for payment creation, payment-gateway callbacks, cancellations, refunds, subscriptions, and external API side effects. |
| **Bootstrap** | Wired concrete dependencies such as UnitOfWork implementations, external payment-gateway and point clients, repositories, and message producers into handlers. |

## Goals

The payment system was expanding across multiple marketplaces while the number of launched online stores increased by 60% during my tenure. To support that growth, the platform needed to evolve beyond card payments and support points, coupons, gift cards, subscriptions, and provider-specific checkout rules. My core goal was to help build a stable and flexible payment system that could integrate new business features quickly without sacrificing transaction accuracy, auditability, or operational reliability.

## Actions

### Development Highlights

Developed core payment features, external provider integrations, and automated billing pipelines.

| Focus | Contribution |
| --- | --- |
| **Provider integrations** | Integrated 5+ third-party payment gateway providers and 3+ external point providers into checkout, cancellation, and reward workflows. |
| **Gift cards and coupons** | Developed gift card and coupon transaction features, including pin issuance, status transitions, expiration handling, and refund-amount separation between card payments and points. |
| **Automated billing** | Implemented an automated billing pipeline with cron jobs to support the new subscription product flow. |
| **Point history** | Designed and deployed the point history persistence layer, including database tables, repository access, list APIs, and request/response audit columns so point-related failures could be attributed and recovered. |
| **Marketplace setup** | Supported one-click per-marketplace payment gateway configuration by implementing payment gateway setting tables and CRUD APIs. |
| **Scheduled recovery** | Built scheduled jobs for order state transitions and expired gift cards, plus reconciliation scripts for retrying missing point rewards and recovering payment data. |
| **Internal APIs** | Implemented internal service-to-service APIs consumed by upstream channel and product services. |

### Maintenance and Optimization Highlights

Ensured mission-critical system stability by patching validation gaps, optimizing database execution, and resolving concurrency issues that affected transaction integrity.

| Focus | Contribution |
| --- | --- |
| **Validation gaps** | Preemptively blocked invalid checkout attempts by discovering and patching gaps in existing payment validation logic for amounts and quantities. |
| **Gift card hotfix** | Protected payment integrity by identifying a critical gift card calculation flaw and deploying a hotfix to prevent underpaid transactions. |
| **Query optimization** | Resolved an N+1 query bottleneck through eager loading, capping database execution at 3 queries regardless of data volume in the affected flow. |
| **Concurrency fix** | Diagnosed and resolved an asyncio concurrency bug that caused silent payment data loss under load; reproduced it with a targeted concurrency probe and secured transaction integrity by isolating Unit of Work injection per message. |
| **Test coverage** | Strengthened safe refactoring by building a testing pyramid across unit, integration, and E2E tests with over 75% coverage for core business logic. |

## Selected Deep Dives

### Cooperative-concurrency race in the UoW layer

A FastAPI + SQLAlchemy payment service backed by Cosmic Python suffered intermittent silent data loss under concurrent load. Root cause: a mutable default argument in the bootstrap turned the Unit of Work into an accidental singleton, so two coroutines could clobber each other's session across an `await`. The fix relocated dependency injection from boot-time to dispatch-time and replaced instance defaults with factory defaults. I studied and reproduced this fix as a learning exercise.

### Transactions stuck in pending state

A class of payment transactions occasionally failed to resolve to a terminal state, paid or failed, and remained stuck in pending. I worked on this in production and intend to reconstruct the failure modes: missing webhooks, user abandonment, payment-gateway timeouts, duplicate webhooks, and races between webhook and user action, and to design the reconciliation and explicit-timeout behavior the original system lacked.

## Results

| Metric | Result | Impact |
| --- | --- | --- |
| **4 months** | Promotion | Promoted from Backend Engineer I to Backend Engineer II within my first 4 months at Automata. |
| **$500K+/mo** | Scale | Supported systems processing over $500K/month in online transactions, contributing to $2.7M USD in yearly net revenue. |
| **99.9%** | Reliability | Helped maintain 99.9% payment success rates. |
| **8+ flows** | Payment coverage | Built and maintained flows for card payments, points, coupons, gift cards, subscriptions, refunds, cancellations, and backoffice operations. |
| **5+ / 3+** | Partner payment options | Helped partners offer more valuable checkout options by integrating 5+ payment gateway providers, 3+ external point providers, and gift card provider flows. |

## Takeaways

- I learned that payment systems need more than successful API calls; they need a reliable state model, idempotent retries, complete audit logs, and clear provider-specific failure handling.
- Next time, I would define a stronger shared payment ledger and provider event state machine earlier, especially before adding multiple payment gateway, point, coupon, and gift card providers into the same checkout flow.
- I would also invest earlier in reconciliation dashboards, contract tests for external providers, and standardized callback simulations so production-only payment edge cases can be caught before release.
