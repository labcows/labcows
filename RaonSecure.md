# Quality Assurance for an AR/XR Metaverse Education Platform Launch

**Education Platform QA Portfolio**

**Company Website:** [RaonSecure](https://www.raonsecure.com/en)

Validating gameplay-like client behavior, account integrity, content access, and cross-build consistency for South Korea's first AR/XR metaverse-based education platform, *Metademy*, ahead of its 2026 launch.

| Metric | Value |
| --- | --- |
| Issues reported | 150+ |
| Platform user growth | +20% |
| B2B content partners (YoY) | 11 → 31 (+200%) |
| Active B2B contracts retained | 28 |
| AR/XR training modules validated | 40+ |

## Company Introduction

RaonSecure is a leading AI-powered security and authentication platform provider in South Korea, reporting $44M USD in revenue for 2025. As a pioneer in FIDO-based biometric authentication and blockchain-based digital identity (DID), the company serves over 2,000 corporate and government clients. By integrating generative AI into its security suite, RaonSecure continues to lead the digital transformation of secure mobile and cloud environments.

## Background

RaonSecure set out to launch *Metademy*, South Korea's first AR/XR metaverse-based education platform, in 2026. The project initially began on Unity, but a mid-development strategic pivot to Unreal Engine forced a complete ground-up rebuild of every feature, leaving a roughly two-month window to verify and stabilize the platform for official release.

The accelerated timeline produced frequent requirement changes and ambiguous specifications. QA absorbed a second role in that environment: working directly with PMs to clarify functional intent so a reusable, scalable test suite could be built against a moving target.

My responsibilities included:

1. Running validation passes against every build target — Unreal Client, WebView, and Launcher.
2. Surfacing client crashes, login failures, and infinite-loading paths before they reached enterprise users.
3. Drafting test cases for the rebuilt modules (광장 / Plaza, 마이룸 / MyRoom, 온라인 강의실 / Online Classroom, 튜토리얼 / Tutorial, 프렌즈 / Friends, 콘텐츠 관리 / Content Manager).
4. Collaborating with PMs to convert ambiguous specs into testable acceptance criteria.
5. Escalating cross-module inconsistencies as systemic UX/data issues rather than per-screen defects.

## Tech Stack

| Stack | Summary |
| --- | --- |
| **Unreal Engine 5 Client** | Reproduced runtime crashes, login races, and state desync against the packaged client; used in-editor inspection to separate level-design bugs from gameplay-state bugs. |
| **Jira** | Filed, triaged, and verified 150+ defect tickets across Client, WebView, and Launcher with full reproduction steps, video, screenshots, and build version (2.1.3.0). |
| **Zephyr Scale** | Authored and maintained the test case suite for the rebuilt modules; mapped cases to acceptance criteria clarified with PMs. |
| **Google Sheets** | Tracked cross-build coverage, regression status, and pre-launch readiness for the 40+ training modules. |

## Goals

The team needed *Metademy* to ship on a fixed 2026 launch date without sacrificing the trust enterprise B2B clients expect from a paid training platform. My goal was to make sure no client crash, login race, or content-access bypass reached the release build, and to flag the consistency gaps — close-button behavior, empty-state messaging, Korean input handling, keyboard-shortcut scope — that would otherwise erode the user experience across 40+ training modules.

## Actions

### Testing Coverage Highlights

Reported 150+ issues across three build targets and the major rebuilt modules, with cases mapped back to PM-clarified acceptance criteria so coverage stayed defensible as specs shifted.

| Focus | Contribution |
| --- | --- |
| **Build coverage** | Validated each fix across all three targets — Unreal Client, WebView (dashboard / 공지사항 / 디지털 배지), and Launcher (install, update, uninstall) — catching regressions that single-target sweeps would have missed. |
| **Module coverage** | Filed bugs across Plaza, MyRoom, Online Classroom, Tutorial, Friends, Notifications, Profile/Avatar, Content Manager, Camera, Mini-map, Hoverboard, and Login. |
| **Spec collaboration** | Worked with PMs to convert ambiguous post-pivot requirements into testable acceptance criteria, then encoded them in Zephyr Scale so the suite remained reusable as the spec evolved. |
| **Content category sweep** | Verified 40+ training modules spanning IT 실습, 물리치료 VR, TOPIK Korean-language testing, 보안 실습, 직무훈련 일반화학 시뮬레이션, and 조종훈련 실습관. |
| **Reproduction quality** | Attached video, screenshots, save state, and exact RC build version on the majority of tickets, including login-state captures for race-condition bugs that depended on prior session data. |

### Defect Discovery Highlights

Caught issues spanning client crashes, login races, infinite-loading paths, and content-access bypasses across the rebuilt modules.

| Focus | Contribution |
| --- | --- |
| **Critical login & auth bugs** | Identified that all social-login routes were silently being collapsed onto Google OAuth (METADEMY-1883), a login flow that crashed the client with a hotspot on failure (METADEMY-1884), and a missing tutorial step 6 that blocked first-run completion (METADEMY-1921). |
| **Client crashes & launcher failures** | Reported reproducible client crashes (METADEMY-1620, METADEMY-1100-class issues), Alt+F4 shutdown crash (METADEMY-1705), Launcher double-launch (METADEMY-1579), and network-off exception gaps in the Launcher boot flow (METADEMY-1617, METADEMY-1616). |
| **Progression & infinite loading** | Caught infinite-loading on entering a deleted online classroom (METADEMY-1690), on classroom file upload (METADEMY-1782), on the renewal button on the main page (METADEMY-1612), and on duplicate-login races (METADEMY-1713). |
| **State & data integrity** | Logged-out session data not resetting on re-login (METADEMY-1749), LIVE session ending without logging the user out (METADEMY-1744), notification panel not reflecting state in real time (METADEMY-1750, METADEMY-1748), and B2B account email mis-rendered (METADEMY-1791). |
| **Content access & authorization** | Surfaced that 실습 콘텐츠 관리 used the local install file as the source of truth instead of the current account's entitlement (METADEMY-1875) — a B2B revenue-loss risk before launch. Full narrative in [Case Study: Quality Assurance.md](Case%20Study:%20Quality%20Assurance.md). |
| **Localization & input** | Reported Korean input blocked in the dashboard 질문하기 form (METADEMY-1652), Korean search blocked in 공지사항 (METADEMY-1850), language-selector dropdown rendering off-screen (METADEMY-1667), and Korean character composition truncation on duplicate-nickname check (METADEMY-1913). |
| **Hardware & permission** | Flagged mic permission denial breaking IT 실습 voice features (METADEMY-2036) and voice chat / mic non-functional in Plaza (METADEMY-1799). |

### System-Level Advocacy Highlights

Where the same root cause produced inconsistent player experience across the platform, I raised the issue as a design and consistency concern rather than as a single defect.

| Focus | Contribution |
| --- | --- |
| **Account-to-email data model** | Flagged that the system allowed multiple accounts to share the same email address with no enforced one-to-one constraint, escalating it as a data-modeling concern rather than a UI validation bug. Full narrative in [Case Study: Quality Assurance.md](Case%20Study:%20Quality%20Assurance.md). |
| **Close-button behavior unification** | Catalogued cases where close buttons either rendered twice or were non-functional across Dashboard (METADEMY-1631), Digital Badge (METADEMY-1636), MyRoom (METADEMY-1716), and the modal login (METADEMY-1590), proposing a shared component instead of per-screen fixes. |
| **Empty-state message standardization** | Reported missing empty-case copy across notifications, blocked-users list, attendance lists, and content lists (METADEMY-1656, METADEMY-1920, METADEMY-1923, METADEMY-1938, METADEMY-1941, METADEMY-1967), framing it as a shared component contract rather than per-screen text edits. |
| **Keyboard-shortcut scope discipline** | Caught client-wide shortcut keys (M for mini-map, V for hoverboard, Enter for chat) firing inside text-input contexts (METADEMY-1648, METADEMY-1525, METADEMY-1692, METADEMY-1708), recommending an input-focus guard at the input layer rather than per-shortcut suppression. |
| **Korean input across input fields** | Grouped Korean-input failures across dashboard 질문하기, 공지사항 search, and dialog inputs (METADEMY-1652, METADEMY-1850, METADEMY-2104) so engineering could treat them as one IME/input-layer fix instead of per-field text bugs. |

## Selected Deep Dives

### A silent collapse of every social login onto Google

Auto-login appeared to work for every account during normal smoke tests. Testing with a clean profile across each provider revealed that all social-login routes were resolving to the same Google identity behind the scenes (METADEMY-1883). I reported this as Critical with the auth-callback trace and account-state captures; it was a pre-launch blocker for B2B clients whose SSO bindings depend on the correct identity provider being recorded.

### Local file presence treated as content entitlement

Account A could download a training module, and Account B could then sign in on the same machine and access the same module without an entitlement (METADEMY-1875). I escalated this not as a UI list bug but as an authorization bug — local caching had silently become the source of truth for permissions. Detailed in the linked case study.

### Account-to-email data integrity

Two accounts could be configured to share the same email address with no enforced uniqueness, leaving notification routing, password recovery, and billing ownership ambiguous. I raised this as a data-modeling concern rather than a missing UI validator, and engineering paused related work to revisit the constraint. Detailed in the linked case study.

## Results

| Metric | Result | Impact |
| --- | --- | --- |
| **150+** | Issues reported | Across Client, WebView, and Launcher, including runtime-crashing system-level bugs, login races, and content-authorization bypasses caught before launch. |
| **+20%** | Platform user growth | Stabilized first-run experience (tutorial, login, authorization) held up under enterprise rollout. |
| **11 → 31** | B2B content partnerships YoY | +200% growth supported by 40+ validated training modules. |
| **28** | Active B2B contracts retained | Rigorous validation of AR/XR training modules delivered a reliable job-training experience for end users. |
| **40+** | Training modules validated | Spanning IT 실습, 물리치료 VR, TOPIK, 보안 실습, and 조종훈련 실습관. |

## Takeaways

- Under a fixed launch date with shifting specs, QA's value compounded most when it doubled as a spec-clarification function. PM-aligned acceptance criteria were what made the test suite reusable; defect counts were the byproduct.
- Three-build coverage (Client / WebView / Launcher) caught a class of regressions — close-button behavior, empty-state copy, Korean input — that a single-target sweep would have shipped.
- Login and tutorial paths were the highest-leverage surfaces. Anything that ran before authentication or first-content was load-bearing for retention, and the social-login collapse and missing tutorial step 6 would have been launch-blocking incidents in production.
- Local caching is not an authorization layer. The B2B content-access bypass was the kind of bug that looks like a UI list defect until you realize the cache had silently become the source of truth — worth raising as a system contract concern, not a single ticket.
