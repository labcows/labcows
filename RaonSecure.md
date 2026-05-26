# Quality Assurance for an AR/XR Education Platform with 40+ Training Modules

<p align="center">
  <img src="./assets/Metademy%20-%20banner.jpg" width="100%" alt="Metademy banner" />
</p>

## Project Summary

Served as QA analyst for *Metademy*, an AR/XR metaverse-based education platform, during an accelerated rebuild from Unity to Unreal Engine. Validated Unreal Client, WebView, and Launcher builds, reported 150+ issues, and supported release readiness across 40+ training modules.

<p align="center">
  <img src="./assets/Metademy%20-%20Image.png" width="100%" alt="Metademy banner" />
</p>

<p align="center">
  <a href="https://youtu.be/lMSryzdZiHA?si=a5vkiP0YfrcugoyY" target="_blank" rel="noopener noreferrer"><strong>Watch Metademy Demo</strong></a>
  &nbsp;|&nbsp;
  <a href="https://www.youtube.com/@metademy/videos" target="_blank" rel="noopener noreferrer"><strong>Metademy YouTube Channel</strong></a>
</p>

| Metric | Value |
| --- | --- |
| Issues reported | 150+ |
| AR/XR training modules validated | 40+ |
| Platform user growth | +20% |
| B2B content partners | 11 -> 31 (+200%) |
| Active B2B contracts retained | 28 |

## Background

RaonSecure is a South Korean security and authentication company serving enterprise and public-sector clients, with approximately $44M USD in 2025 revenue. Its education platform, *Metademy*, required QA coverage across gameplay-like client behavior, account integrity, content access, and cross-build consistency.

<table>
  <tr>
    <td width="50%" align="center">
      <img src="./assets/Metademy%20-%20drone.png" alt="Metademy drone training module" />
    </td>
    <td width="50%" align="center">
      <img src="./assets/Metademy%20-%20boat.png" alt="Metademy boat training module" />
    </td>
  </tr>
</table>

<p align="center">
  <sub>Examples of AR/XR training modules validated during pre-launch QA.</sub>
</p>

I joined after the project pivoted from Unity to Unreal Engine, which forced a ground-up rebuild under a compressed launch timeline. My role was to validate the Unreal Client, WebView, and Launcher, clarify shifting requirements with PMs, build reusable test cases, and surface client crashes, login failures, infinite-loading paths, and authorization risks before enterprise users encountered them.

## Tech Stack

| Stack | Summary |
| --- | --- |
| **Unreal Engine 5 Client** | Tested Metademy scenarios, training content, audio, collision, and level-design behavior in the Unreal Client. |
| **Jira** | Filed, triaged, and verified 150+ issues across Client, WebView, and Launcher with reproduction steps, video, and screenshots. |
| **Zephyr Scale** | Authored and maintained test cases for rebuilt modules, mapping coverage back to acceptance criteria clarified with PMs. |
| **Google Sheets** | Tracked cross-build coverage, regression status, and pre-launch readiness for 40+ training modules. |

## Actions

### QA Execution Highlights

Built reusable coverage across product modules, build targets, training content, and changing requirements.

| Focus | Contribution |
| --- | --- |
| **Test Planning** | Authored and maintained test cases for rebuilt modules, converting ambiguous requirements into testable acceptance criteria. |
| **Build Coverage** | Validated fixes across Unreal Client, WebView, and Launcher, catching regressions that single-target testing would have missed. |
| **Training Content** | Verified 40+ AR/XR training modules across IT, Physical Therapy, Languages, Natural Science, and Job Training content. |
| **Reproduction Quality** | Attached video, screenshots, account state, build version, and clear reproduction steps so engineers could diagnose state-dependent issues quickly. |

<p align="center">
  <img src="./assets/Metademy%20-%20webview.png" width="75%" alt="Metademy WebView interface" />
</p>

<p align="center">
  <sub>WebView - only accessible with South Korea IP.</sub>
</p>

### Defect Discovery and System-Level Highlights

Caught launch-risk defects and escalated recurring inconsistencies as platform-level quality concerns.

| Focus | Contribution |
| --- | --- |
| **AR/XR Content Authorization** | Surfaced a content-access bypass where local installed files could be treated as entitlement, creating a B2B revenue-loss risk. |
| **Authentication and Login** | Identified social-login routing failures, client crashes during failed login, and duplicate-login races. |
| **Client Stability** | Reported reproducible client crashes, launcher double-launch issues, and network-off exception paths. |
| **Data Modeling** | Identified authentication and account-integrity risks caused by allowing multiple users to share the same identifier, then proposed a data-modeling change to PMs. |

## Results

| Metric | Result |
| --- | --- |
| **150+** | Issues reported across Client, WebView, and Launcher, including crashes, login races, infinite-loading paths, and content-authorization risks. |
| **40+** | Training modules validated across enterprise education and job-training categories. |
| **+20%** | Platform user growth supported by stabilized first-run, login, and content-access flows. |
| **11 -> 31** | B2B content partnerships increased by 200% with 40+ validated training modules. |
| **28** | Active B2B contracts retained through reliable validation of AR/XR job-training content. |

## Takeaways

- Under a tight deadline with shifting specifications, QA creates the most leverage by actively aligning with PMs and turning ambiguity into executable test cases. Experiencing that same pressure from the QA side deepened my understanding of the software development cycle.
- When a product spans Client, WebView, and Launcher, strong validation requires deliberate cross-environment exception cases. Features like login can fail at the boundaries even when each individual implementation appears correct.
- Software engineering knowledge, especially around data modeling, state ownership, and authorization boundaries, helped me identify risks that could cause business loss and reduce product stability.
