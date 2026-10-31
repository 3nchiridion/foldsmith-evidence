# NetNewsWire on iPhone Duo — migration report

> Edited on 2026-10-31, after the run: [Corrections](#corrections) lists what changed and why. Notes meant for
> Foldsmith's own team (timing, the skill's references, what the corrections say about the skill) are in
> [RUN-NOTES.md](RUN-NOTES.md).

## At a glance
- **Status: done for the five screens it captured, one known miss left.** The article's Reader View button stays
  outside the vertical bar (T10) in 8 cells, as before the change; found after the run, not fixed. Not a pull request
  ([Project rules](#project-rules)).
- **For an iPhone Duo user:** in book the list and the article each get one half, instead of the article crossing the
  fold; half open or flat upright the list stays beside the article; unfolding from the feeds list keeps it on screen;
  Settings and Add Feed open on the right half in book; on the cover Add Feed's Cancel and Add join the vertical bar.
- **Coverage:** 5 of 5 screens in the flows; of 35 cells, 19 verified (changed and re-checked; 14 had a finding
  before), 16 fine as is, 0 left. Screens outside the flows were not captured.
- **Change:** NetNewsWire d9ec7df; five code commits ([commits.txt](commits.txt)); 5 files, +331 −26
  ([NetNewsWire.patch](NetNewsWire.patch)).
- **Checked by:** the run's detectors (3 findings before, 0 after) and the run's look at every shot (the other 11
  cells). The published [detectors-before.md](detectors-before.md) and [detectors-after.md](detectors-after.md) are a
  later detector version (Foldsmith's Phase 4 step 5): 21 marks in 15 cells before, none after. The run's log records no
  review of the change, independent or human (Foldsmith's
  [DEVIATIONS.md](https://github.com/3nchiridion/foldsmith/blob/main/docs/history/DEVIATIONS.md) mentions the run's
  "review" without saying what it covered). Unit tests not run; no simulator without a fold run
  ([Not checked](#not-checked)).
- **Known miss and cost:** Reader View, above. Upright the article pane is 328 pt; Previous Article, Next Article and
  Share are no longer in its bars, and a More button appears that the run did not open (9 cells).
- **Decisions for NetNewsWire's maintainers:** two panes upright; the feeds list on the timeline column's stack (it
  reaches routes no capture exercised); symbols for Cancel and Add on every iOS 27.1 device
  ([details](#decisions-for-the-maintainers)).

## Glossary
| Term | Meaning |
|---|---|
| iPhone Duo | Apple's folding iPhone (iOS 27.1). The **cover** (outer display) is 466 × 678 pt; the **inner display** is 951 × 669 pt held wide, 669 × 951 pt held tall. |
| pt | points, UIKit's layout unit. |
| fold, division | the band the system reserves over the hinge, read with `UIView.reservedRegions(kind: .division)`: 455.5–495.5 pt across the inner display. **Active** while the phone is part folded (book, laptop, tent), **inactive** when it lies flat. |
| poses | **closed**: shut, cover upright. **closed-sideways**: shut, cover turned. **book**: half open, held like a book (fold vertical). **laptop**: half open, resting like a laptop (fold horizontal). **tent**: as laptop, standing on its edges. **flat-sideways**, **flat-upright**: open flat, wide or tall. |
| vertical bar | on the cover and in book and flat-sideways the system moves navigation-bar and toolbar items into a column at one edge. An item joins it only with a symbol (or a custom view set to allow it); otherwise it stays in a horizontal bar. |
| cell | one screen in one pose: 5 screens × 7 poses = 35. |
| verified, fine as is, left | a cell's state in [migration.json](migration.json): `verified` (changed by the migration, then re-checked with the detectors and by eye), `unchanged` (fine as is: looked at, nothing to fix), `finding` (left; none here). |
| T1 crease-straddle | something that does not scroll sits on the fold. |
| T7 state-loss-on-fold | after unfolding, the screen the person was on is gone (here: the list hidden behind a button). |
| T10 stranded-horizontal-item | a bar item that stays outside the vertical bar. |
| T14 columns-off-fold | split-view panes or columns not aligned with the fold. |
| T15 sheet-in-book | in book, a sheet sits on the leading half, over the list. |
| Fold-aligned split (P1) | Foldsmith's layout pattern for a list and its detail: two panes, one on each side of the fold. With three levels (feeds, timeline, article) the list pane holds the first two. Its **sheet rule**: sheets go to the trailing half while the book fold is active. |
| detectors (`duo-detect`) | Foldsmith's checks: they read each shot's accessibility snapshot and report the classes above. |

## Corrections
Corrected on 2026-10-31 against the run's evidence (migration.json, the after snapshots, the patch). The text as first
published is in this repository's history (commit `7de0b09`). The code, the run and the other evidence files in this
folder are unchanged.
- The headline "All five screens are verified in all seven poses", and "verified in all seven poses" as every screen's
  status: 16 of the 35 cells were fine as is, not fixed, and the Reader View miss was not mentioned. Replaced with
  per-cell counts from migration.json.
- Added: the article's Reader View button stays outside the vertical bar in 8 cells, before and after the change (found
  after the run by the T10 detector of Foldsmith's Phase 4 step 9; the README already said so).
- Added: the cost of two panes upright (a 328 pt article pane; Previous Article, Next Article and Share no longer in its
  bars), read from the after snapshots.
- The feeds per-pose table still said "finding (T7)" in five poses after the follow-up. The grid below is built from
  migration.json, where those cells are verified. The feeds commit `2b2e1dc` is now listed with the others.
- "Before 3 findings": kept as the run's own detector count, now set beside the published tables, which come from a
  later detector version (21 marks in 15 cells).
- "Unchanged by construction — every change is gated on a division region": not true as worded.
  [Devices without a fold](#devices-without-a-fold) lists what runs on every iOS 27.1 device and on every device.
- "Timeline to 455.5, article from 495.5": those are the fold's own edges; the measured edges are given instead.
- "The owner" in the follow-up is the Foldsmith project's owner, not NetNewsWire's.
- The 70 shot paths of the per-pose tables, which do not exist in this folder, are replaced by one grid and the path
  pattern, and the files that are here are linked. `AddFolderView.swift:60-70` is corrected to `59-78`.
- Add Folder is not presented through the root split, unlike the inspectors: the original said all were, then, in a
  parenthesis, that Add Folder is presented from `MainFeedCollectionViewController`. On the cover Add Feed's snapshots
  differ before and after (the original said the cover shots match), and Add Feed's four cells left "fine as is" were
  altered too (Cancel and Add became symbols there).
- Timing and the notes on the skill's references moved to RUN-NOTES.md. Added: this summary, the glossary, the project's
  contribution rules, decisions for the maintainers and a list of what was not checked.

## Project rules
NetNewsWire's `CONTRIBUTING.md` asks contributors to ask first, with a ticket and a topic on the project's forum,
before working on a change, and says a pull request opened without that is ignored or closed. It also asks that a PR
note any LLM use, that its author review every line, and that it add no warnings. This report is not a PR, and nothing
was sent to the project. The code was written by a Claude Code agent with the Foldsmith skill: six of the branch's seven
commits carry a `Co-Authored-By` line naming the model; the feeds commit `2b2e1dc` has no message body. The run's log
records no warning count.

## Screens
Cell states from [migration.json](migration.json); the R and M marks from the after snapshots.

| Screen | closed | closed-sideways | book | laptop | tent | flat-sideways | flat-upright |
|---|---|---|---|---|---|---|---|
| feeds | = | = | ✓ T14 T7, R | ✓ T1 T7, M | ✓ T1 T7, M | ✓ T7, R | ✓ T7, M |
| timeline | = | = | ✓ T14, R | ✓ T1 T7, M | ✓ T1 T7, M | ✓, R | ✓ T7, M |
| article | =, R | =, R | ✓ T14, R | ✓, M | ✓, M | ✓, R | ✓, M |
| settings | = | = | ✓ T15 | = | = | = | = |
| add-feed | ✓ T10 | ✓ T10 | ✓ T15 ᵃ | = ᵇ | = ᵇ | = ᵃ ᵇ | = ᵇ |

- **✓** verified; the classes after it are what the run found there before (a ✓ with none: the change altered the
  cell, which was then re-checked). **=** fine as is.
- **R**: the article's Reader View button sits outside the vertical bar (T10), before and after alike. Not fixed.
- **M**: two panes upright: the article pane is 328 pt wide, and Previous Article, Next Article and Share are no longer
  in its bars (in the feeds and timeline cells no article is open, so they were disabled before as well); its navigation
  bar shows a More button, which the run did not open.
- **ᵃ**: the later detectors also mark T10 on Add Feed here before the change, which the run's audit missed; after the
  change Cancel and Add are symbols and detectors-after.md is clean here.
- **ᵇ**: "fine as is" in migration.json, although the change altered the cell: Cancel and Add became 40 × 40 pt symbols
  in the sheet's own horizontal bar.

Per screen, measured in the accessibility snapshots (before → after):
- **feeds** (Fold-aligned split, three levels). Unfolding from the feeds list showed an empty timeline beside "No
  selection" (book, flat-sideways) or "No selection" alone (laptop, tent, flat-upright), with the feeds list behind the
  sidebar button ([audit.md](audit.md)). Now the feeds list is in the list pane beside the article's empty page in
  every inner pose: 0–474.7 | 475.7–951 in book, 0–340 | 341–951 flat-sideways, 0–340 | 341–669 upright.
- **timeline** and **article** (Fold-aligned split; one split view serves both). Book: timeline 0–320 and article
  321–951, across the fold → the panes meet in the middle of the fold (474.7 | 475.7); the timeline's rows end at 454.7
  and the article's text starts at 514, so neither crosses 455.5–495.5. The article pane is 475 pt wide instead of 630.
  Flat-sideways: 0–320 | 321–951 → 0–340 | 341–951. Upright: the article alone at 0–669, the list behind the sidebar
  button → the list at 0–340 beside the article at 341–669. The empty page's "No selection" was at y 444–469 in laptop,
  on the fold; now at y 286–311.
- **settings** (the sheet rule). Book: the sheet at x 8, over the list → x 483.5 (459.3 pt wide), with Done in the
  vertical bar. The flow opens Settings on the cover, so this is also the sheet following the fold while it is up. In
  the other poses the sheet's frame and Done are the same before and after.
- **add-feed** (the sheet rule, and symbols). Book: the sheet at x 483.5 as Settings, with Cancel and Add in the
  vertical bar. Cover: Cancel and Add were words in a horizontal bar; now 38 × 38 pt symbols in the vertical bar,
  at x 399 (closed) and x 29 (closed-sideways). In laptop, tent and both flat poses they are 40 × 40 pt symbols in the
  sheet's own horizontal bar (ᵇ).
- On the cover the app collapses to its navigation stack as before: the closed and closed-sideways snapshots have the
  same bars and buttons before and after, except Add Feed's Cancel and Add; only the lists' and the article's contents
  differ. The after capture (the follow-up's, 35 cells) shuts the phone again before each inner pose, and its log
  records no screen lost on the way.

The shots and snapshots are not published, as for every run. In the run's copy they are
`.duo/captures/NetNewsWire/{before,after}/<screen>-<pose>-cover.png` (closed poses) or `…-inner.png` (the others), each
with `<screen>-<pose>.json` beside it; migration.json names every shot.

## What changed
One commit per screen, then the feeds follow-up. Line numbers point into [NetNewsWire.patch](NetNewsWire.patch).
- `iOS/FoldAlignedSplit.swift` (new, lines 110–188): Fold-aligned split for a UIKit split view. The fold is read from
  `UIView.reservedRegions(kind: .division, options: .includeInactive)`, gated `#available(iOS 27.1, *)`; nothing reads
  the device, idiom or orientation (the one `userInterfaceIdiom` in the diff, line 249, is an unchanged context line).
- **article** (`67b5cc7`): on a display with a fold `SceneCoordinator` sets no column width (primary and supplementary
  minimum, maximum and preferred → `automaticDimension`) and saves no widths there; elsewhere the saved widths apply as
  before (lines 273–359, 444–458). The system then puts the timeline/article boundary on the fold in book and keeps the
  leading pane narrower when flat (340 pt). A 320 pt `minimumSecondaryColumnWidth` (iOS 26 API) lets the timeline tile
  beside the article on the upright inner display, as the decision table's cell for this kind of screen asks.
  `RootSplitViewController` re-reads the fold on every layout and on `UIHingeInteraction` updates (lines 202–216,
  223–231).
- **timeline** (`ebe206a`): on a display with a fold the empty article page's "No selection" sits at 25 % of the
  pane's height instead of 45 %, which was on the fold in laptop and tent. The page re-renders when the window gains or
  loses the fold (lines 69–109, 179–182).
- **settings** (`936019c`): every sheet presented through the root split gets
  `sheetPresentationController.preferredPlacement = .trailing` while a vertical division is active and `.automatic`
  otherwise, and sheets already up follow the fold (lines 145–177, 218–221, 232).
- **add-feed** (`2b5f2a9`): the sheet rule above, and title + symbol items on iOS 27.1 (Cancel → `xmark`, Add →
  `checkmark`) so they join the vertical bar on the cover and on the trailing sheet; earlier systems keep the text
  buttons (lines 5–68).
- **feeds** (`2b2e1dc`, a follow-up the same day; it reaches routes no capture exercised, decision 2). The Foldsmith
  project's owner chose, for three levels: unfolded from the feeds list, the two panes are the feeds list and the
  article. While the split is expanded on a display with a fold, the feeds list moves onto the timeline column's
  navigation stack and the primary column's stack holds an empty controller; a picked feed pushes the timeline onto that
  stack and Back returns to the feeds (lines 361–438). A triple-column split pairs only the timeline with the article in
  its two-pane mode, and a zero-width timeline column fails a UIKit assertion, hence the stack move. The stacks go back
  in `splitViewController(_:topColumnForCollapsingToProposedTopColumn:)`, which then answers from the app's state (lines
  463–471); `showColumn` (lines 242–246), `navigationController(_:didShow:)` (lines 499–507) and the saved display mode
  (lines 478–485) have new branches for it.
- Tried and reverted (by the run's account; none was committed): bringing the feeds list up with `show(.primary)`, which
  showed it as an overlay over a dimmed timeline and article; moving the controllers between columns with
  `setViewController(_:for:)`, which popped the cover to the feeds list at its next rotation; hooking
  `viewWillTransition`, which hung the app with Add Feed up.
- API notes: UIKit has no `presentationPlacement` (the SwiftUI modifier); the 27.0 SDK's
  `UISheetPresentationController.preferredPlacement` (`.automatic`, `.leading`, `.center`, `.trailing`) does the same.
  UIKit has no callback when the reserved regions change; layout plus `UIHingeInteraction` was enough on the simulator.

## Devices without a fold
Off the fold the layout should be as before, but not every change is gated on the fold, and no simulator without a fold
was run (this run could use only the Duo simulator).
- **Gated on a fold** (a division, active or not, on iOS 27.1): the column widths, the 320 pt article minimum, the feeds
  list on the timeline column's stack, the placeholder at 25 %, not saving widths. On the Duo this includes
  flat-sideways and flat-upright, where the fold is inactive.
- **Runs on every iOS 27.1 device** (iPhone, iPad): a `UIHingeInteraction` on the root split (lines 202–211); the root
  split's new `present` override, which sets `preferredPlacement` on every sheet presented through it, `.automatic`
  where there is no book fold, the documented default (lines 218–221); `updateSheetPlacement`, which on every layout
  pass of the root split walks the presented sheets and sets `.automatic` on any that has another placement (lines
  163–177, 232); Add Feed's Cancel and Add as symbols instead of words (their titles stay their accessibility labels).
- **Runs on every device and system**: `SceneCoordinator.init` applies the column widths after `super.init()` and
  after `preferredSplitBehavior = .tile`, not before (line 297). Without a fold it sets the same widths as before, plus
  `minimumSecondaryColumnWidth` (iOS 26 and later) and, when no sidebar width is saved, `preferredPrimaryColumnWidth`,
  both to `automaticDimension`, their documented defaults (lines 339–358). `WebViewController` observes a new
  notification that is posted only when the window gains or loses a fold.
- **Mac**: the Mac app is the AppKit target, which compiles `Mac/` and `Shared/`; every changed file is in `iOS/`
  (checked in the Xcode project), so its source is unchanged.

## Decisions for the maintainers
1. **Two panes upright** (`67b5cc7`, the 320 pt `minimumSecondaryColumnWidth`). Laptop, tent and flat-upright show the
   list (0–340) beside a 328 pt article; Previous Article, Next Article and Share leave the article's bars. Without the
   minimum the article is alone there at 669 pt, as before (the run's probe after the column-width change), and
   unfolding from the timeline upright hides the list again.
2. **The feeds list on the timeline column's stack** (`2b2e1dc`). Review this first. It changes two navigation stacks
   under the split while it is expanded on the inner display, and it reaches routes no capture exercised: state
   restoration, keyboard commands and other callers of `showColumn`, and `SceneCoordinator.handle(_:)` for user
   activities and notification responses. Without the commit, feeds keeps the first run's result: T7 in the five inner
   poses, the feeds list one tap away behind the sidebar button.
3. **Symbols for Add Feed's Cancel and Add on every iOS 27.1 device** (`2b5f2a9`), iPhone and iPad included. The
   alternatives are the words, which stay in a horizontal bar on the cover (T10), or symbols only where the bar is
   vertical; the skill's step 5, as published now, asks for that gating, which this change predates.
4. **Reader View** (`ArticleViewController.swift:144`, `UIBarButtonItem(customView:)`): not fixed. Either
   `axisBehavior = .verticalPreferred` on that item (iOS 27.1) or a title + symbol item would let it join the vertical
   bar; neither was tried.

## Not checked
- The app's unit tests (`./buildscripts/build_and_test.sh`, as the app's `CLAUDE.md` gives it). Build warnings were not
  counted.
- Any simulator without a fold (iPhone, iPad); the Mac app was not built.
- Taps on the inner display: the captures reach each screen with taps on the shut phone and then unfold it, so picking
  a feed in the list pane, Back to the feeds and the More button were never exercised.
- Launching with the phone already open: every capture launched on the cover. The P1 page's UIKit section, published
  after the run, warns that a detail minimum set in `viewDidLayoutSubviews` (as here, through `updateForFold`) left the
  catalog's split as an overlay on first launch in laptop.
- State restoration: the flow starts every launch at the feeds list (`-selectedSidebarItem {} -selectedArticle {}` in
  [NetNewsWire.flow](NetNewsWire.flow)), so reopening the last feed and article was not exercised.
- Sheets and screens outside the flows: Add Folder, the feed and account inspectors, Current Activity, article search,
  the full-screen image. The skill's T10 static scan, as published now, also lists bar items in Account Setup,
  Current Activity, Activity Log and Error Log on the unmodified source.
- Split View's half window (475 × 669 pt): not drivable headless.
- The detector rerun's figure of 26 findings (README): the list behind it is not published; only the table's 21 marks
  in 15 cells can be checked here.

## Left to do
- The Reader View button (decision 4).
- Capture the sheets and screens listed under Not checked. The inspectors and Current Activity are presented through
  the root split, so they get the sheet rule in code; none was captured. Add Folder (`AddFolderView.swift:59-78`) has
  the text-only Cancel and Add that Add Feed had (T10 by static scan); AddFeedView's `cancelButton` and `addButton`
  would carry over. Add Folder is presented from `MainFeedCollectionViewController` (`SceneCoordinator.showAddFolder()`),
  not through the root split, so the `present` override never places it; only the update that runs when the root split
  lays out again (on a fold change, for one) does.
- Split View's half window, when it can be driven.

## Reproduce
- The work is branch `duo-migration`, cut from `duo-baseline` (d9ec7df with its secrets generated without keys, by the
  baseline commit's message), in a copy of the app outside this repository.
- The patch is under NetNewsWire's MIT license ([LICENSE-MIT-NetNewsWire](LICENSE-MIT-NetNewsWire)). It applies cleanly
  to `duo-baseline`'s tree (`git apply --check`, 2026-10-31); applying to d9ec7df itself was not checked.
- Built with Xcode 27.1 against the iOS 27.1 SDK; captured on the iPhone Duo simulator `<Duo simulator UDID>` with the
  skill's `tools/capture-app.sh` and [NetNewsWire.flow](NetNewsWire.flow).
