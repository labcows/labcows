# Quality Assurance for a Console/PC Action Game Launch

**Game QA Portfolio**

**Company Website:** [NewCore Games](https://www.newcoregames.com/)

Validating gameplay, progression, localization, and crash-safety for a single-player action title shipped to Steam and PlayStation 5.

| Metric | Value |
| --- | --- |
| Global purchases at launch | 100K+ |
| Steam user review score | 79 / 100 |
| PS5 user review score | 4.24 / 5.0 |
| Company YoY revenue growth | 252% |

## Company Introduction

NewCore Games is an indie game studio based in South Korea with 200K+ total downloads across two titles. The studio's first title, the mobile game *I Am Zombie*, reached 100K+ downloads. In 2024, NewCore Games launched its first PC/console title, *The Devil Within: Satgat*, which achieved 100K+ global purchases and contributed to 252% year-over-year company-wide revenue growth. The studio is currently developing a new action roguelike, *7 Trials*.

## Background

I joined NewCore Games during the final development phase of *The Devil Within: Satgat*. At that point, the team had completed Stages 1–3 and was building out Stage 4 (도성), the final boss battle (악귀 동백), the ending sequence and credits, and polish work across all earlier content. The launch date was fixed, so the QA workload had to compress around a hard deadline.

My responsibilities included:

1. Running validation passes against every build (Stable editor, Win64 RC, PS5 RC).
2. Surfacing critical bugs that could crash the client or block progression before they reached players.
3. Writing test cases for new Stage 4 content, the final boss fight, and the boss-retry / boss-challenge modes.
4. Sanity-checking balance for boss fights and elite monsters.
5. Proposing UX improvements where systemic inconsistency would hurt the player experience.

## Tech Stack

| Stack | Summary |
| --- | --- |
| **Unreal Engine 5** | Reproduced bugs inside the editor, set up isolated test environments, and used in-editor cheats and inspection tools (level streaming, collision visualization) to root-cause defects. |
| **Jira** | Filed, triaged, and verified ~80 defect tickets with full reproduction steps, attached video, screenshots, save files, and crash dumps; tracked fixes across editor and packaged builds. |
| **Perforce** | Synced specific changelists to verify whether fixes were actually included in a given build before signing off on closure. |
| **Google Sheets** | Wrote and tracked test cases for Stage 4, the final boss fight, and boss-retry / boss-challenge modes. |

## Goals

The team needed to ship *The Devil Within: Satgat* on a fixed Steam and PlayStation 5 launch date without sacrificing the consistency players expect from a paid single-player action game. My goal was to make sure no critical crash, soft-lock, or progression blocker reached the gold build, and to flag the system-level inconsistencies that would otherwise erode player trust after launch.

## Actions

### Testing Coverage Highlights

Reported ~80 issues across five stages, a hub area, boss retry and challenge modes, and a separate Demo build, validating fixes across editor and packaged environments on both target platforms.

| Focus | Contribution |
| --- | --- |
| **Stage coverage** | Filed bugs across all five game areas — 파괴된 도시 (Stage 1), 악귀들린 숲 (Stage 2), 통제구역 21 (Stage 3), 도성 (Stage 4), 최종전 — plus the 미라벌 hub, the 악귀의 씨앗 side mission chain, and 보스 리트라이 / 보스 챌린지 modes. |
| **Build environments** | Verified fixes in Stable editor, Win64 RC builds (RC 82 through RC 186), PS5 RC builds, and the separate `satgat_demo` editor used for the public demo. |
| **Reproduction quality** | Attached step-by-step repro, save files (`.sav`), gameplay video (`.mp4`), screenshots, and packaged crash dumps (`UECC-Windows-*.zip` + `SatGat.log`) on the majority of tickets so engineers could reproduce without back-and-forth. |
| **Localization sweep** | Tested across 8 languages (KR, EN, JP, DE, ES, FR, PT, and others) and reported audio-to-subtitle mismatches, Korean text leaking into English subtitles mid-cutscene, glyph spacing artifacts (`isn' t`, `I' ll`), and item-name truncation. |
| **Regression verification** | Closed-loop verified every fix in both editor and the next RC build before final ticket closure, catching incomplete fixes (PS-5138, PS-5125) where intended merges had not actually landed in the build. |

### Defect Discovery Highlights

Caught issues spanning client crashes, state-machine bugs, progression soft-locks, and visual integrity problems across the new Stage 4 content and the boss-retry systems.

| Focus | Contribution |
| --- | --- |
| **Critical crashes** | Reported reproducible client crashes in 악귀 신만 boss-retry Phase 1 (PS-4780), in skipping PZO-2000 Phase 1 (PS-4618), in artifact-table reload on editor play (PS-4360), and on re-entry to the 관리자 zone after stage clear (PS-4313). Each ticket shipped with the packaged Unreal crash report and `SatGat.log`. |
| **Progression blockers** | Identified flows where the player could no longer advance: dashing into the 영묘 portal soft-locked input (PS-4730), the post-oil-vat sequence froze on a black screen (PS-4268), the 연옥 collision-sequence locked the screen black until process kill (PS-4732), and 도성 wave battles intermittently failed to terminate (PS-5263). |
| **State and reward bugs** | Caught duplicate engram drops in boss-retry (PS-4849), intermittently missing engrams (PS-4941), demo save data not resetting between runs (PS-5389), and NPCs persisting in a stage after it had been cleared (PS-4315). |
| **Background and platform sync** | Authored two structured QA reports (`도성 배경 QA`, `도성 배경 및 플랫폼 QA`) as slide decks cataloguing missing platforms, see-through-wall gaps, and level-streaming desync across 도성 sub-zones A_01 through B_02 (PS-4493, PS-4371), which let the level designers fix the area in one pass rather than chasing individual tickets. |
| **Camera and collision** | Reported camera-pop and collision artifacts caused by mis-sized camera-blocking volumes, dynamic blocking zones not deactivating after expansion unlocked, and removed-enemy residual collision still stopping projectiles (PS-4592, PS-5252, PS-5241, PS-5090). |
| **Audio and cutscene integrity** | Caught the credits BGM cutting before the final card (PS-5247), looping VO lines on phase-2 이충 (PS-4585), credits text misalignment under F11 window resize (PS-5267), and characters disappearing mid-dialogue (PS-4736, PS-5269). |

### System-Level Advocacy Highlights

Where the same root cause produced inconsistent player experience across the game, I raised the issue as a design and consistency concern rather than as a single defect.

| Focus | Contribution |
| --- | --- |
| **Save-point safety as a guaranteed rule** | Proposed applying safety volumes to every save point so the "save points are safe" contract held across stages (PS-5075). The team adopted the change across Stages 1–4 before launch. Full narrative in [Case Study: Quality Assurance.md](Case%20Study:%20Quality%20Assurance.md). |
| **Loading-screen UI consistency** | Made the case for adding illustration assets to the 기력 and 보호막 loading screens to match the existing 집중력 screen, framing it as UI coherence rather than missing-asset bugs (PS-5103). |
| **Reward UI unification across modes** | Flagged that boss-clear and boss-retry reward screens formatted quantities differently (`x1` vs `x01`) and proposed unification before launch (PS-5186). |
| **Skill-cost discoverability** | Recommended surfacing `필요 집중력` in the 검술 skill summary at the same visual hierarchy as `필요 기력`, so cost was readable at decision time rather than buried in detail text (PS-4948). |
| **Cross-language UI robustness** | Reported that DE, JP, EN, ES, FR, and PT all hit the same line-break and truncation problem in a single item description, so the team could address it as a layout fix rather than per-language text edits (PS-5094). |

## Selected Deep Dives

### Save-point safety as a system-level rule, not per-location setup

Most save points appeared safe during normal play, but a few let enemies follow the player into the menu and attack during interaction. Inspection in Unreal Editor showed safety volumes were applied per save point, so the rule depended on manual setup at every location. I escalated this as a consistency issue rather than a single bug, and the team applied safety volumes uniformly before launch. Detailed in the linked case study.

### A 100% reproducible crash from jumping over a pre-boss save point

A pre-boss save point assumed the player would step into its trigger before the boss fight started. By jumping over the trigger, entering the fight, clearing the boss, and returning, I reproduced a 100% client crash. Root cause was a misalignment between boss-clear state and save-progression state. The team fixed it by enlarging the trigger volume — a level-design adjustment rather than a code change — which kept blast radius low this close to launch.

### Catching localization regressions across 8 languages

The English build occasionally rendered Korean subtitle text mid-cutscene, mismatched audio against subtitle, and clipped glyphs (`isn' t`, `I' ll`). I split the issues by root cause — string-table fallback bugs vs font/spacing bugs vs audio-sync bugs — so engineering could fix each class once rather than per-occurrence (PS-4567, PS-4571, PS-4583, PS-4585, PS-4605, PS-4463).

## Results

| Metric | Result | Impact |
| --- | --- | --- |
| **On time** | Validation schedule completed against the fixed launch date with no slip attributable to QA. |
| **100K+** | Global purchases across Steam and PlayStation 5 at launch. |
| **79 / 100** | Steam user review score, above the 80% positive threshold. |
| **4.24 / 5.0** | PlayStation 5 user review score. |
| **252%** | YoY company-wide revenue growth, with *The Devil Within: Satgat* as the primary driver. |
| **+34** | Position climb in 2024 Korean game-studio revenue rankings. |

## Takeaways

- A fixed launch date does not reduce the cost of inconsistency bugs; it raises it. Issues like the save-point safety rule were cheaper to fix as a single systemic change than as dozens of post-launch reports, but only if QA framed them that way before code-freeze.
- Reproduction artifacts (save files, crash dumps, video, exact RC build number) shortened triage substantially. The tickets that closed fastest were the ones engineers could open in their own editor without asking me a single follow-up question.
- Verifying fixes in the packaged RC build, not just the editor, caught at least two cases where merges had not actually landed in the build that was about to ship.
- Cross-language UI bugs are usually one layout bug surfacing in eight languages, not eight separate text bugs. Grouping them by root cause changed how the team scheduled the fix.
