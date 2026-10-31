# Ice Cubes run: notes for Foldsmith

For Foldsmith's maintainers, not the app's. These sections were in [REPORT.md](REPORT.md) until 2026-10-31 (the
original is in this repository's history, commit 7de0b09). They describe the run of 2026-10-28 with the skill as it
stood then (in duo-patterns, at ccf5c17, merged as 4df33be at 07:46 that morning). [README.md](README.md) lists what the
skill changed in answer; "Since" below marks what was checked against the published skill
(`skills/foldsmith-duo/` in Foldsmith) on 2026-10-31.

## Timing

Local time, 2026-10-28. The capture files named here are in the app's working copy, not published.

| Step | Time | Source |
|---|---|---|
| The run | 07:52–09:51, about 2 h | the log's first line (07:52; the baseline commit is 07:50:59) to the report commit 5213b0e (09:50:58) |
| Static scans, flow written and checked | 07:52–08:15 | the log (08:15 line); the run gives the five-screen, closed-only flow check as 12 min (its output was not kept) |
| Before capture | 08:15:15–08:50:07: 34 min 52 s, about 60 s a cell | `before/capture.txt`, `before/findings.txt` |
| Audit, classification | to 08:53 | commit 447ad11 (08:53:26) |
| Diagnosis, fixes, probes | 08:53–09:24 | commits 761fc95 (09:21:38) and 725ff38 (09:24:01) |
| After capture | 09:24:09–09:47:28: 23 min 19 s | `after/capture.txt`, `after/findings.txt` |
| Report | 09:47–09:51 | the log, commit 5213b0e |

Disk use was not recorded in this run.

## Skill problems the run found

- **Taps are slow on this simulator, and the default tap timeout fails three-tap screens.** Every XCUITest tap waits
  60 s for SpringBoard to idle ("App animations complete notification not received, will attempt to continue"), so
  `capture-corpus-flow.sh`'s default `TAP_TIMEOUT=150` skipped `settings-display` (three taps) in every pose. The
  knob exists in the script, but neither `references/simulator.md` nor `capture-app.sh`'s header mentions it. Used
  `TAP_TIMEOUT=400`. A status-bar override that stops the Wi-Fi "searching" animation did not change the wait (tried,
  then cleared). Consequence: by the run's account the five-screen closed-only flow check took 12 min, and the before
  capture took about 60 s a cell (2092 s for 35 cells), against `simulator.md`'s 27 s.
  Since: the tap runner turns XCTest's system-alert check off (`tools/tap`), and `capture-app.sh`'s header documents
  `TAP_TIMEOUT`.
- **`capture-app.sh` exits 0 and prints "N captures, 0 findings" when a screen was skipped in every pose**; only
  `capture.txt` says so. The flow check read "4 captures, 0 findings" with `settings-display` missing (by the run's
  account; that output was not kept). Since: it exits 3 when a screen or pose was skipped.
- **A crash is invisible to the detectors.** When the app is not running the snapshot fails and `duo-detect` writes
  `n/a`; `findings.txt` counted 2 findings with a crash in the run, and `capture-app.sh` still wrote the Home Screen
  shot to the cell as `before`. The taxonomy has no class for a crash on fold; filed under T7 (state lost), with the
  crash report as evidence (not kept). Since: the taxonomy counts a snapshot taken while the app was not in front as T7
  in that pose. The published detector tables are the run's and were not regenerated, so they still read `n/a` there.
- **"Lost the screen on the way to <pose>" names the next pose, not the one whose round trip lost it.** The skill
  calls the line T7 evidence "on that screen" without saying which cell. Here the loss happened on closing from the
  previous inner pose, so T7 went on that pose (settings: laptop; settings-display: laptop, flat-sideways).
  Since: the line says "lost the screen after <pose>".
- **Probing a tapped screen.** `simulator.md`'s probe (`duo.py launch`, fold, shot, ≈ 25 s) only works for screens
  with no taps, or apps that reopen where they were. For Ice Cubes' deeper screens the run probed with `capture-app.sh`
  on a one-screen scratch flow and `ONLY=…` (4–11 min each by its account, the 60 s taps again).
  Since: `capture-app.sh` takes `SCREEN=` to capture one screen behind taps.
- **The sheet rule assumes `FoldAlignedSplit`.** Only `FoldAlignedSplit` sets `duoBookFoldIsActive`, inside its own
  `NavigationSplitView`. An app whose split is the system tab sidebar (`TabView` `.sidebarAdaptable` with
  `defaultTabBarPlacement(.sidebar)`, as here) has no way to set it without taking the split too. The run factored the
  fold reader into `readsBookFold()` on the window's root. Since: `FoldAlignedSplit.swift` has `readsBookFold()`.
- **No guidance for a `TabView` sidebar as the P1 container.** Neither the pattern page nor the decision table says
  whether the tab sidebar is the "sidebar" of the three-level rule. The run measured that the system aligns it like a
  split view (book: rows to 455.3 pt, content from 495.7 pt; flat sideways: sidebar bar 0–280 pt) and kept it (prefer
  system containers). Since: the P1 page says a `TabView` sidebar is P1 already, and what to add.
- **The pages disagree on what a feed is.** `even-grid.md` calls "a timeline or chat" continuous content;
  `reflowing-columns.md` calls "a feed, search results" peer content; the decision table's I1 lists "feed" under peer
  and "feeds-with-detail" under hierarchical. The run classified the timeline hierarchical (posts open into their
  thread). Since: I1 says what a feed is by its items, and names this timeline as hierarchical.
- **The T10 scan has a false positive.** The multi-line pattern `ToolbarItem[^{]*\{` matches a type named
  `ToolbarItems(` and runs on to the next `{`, an alert's `Button("OK")` (`StatusKit/Editor/MainView.swift:100`).
  Since: the scan matches the whole word, and says an app's own `ToolbarItems` type is not one.

## Where the skill's references and the SDK differed

- `FoldAlignedSplit.swift` used `presentationPlacement(.trailing)` behind `#available(iOS 27.1, *)` only. The 27.1
  SDK declares `PresentationPlacement.trailing` `@available(visionOS, unavailable)` and `@available(macOS,
  unavailable)`, so copied as is into a package that also builds for visionOS (Ice Cubes' DesignSystem) it does not
  compile there. The copy in the app is wrapped in `#if os(iOS) && !targetEnvironment(macCatalyst)`.
  Since: the published `FoldAlignedSplit.swift` carries the same `#if`.
- `reservedRegions(kind:options:layoutDirectionBehavior:)`, `ReservedRegion.isActive`, `.includeInactive`,
  `toolbarVerticalEdge` and `presentationPlacement(_:)` matched the SDK as `references/apple.md` named them (the run's
  check; not repeated here).

## How the run checked itself

The run's own look at every after shot, the detectors, and, before the after capture, a logging build and probes whose
output was not kept (log lines at 09:21 and 09:24 in [migration.json](migration.json)). The skill had no independent
review step then, and none ran during the run.

An independent review followed on 2026-10-31 ([README.md](README.md), Independent review; [REPORT.md](REPORT.md), Known
misses): 7 items confirmed, none a regression. Two bear on the skill. The decision table's exception for a local change
that runs on every device (`references/decision-table.md`, Local) cites this run's logged-out tabs, made plain `Tab`s
"as on iPhone", as putting the app on "the path it already takes on iPhone"; at the base a logged-out iPhone used the
same sidebar-only section, and by the code the change swaps the two tabs on every logged-out iPhone's bar, so the
example does not hold (not yet changed in Foldsmith). And 8 cells the run recorded `unchanged` did change, the tab swap
on the cover: the cell states record findings, not changes.
