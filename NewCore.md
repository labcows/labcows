# Quality Assurance for a Console/PC Action Game with 100K+ copies

## Project Summary

Served as QA analyst to successfully launch “[The Devil Within: Satgat](https://store.steampowered.com/app/1802880/The_Devil_Within_Satgat/)” (the company’s first-ever PC/Console title) while collaborating with a team of 10+. Created 2,000+ test cases and executed 10+ full system tests and 20+ build regression test cycles.

<p align="center">
  <img src="./assets/Satgat%20-%20intro.png" width="75%" alt="The Devil Within Satgat gameplay introduction image" />
</p>

| Metric | Value |
| --- | --- |
| Global purchases | 100K+ |
| Steam user review score | 7.9 / 10.0 |
| PS5 user review score | 4.24 / 5.0 |
| Company YoY revenue growth | 252% |

## Background

NewCore Games is a South Korean indie game studio with 200K+ total downloads across three titles. In 2024, the studio launched its first PC/console title, *The Devil Within: Satgat*, which reached 100K+ global purchases.

<table align="center" border="0" cellpadding="6">
  <tr>
    <td width="33%" align="center" valign="middle">
      <img src="./assets/Satgat%20-%20imzombie.png" height="220" alt="I Am Zombie by NewCore Games" />
    </td>
    <td width="33%" align="center" valign="middle">
      <img src="./assets/Satgat%20-%20background.png" height="220" alt="The Devil Within Satgat by NewCore Games" />
    </td>
    <td width="33%" align="center" valign="middle">
      <img src="./assets/Satgat%20-%207trials.png" height="220" alt="7 Trials by NewCore Games" />
    </td>
  </tr>
</table>

<p align="center">
  <sub>Three titles from NewCore Games.</sub>
</p>

## My Role/Responsibilities

I joined during the final development phase, when Stage 4, the final boss battle, ending sequence, credits, and polish work still had to be validated against a fixed December launch date. 

My role was to:
1: Run regression and full-system tests
2: Write test cases for late-stage content, surface crash and progression-blocking defects
3: Flag system-level UX issues before they reached players

## Tech Stack

| Stack | Summary |
| --- | --- |
| **Unreal Engine 5** | Reproduced bugs inside the editor, set up isolated test environments, and used in-editor cheats and inspection tools (level streaming, collision visualization, etc.) to identify root-cause defects. |
| **Perforce** | Version control. Synced changelists to verify whether fixes were actually included in a given build before signing off on closure. |
| **Jira** | Filed, triaged, and verified issue tickets with full reproduction steps, attached video, screenshots, save files, and crash dumps; tracked fixes across editor and packaged builds. |
| **Google Sheets** | Wrote and tracked test cases for Stage 4, the final boss fight, boss-retry / boss-challenge modes, and regression tests. |


## Actions

<p align="center">
  <img src="./assets/Satgat%20-%20skill.png" width="75%" alt="The Devil Within Satgat skill UI image" />
</p>

<p align="center">
  <sub>Example gameplay screenshot</sub>
</p>

### QA Execution Highlights

Built broad test coverage across content, platforms, languages, and release-candidate builds.

| Focus | Contribution |
| --- | --- |
| **Test Planning** | Created 2,000+ test cases for Stage 4, the final boss fight, boss-retry / boss-challenge modes, and full regression passes. |
| **Content Coverage** | Tested all five game levels, the hub, side missions, final battle, boss-retry / boss-challenge modes, and the public demo build. |
| **Build Validation** | Ran 10+ full system tests and 20+ regression cycles across editor, Win64 release-candidate builds, and the demo editor. |
| **Reproduction Quality** | Attached step-by-step reproduction steps, save files, gameplay video, screenshots, crash dumps, and logs so engineers could reproduce issues. |
| **Localization Test** | Tested 8 languages and reported subtitle mismatches, fallback text, glyph spacing artifacts, and item-name truncation. |

<p align="center">
  <img src="./assets/Satgat%20-%20boss.png" width="75%" alt="The Devil Within Satgat gameplay introduction image" />
</p>

<p align="center">
  <sub>Stage 1 Boss Battle</sub>
</p>

### Defect Discovery and System-Level Highlights

Caught launch-risk defects and escalated recurring inconsistencies as system-level quality concerns.

| Focus | Contribution |
| --- | --- |
| **Game Crashes** | Reported reproducible crashes across boss retry, phase transitions, editor reloads, and post-clear re-entry flows. |
| **Progression Blockers** | Identified soft-locks, black-screen sequences, wave-battle termination failures, and state-machine issues that could block completion. |
| **Gameplay Data Integrity** | Caught duplicate or missing rewards, demo save-data reset failures, and NPCs persisting after a stage had been cleared. |
| **Level Design QA** | Authored structured QA reports for missing platforms and meshes, level streaming desync, cameras, and collision artifacts. |
| **UI/UX** | Proposed system-level fixes for save-point safety, loading-screen consistency, reward quantity formatting, skill-cost visibility, and cross-language layout robustness. |


## Results

<p align="center">
  <img src="./assets/Satgat%20-%20review.png" width="75%" alt="The Devil Within Satgat review score image" />
</p>

| Metric | Result |
| --- | --- |
| **On time** | Validation schedule completed against the fixed launch date with no slip attributable to QA. |
| **100K+** | Global purchases across Steam and PlayStation 5 at launch. |
| **7.9 / 10.0** | Steam user review score. |
| **4.24 / 5.0** | PlayStation 5 user review score. |
| **252%** | YoY company-wide revenue growth, with *The Devil Within: Satgat* as the primary driver. |
| **+34** | Position climb in 2024 Korean game-studio revenue rankings. |

## Takeaways

- Reproduction artifacts (detailed steps, save files, crash dumps, video, exact build number) shortened triage substantially. The tickets that closed fastest were the ones engineers could open in their own editor without asking me a single follow-up question.
- Game QA is strongest when it is done from the perspective of a player who paid for the game. Beyond required checks like story, mechanics, graphics, and performance, small friction points can meaningfully improve the final play experience.
- Testing outside the expected scenario and level-design path is essential. Curious players will try unexpected actions, and the game still needs to avoid soft-locks, black screens, and crashes.
- Repetitive validation still requires product ownership. Testing the same feature from different angles, supported by clear reproduction artifacts, helps uncover new bugs, polish gaps, and faster fixes.
