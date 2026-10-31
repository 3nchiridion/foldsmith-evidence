# NetNewsWire run — notes for Foldsmith

For Foldsmith's own team, not for the app's maintainers: how long the run took, what it found wrong in the skill's
references and detectors, and what the corrections of 2026-10-31 to [REPORT.md](REPORT.md) say about the skill. Moved
out of REPORT.md on 2026-10-31 (the Timing table and the section "Where the skill's references and the SDK differ"),
checked against [migration.json](migration.json)'s log and the run's working copy, and completed where the original
left things out.

## When things happened
Local time, from migration.json's log.
- **First run, 11:16–13:00** (1 h 44 min by the log): flows written at 11:19; flow check 11:25; audit capture
  11:25–12:04; audit, classification and lookup 12:04; migration 12:11–12:20 (article, timeline, settings, Add Feed,
  and the `show(.primary)` attempt for feeds, reverted at 12:14); proof capture ending 13:00; report and `phase done`
  at 13:00, with feeds in `todo` (T7).
- **Follow-up, same day**: the log resumes at 19:29 with a full after pass (35 shots, 19:29–19:51), then a second full
  after pass (20:34–20:48), and marks feeds done at 20:49. The log does not say when the follow-up's code work began,
  nor why the after capture was taken twice; the original report mentions only the second pass. The shots of the first
  run's proof capture and of the 19:29 pass were overwritten by the 20:34 pass.

## Timing
| Step | Time | Source |
|---|---|---|
| Unmodified build (iOS 27.1 SDK) | 59 s | log, 11:19 |
| Tap runner (`xcodegen` + `build-for-testing`) | 9 s | log, 11:19 |
| Detectors' first `swift build` | ≈ 10 s | the original report only; not in the log |
| Flow check, 5 screens, closed only | 339 s | log, 11:25 |
| Audit capture, 35 cells | 2274 s (≈ 65 s a cell) | log, 12:04; the before shots are stamped 11:26:12–12:03:05 |
| Incremental build per migration step | 13–18 s | the five builds in the log (the original report said 12–19 s) |
| Probe with `capture-app.sh`, 2 cells | 130 s, 144 s | log, 12:17 and 12:20 |
| Probe with `duo.py`, direct launch | ≈ 25 s a shot | the original report only; not in the log |
| Proof capture, 35 cells | 2277 s | log, 13:00 only: its shots were overwritten |
| Follow-up after pass, 35 cells | 19:29–19:51 by the log | not in the original report; its shots were overwritten |
| Follow-up recapture, 35 cells, one test run per screen | 915 s (≈ 26 s a cell) | log, 20:49; `capture.txt` starts 20:33:31, the last shot is stamped 20:48:45; no screen lost |

The README's "about 76 of the run's minutes" for captures is the audit and the proof capture (2274 s + 2277 s); the
follow-up's two after passes come on top. Disk use was not recorded for this run. The run's log records no review round
(Foldsmith's [DEVIATIONS.md](https://github.com/3nchiridion/foldsmith/blob/main/docs/history/DEVIATIONS.md) mentions the
run's "review" without saying what it covered): the skill's step 6 now asks for a fresh subagent's review of the diff
and the captures, a requirement written after this run (it cites the Wikipedia run, which came later).

## The skill's references against the SDK and the app
As the run found them on 2026-10-27, with what the skill as published says now.
- The Fold-aligned split (P1) reference was SwiftUI only (`NavigationSplitView`); NetNewsWire is a UIKit three-column
  `UISplitViewController`. The rule carried over (no column width, so the system aligns the boundary with the fold),
  measured here for the supplementary/secondary boundary. Now: the P1 page has a UIKit section measured on this run.
- The P1 page's upright row ("the detail, 0–669; the sidebar one tap away") was the SwiftUI reference's shortfall: a
  UIKit split tiles at 669 pt once `minimumSecondaryColumnWidth` is lowered, as the decision table's cell says. Now:
  `references/decision-table.md` records this as closed, for UIKit (this run) and for SwiftUI (`FoldAlignedTiledSplit`).
- UIKit has no `presentationPlacement`; the 27.0 SDK's `UISheetPresentationController.preferredPlacement` does the
  same. UIKit has no callback when the reserved regions change; layout plus `UIHingeInteraction` was enough. Now: both
  are in the P1 page's UIKit section.
- `references/detectors-static.md`: the T13 line `rg -L 'UIApplicationSceneManifest'` listed matches (`-L` is
  `--follow` in ripgrep), not files without one; the T14 scan missed `…SupplementaryColumnWidth`; the T10 scan missed
  multi-line `ToolbarItem` and `Button(NSLocalizedString(…))`. Now: the T10 row covers both and custom-view items, the
  T14 row names `SupplementaryColumnWidth`, and T13 is three scans read together.
- The run's detectors found 3 of the 14 cells with a finding. They exempted the empty web view's "No selection" as
  scrolling content, did not measure a pane with no text, and had no rule for T7 or for T10 at runtime; the run found
  the other 11 by eye. Now (taxonomy.md): a scrolling container holding one short item is a placeholder for T1;
  `PaneBoundaryOffFold` reads a split's panes from their navigation bars; `StateLossOnFold` (T7) and
  `StrandedHorizontalItem` (T10) exist. The published [detectors-before.md](detectors-before.md) and
  [detectors-after.md](detectors-after.md) come from that version (Phase 4 step 5).
- Missed by the run and by the step-5 detectors: the article's Reader View button (`UIBarButtonItem(customView:)`),
  outside the vertical bar in 8 cells before and after. The T10 detector of Phase 4 step 9 finds it. Not fixed.

## What the corrections of 2026-10-31 say about the skill
- The follow-up edited the report by hand instead of regenerating it (`2b2e1dc` adds 15 lines to it and removes 9): the
  feeds table kept "finding (T7)" while migration.json said `verified`, and the feeds commit was never cited. Tables,
  counts and commits should come from migration.json and git.
- "Verified in all seven poses" was written for screens with 1, 3 and 5 verified cells. The report should use the
  progress file's states as its words.
- The published detector tables were replaced by a later detector version while the report kept the run's count, and
  nothing in the folder said which version made which table. Each table should name its detector version.
- The report's claim that nothing changes off the fold was not checked against the diff: the root split's `present`
  override, `updateSheetPlacement` and the hinge interaction run on every iOS 27.1 device, and the column widths are
  applied at a different point in `init` on every device.
- The cost of two panes upright (Previous Article, Next Article and Share leave the article's bars; More appears) is in
  the after snapshots, but no step asked for it then. Step 6 now does: measure the controls in each changed pose, say
  where each moved control went, and count each extra tap as a cost.
- Step 7 asks for "the before/after shots per pose", and the report gave 70 paths into `.duo/captures/`, which is kept
  out of git and out of this repository; a visitor could open none of them.
- The published copies leave out the simulator's UDID (`<Duo simulator UDID>` in REPORT.md and migration.json, and
  dropped from the log's first line); the working copy's `capture.txt` files, which are not published, still carry it.
