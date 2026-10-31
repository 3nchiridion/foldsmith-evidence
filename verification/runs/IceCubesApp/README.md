# IceCubesApp, migrated hands-free (2026-10-28)

*Edited on 2026-10-31: the result below now says what was covered and what was not run; `REPORT.md` lists its
corrections, and the earlier text is in git history.*

The second run of the hands-free skill (`skills/foldsmith-duo/SKILL.md`), and the first on a SwiftUI app: the
`/duo-start` (now `/foldsmith-start`) prompt and nothing else about the work, run by a fresh Claude Code agent with the
skill, on the project's iPhone Duo simulator (iOS 27.1, Xcode 27.1). The prompt added only this machine's facts (where
Xcode, `duo.py` and the tap runner are; that no Mastodon account may be created) and the project's limits (no simulator
changes; no push; no edits to the project's repository, then duo-patterns; strangers' posts never committed). The app
was IceCubesApp at 98098b9 (`raw/corpus/IceCubesApp`, a clone made by `raw/corpus/clone.sh`, not committed), copied
outside this repository with one baseline commit on `main`; the skill committed its work on `duo-migration` there.
Headless `claude -p`, used for NetNewsWire, was signed out, so the run was a subagent.

| File | What it is |
|---|---|
| `REPORT.md` | the skill's report, for the app's maintainers: at a glance, terms, corrections (2026-10-31), per screen what was wrong and what changed, what was not verified, what is left, and the decisions left to the app's owner |
| `RUN-NOTES.md` | for Foldsmith's maintainers, moved out of `REPORT.md` on 2026-10-31: timing, the skill's problems as the run found them (and what has changed since), where the skill's references and the SDK differed |
| `audit.md` | the audit of the unmodified app and the classification of each screen |
| `migration.json` | the progress file at the end of the run (what the Foldsmith pane reads) |
| `IceCubesApp.flow` | the screens the skill found and the taps that reach them, logged out |
| `detectors-before.md`, `detectors-after.md` | `duo-detect` on the snapshots before (33) and after (35) |
| `IceCubesApp.patch` | the code change, `main..duo-migration` without `.duo/`; **AGPL-3.0**, as IceCubesApp is |
| `LICENSE-AGPL-3.0` | the license of the patch: IceCubesApp's `LICENSE` at 98098b9, unchanged |
| `commits.txt` | the run's four commits |

The shots stay outside: the timeline and the post show strangers' posts. `REPORT.md` gives their path pattern in the
app's working copy.

Result: the five screens the run could reach without an account are done, and the logged-in app was never run. All
five are Fold-aligned split by classification, a split the system's tab sidebar already makes, so the changes were a
fix to the tab structure and the sheet rule. Of the 35 cells (5 screens × 7 poses), 8 had a defect and are fixed and
verified, and 27 had no finding before or after (`unchanged`); upright, every screen shows one pane where the decision
table asks for two (11 of those 27 cells, not fixed). `duo-detect` finds nothing after in any of the 35 (before: 2
findings, the crashed cells reading `n/a`), which meets the corpus part of Foldsmith's gate G3 for these five screens,
with the run's detectors (zero findings after, in every pose;
[`EVAL.md`](https://github.com/3nchiridion/foldsmith/blob/main/EVAL.md) leaves to the owner whether one app suffices
and which detectors count), though the detectors found only 2 of the 8 defects before. The run took about two hours
(the before capture 35 min, the after capture 23 min). Before: T15 on the Add Account sheet in book; T7 on the post
(flat sideways), Settings (laptop) and Display Settings (laptop, flat sideways), where a fold re-presented Add Account
over the screen; and a crash closing the phone from tent with Settings selected, so Settings showed the Home Screen in
both flat poses. By the run's diagnosis (its logging build and crash report were not kept), the cause of the T7s and
the crash was one: the logged-out tabs sat in an untitled sidebar-only `TabSection`, and every change of the vertical
bar rebuilt the tab bar's items. Plain tabs fixed both in the after capture (one pass; a crash on the fold can be
intermittent); the sheet rule (`foldAwareSheetPlacement()` on every sheet, `readsBookFold()` on the window's root)
fixed T15. The timeline needed nothing: the system's tab sidebar already splits at the fold. Screens that need an
account are in `todo`; logged in, the inner display gets titled sidebar-only tab sections and the cover the iPhone
tabs, so every opening or closing of the phone changes the tab set, and every change of the vertical bar makes the tab
bar rebuild (by the run's account): the kind of structure and rebuild that crashed logged out, so it may crash there.
Of the 14 sheet call sites given the sheet rule, one (Add Account) was opened. The project's tests and SwiftFormat
were not run, iPad, Mac and visionOS were not run though the logged-out tab change reaches them, and no independent
review looked at the result.

Spot-checked by eye after the run (2026-10-28): Add Account on the trailing half in book; Settings in front in tent and
both flat poses; the post in flat sideways; the tab sidebar ending at the fold in book.

What the run changed in the skill (in duo-patterns, Phase 4, step 8; carried into Foldsmith), from its "Skill problems"
(now in `RUN-NOTES.md`):
- Taps: XCTest waited 60 s for SpringBoard before every tap (its check for system alerts); the tap runner now turns
  that check off. Settings → Display Settings, closed only: 243 s → 61 s.
- `capture-app.sh` exits 3 and repeats the lines when a screen or pose was skipped, takes `SCREEN=` to probe one screen
  behind taps, and documents `TAP_TIMEOUT`.
- A crash on the fold is a finding: the runner says when the app is not in front, and `duo-detect` reports T7 there.
- The capture's "lost the screen" line names the pose after which it was lost.
- The reference files compile for Mac Catalyst, visionOS and macOS too (no reserved regions in those SDKs;
  `.trailing` unavailable on visionOS and macOS); `FoldAlignedSplit.swift` gains `readsBookFold()`.
- P1 says what to do with a `TabView` sidebar, and the hazard the run found in one; the decision table's I1 says what a
  feed is, and the pages that disagreed now point to it; the T10 scan matches whole words.
