# Quality Assurance for a Console/PC Action Game with 100K+ copies

## Project Summary

Served as QA analyst to successfully launch “[The Devil Within: Satgat](https://store.steampowered.com/app/1802880/The_Devil_Within_Satgat/)” (the company’s first-ever PC/Console title) while collaborating with a team of 10+. Created 2,000+ test cases and executed 10+ full system tests and 20+ build regression test cycles.

| Metric | Value |
| --- | --- |
| Global purchases | 100K+ |
| Steam user review score | 7.9 / 10.0 |
| PS5 user review score | 4.24 / 5.0 |
| Company YoY revenue growth | 252% |

**Company Website:** [NewCore Games](https://www.newcoregames.com/)

## Background

NewCore Games is a South Korean indie game studio with 200K+ total downloads across three titles. In 2024, the studio launched its first PC/console title, *The Devil Within: Satgat*, which reached 100K+ global purchases.

I joined during the final development phase, when Stage 4, the final boss battle, ending sequence, credits, and polish work still had to be validated against a fixed December launch date. My role was to run regression and full-system tests, write test cases for late-stage content, surface crash and progression-blocking defects, and flag system-level UX issues before they reached players.

## Tech Stack

| Stack | Summary |
| --- | --- |
| **Unreal Engine 5** | Reproduced bugs inside the editor, set up isolated test environments, and used in-editor cheats and inspection tools (level streaming, collision visualization, etc.) to identify root-cause defects. |
| **Perforce** | Version control. Synced changelists to verify whether fixes were actually included in a given build before signing off on closure. |
| **Jira** | Filed, triaged, and verified issue tickets with full reproduction steps, attached video, screenshots, save files, and crash dumps; tracked fixes across editor and packaged builds. |
| **Google Sheets** | Wrote and tracked test cases for Stage 4, the final boss fight, boss-retry / boss-challenge modes, and regression tests. |

## Actions

### QA Execution Highlights

Built broad test coverage across content, platforms, languages, and release-candidate builds.

| Focus | Contribution |
| --- | --- |
| **Test planning** | Created 2,000+ test cases for Stage 4, the final boss fight, boss-retry / boss-challenge modes, and full regression passes. |
| **Content coverage** | Tested all five game levels, the hub, side missions, final battle, boss-retry / boss-challenge modes, and the public demo build. |
| **Build validation** | Ran 10+ full system tests and 20+ regression cycles across editor, Win64 release-candidate builds, and the demo editor. |
| **Reproduction quality** | Attached step-by-step reproduction steps, save files, gameplay video, screenshots, crash dumps, and logs so engineers could reproduce issues. |
| **Localization test** | Tested 8 languages and reported subtitle mismatches, fallback text, glyph spacing artifacts, and item-name truncation. |

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
