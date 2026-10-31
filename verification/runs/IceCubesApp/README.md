# IceCubesApp, migrated hands-free (2026-10-28)

*Edited on 2026-10-31: the result below now says what was covered and what was not run, and an independent review is
recorded; `REPORT.md` lists its corrections, and the earlier text is in git history.*

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
| `REPORT.md` | the skill's report, for the app's maintainers: at a glance, terms, corrections (2026-10-31), per screen what was wrong and what changed, what was not verified, the known misses an independent review confirmed (2026-10-31), what is left, and the decisions left to the app's owner |
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
were not run, and no device but the Duo simulator was, though the logged-out tab change reaches every device, iPhones
included. An independent review on 2026-10-31 (below) confirmed 7 items, none of them a regression.

Spot-checked by eye after the run (2026-10-28): Add Account on the trailing half in book; Settings in front in tent and
both flat poses; the post in flat sideways; the tab sidebar ending at the fold in book.

## Independent review (2026-10-31)

After the run, three Claude Code agents with no part in it reviewed the branch from the evidence, read-only (nothing
built, no simulator touched). Two worked blind to what the run claimed, reading neither `REPORT.md`, `audit.md`,
`migration.json` nor this repository: a layout reviewer looked at every before and after shot and compared every
accessibility snapshot node by node; a code reviewer read the diff (`8614ae9..duo-migration` without `.duo/`), the
source at both revisions and the routes into the touched types. A skeptic then checked each of their items against the
diff, the source, the snapshots and shots, and Foldsmith's rules. One of the reviewers' searches printed a line each
from the run's report and progress file; neither was used. Their notes and scripts are not published.

They raised 15 items (layout 7, code 8). The skeptic confirmed 7, none of them a regression (1 medium, 6 low), and
lowered two (the logged-in risk from high, the upright layout from medium). The other 8 were not kept: 5 repeated a
confirmed item and were merged into it, and 3 were refuted (the sidebar's empty section header going, an improvement;
alerts, which the system moves to the trailing side itself; Add Account presented when the timeline appears, upstream
behaviour on every device and no longer set off by a fold). One part of a confirmed item was refuted too (that the
branch needed a Mac build). Confirmed, with their evidence in `REPORT.md` (Known misses; lines on `duo-migration`):
- Medium, not a regression. Logged in (not run), every opening or closing of the phone swaps the whole tab set, a
  sidebar-only tab selected on the inner display has no tab on the cover, and the path holds what the diff's own
  comment blames for the logged-out crash (`AppView.swift:62-80`, `:148`; `Tabs.swift:183-190`, `:388-394`).
- Low, not a regression. The logged-out tab change has no gate and reaches every device, iPhones included: at the base
  a logged-out iPhone also used the sidebar-only section (base `AppView.swift:63-67`), so the diff's "as on iPhone" does
  not hold. On the cover Timeline and Settings swap places, in 8 `unchanged` cells (`AppView.swift:94-108`; the closed
  and closed-sideways snapshots).
- Low, not a regression. 13 of the 14 sheet sites were never opened, 3 of them reachable logged out, and no captured
  sheet held typed text while it changed sides (only `AppRegistry.swift:245`, Add Account, was captured).
- Low, not a regression. In-app Safari (UIKit, the default browser) and the system share sheet cannot take the sheet
  rule, and neither was captured (`SafariRouter.swift:98-109`; `ShareLink` in `StatusRowActionsView.swift`,
  `StatusRowContextMenu.swift`).
- Low, not a regression. The share extension never reads the fold, so the composer's sheets keep the system's placement
  there, as before (`ShareViewController.swift:30-48`).
- Low, not a regression. Upright, one pane, before and after, in the 12 upright cells of the four tab screens: the
  shortfall the result above already gives, counted there as 11 cells by the run's classification
  (`after/*-{laptop,tent,flat-upright}.json`).
- Low, not a regression. Style, nothing functional: `nonisolated private` against the codebase's order, the new
  environment value outside `Env`, a header citing Foldsmith's corpus (`FoldAwareSheetPlacement.swift:1-15, 21, 72`).

What they found right, without the run's claims to go on: T15 fixed in book only (Add Account's bar from x 8–467.3 to
483.5–942.8 pt, Cancel in the vertical bar, nothing truncated; flat sideways centred; closed, closed sideways, laptop
and tent the same as before); T7 fixed (the post in flat sideways with no sheet over it, Settings shown in both flat
poses, no lost screen or relaunch in the after capture); the tab sidebar's boundary on the fold (0–475.5 pt) and the
narrower leading pane flat; no text cut off, no control lost, nothing new on the fold; every sheet but a preview's
carrying the rule, with the fold read at the window's root; the gating (iOS 27.1, not Catalyst or visionOS, the fold
from reserved regions); no route, restoration path or container changed; `git diff --check` clean and the project file
unchanged.

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
