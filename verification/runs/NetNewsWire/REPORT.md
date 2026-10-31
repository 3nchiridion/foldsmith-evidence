# NetNewsWire on iPhone Duo — migration report

> Edited on 2026-10-31, after the run: [Corrections](#corrections) lists what changed and why, including the independent
> review recorded under [Known misses and regressions](#known-misses-and-regressions-independent-review-2026-10-31).
> Notes meant for Foldsmith's own team (timing, the skill's references, what the corrections say about the skill) are
> in [RUN-NOTES.md](RUN-NOTES.md).

## At a glance
- **Status: by the run's own checks, done for the five screens it captured; 9 regressions that an independent review
  confirmed after the run are not fixed.** Two are high: the change does not build with the Xcode 26.6 the project's CI
  uses, and on the inner display Mark All as Read brings up an empty column and closes the open article
  ([Known misses and regressions](#known-misses-and-regressions-independent-review-2026-10-31)). Not a pull request
  ([Project rules](#project-rules)).
- **For an iPhone Duo user:** in book the list and the article each get one half, instead of the article crossing the
  fold; half open or flat upright the list stays beside the article; unfolding from the feeds list keeps it on screen;
  Settings and Add Feed open on the right half in book; on the cover Add Feed's Cancel and Add join the vertical bar.
  What it costs them is under Known regressions, below.
- **Coverage:** 5 of 5 screens in the flows; of 35 cells, 19 verified (changed and re-checked; 14 had a finding
  before), 16 fine as is, 0 left. Screens outside the flows were not captured.
- **Change:** NetNewsWire d9ec7df; five code commits ([commits.txt](commits.txt)); 5 files, +331 −26
  ([NetNewsWire.patch](NetNewsWire.patch)).
- **Checked by:** the run's detectors (3 findings before, 0 after) and the run's look at every shot (the other 11
  cells). The published [detectors-before.md](detectors-before.md) and [detectors-after.md](detectors-after.md) are a
  later detector version (Foldsmith's Phase 4 step 5): 21 marks in 15 cells before, none after. The run's log records no
  review of its own (Foldsmith's
  [DEVIATIONS.md](https://github.com/3nchiridion/foldsmith/blob/main/docs/history/DEVIATIONS.md) mentions the run's
  "review" without saying what it covered). After the run, on 2026-10-31, an independent review: a layout reviewer
  (every shot and snapshot) and a code reviewer (the diff, the source at both revisions, the routes into the touched
  types), working from the evidence and blind to this report, then a skeptic who checked their items; 24 raised, 15
  confirmed, 9 refuted or merged
  ([Known misses and regressions](#known-misses-and-regressions-independent-review-2026-10-31)). Unit tests not run; no
  simulator without a fold run; no build with the CI's Xcode 26.6 ([Not checked](#not-checked)).
- **Known regressions** (from the independent review, none fixed; 6 of the 9 come from the feeds commit `2b2e1dc`): the
  change does not build with the CI's Xcode 26.6 (high); on the inner display Mark All as Read brings up an empty
  column, which stays until the phone is closed, and closes the article (high); upright, in a 328 pt article pane,
  Share has left the pane and Previous Article and Next Article are behind a More no capture opened (the M cells under
  [Screens](#screens)); Back to the feeds closes the open article, and pushes and pops the timeline at once; outside
  routes (Handoff, Spotlight, Siri, notifications, search) may lose the feed they open (plausible); unfolding drops a
  Search All; the edge swipe can bring up the empty column (plausible).
- **Known misses** (not regressions): the article's Reader View button stays outside the vertical bar (T10) in 8 cells,
  as before the change (found after the run, by the later T10 detector and by the review); a launch directly upright
  may still show an overlay (plausible); sheets presented outside the root split, such as Add Folder, skip the sheet
  rule; Add Feed's symbols are not gated on a vertical bar; the "Loading…" page lands on the fold in laptop and tent;
  minor points of the project's coding guidelines.
- **Decisions for NetNewsWire's maintainers:** two panes upright; the feeds list on the timeline column's stack (it
  reaches routes no capture exercised, where the review found 6 regressions); symbols for Cancel and Add on every iOS
  27.1 device; the Xcode the CI uses ([details](#decisions-for-the-maintainers)).

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
- 2026-10-31: added the independent review
  ([Known misses and regressions](#known-misses-and-regressions-independent-review-2026-10-31); At a glance; references
  to it in Project rules, Screens, What changed, Decisions, Not checked and Left to do). The status said one known miss
  was left; it now gives the review's 9 regressions and 6 misses. "The run's log records no review of the change,
  independent or human" now says the run's log records no review of its own, and that one followed. "Back returns to
  the feeds" now adds that, by the code, Back also deselects the feed and closes the open article. Decision 5 (the
  Xcode the CI uses) is new.

## Project rules
NetNewsWire's `CONTRIBUTING.md` asks contributors to ask first, with a ticket and a topic on the project's forum,
before working on a change, and says a pull request opened without that is ignored or closed. It also asks that a PR
note any LLM use, that its author review every line, and that it add no warnings. This report is not a PR, and nothing
was sent to the project. The code was written by a Claude Code agent with the Foldsmith skill: six of the branch's seven
commits carry a `Co-Authored-By` line naming the model; the feeds commit `2b2e1dc` has no message body. The run's log
records no warning count. The independent review found the change short of these rules: it does not build with the
Xcode 26.6 the project's CI selects, against `CONTRIBUTING.md`'s "merge cleanly" and `Technotes/CodingGuidelines.md`'s
"No code that triggers compiler errors" ([review](#known-misses-and-regressions-independent-review-2026-10-31), 1), and
it misses minor points of the coding guidelines (15).

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
  bar shows a More button, which the run did not open. The independent review confirmed both as regressions
  ([review](#known-misses-and-regressions-independent-review-2026-10-31), 3 and 9).
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
  stack and Back returns to the feeds (lines 361–438); by the code, Back also deselects the feed and closes the open
  article ([review](#known-misses-and-regressions-independent-review-2026-10-31), 4). A triple-column split pairs only
  the timeline with the article in its two-pane mode, and a zero-width timeline column fails a UIKit assertion, hence
  the stack move. The stacks go back in `splitViewController(_:topColumnForCollapsingToProposedTopColumn:)`, which then
  answers from the app's state (lines 463–471); `showColumn` (lines 242–246), `navigationController(_:didShow:)` (lines
  499–507) and the saved display mode (lines 478–485) have new branches for it. Six of the independent review's nine
  regressions come from this commit (2, 4–7, 10).
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
   unfolding from the timeline upright hides the list again. The independent review confirmed both costs as
   regressions ([review](#known-misses-and-regressions-independent-review-2026-10-31), 3 and 9): Share has no node in
   the upright article pane, and Previous and Next are behind a More no capture opened, with no `visibilityPriority` to
   choose what stays. It also found the minimum set too late for a launch directly upright (8, plausible).
2. **The feeds list on the timeline column's stack** (`2b2e1dc`). Review this first. It changes two navigation stacks
   under the split while it is expanded on the inner display, and it reaches routes no capture exercised: state
   restoration, keyboard commands and other callers of `showColumn`, and `SceneCoordinator.handle(_:)` for user
   activities and notification responses. Without the commit, feeds keeps the first run's result: T7 in the five inner
   poses, the feeds list one tap away behind the sidebar button. The independent review read these routes
   ([review](#known-misses-and-regressions-independent-review-2026-10-31)). By the code, state restoration, widget deep
   links and notifications through `restoreFeedSelection` work; Mark All as Read (2), Back to the feeds (4, 5), Handoff,
   Spotlight, Siri, notifications that fall back to `discloseFeed` and search (6), a Search All on unfold (7) and the
   edge swipe (10) do not.
3. **Symbols for Add Feed's Cancel and Add on every iOS 27.1 device** (`2b5f2a9`), iPhone and iPad included. The
   alternatives are the words, which stay in a horizontal bar on the cover (T10), or symbols only where the bar is
   vertical; the skill's step 5, as published now, asks for that gating, which this change predates (review, 13).
4. **Reader View** (`ArticleViewController.swift:144`, `UIBarButtonItem(customView:)`): not fixed. Either
   `axisBehavior = .verticalPreferred` on that item (iOS 27.1) or a title + symbol item would let it join the vertical
   bar; neither was tried (review, 11).
5. **The Xcode the project's CI uses** (`67b5cc7`, `936019c`). The change calls iOS 27.0 and 27.1 SDK symbols behind
   runtime `#available` checks only, so the CI's Xcode 26.6 cannot build it (review, 1). Either guard them at compile
   time or move CI to Xcode 27.1 in the same change.

## Not checked
- The app's unit tests (`./buildscripts/build_and_test.sh`, as the app's `CLAUDE.md` gives it). Build warnings were not
  counted. Nor was the change built with the Xcode 26.6 the project's CI selects; by the SDK headers it cannot compile
  there ([review](#known-misses-and-regressions-independent-review-2026-10-31), 1).
- Any simulator without a fold (iPhone, iPad); the Mac app was not built.
- Taps on the inner display: the captures reach each screen with taps on the shut phone and then unfold it, so picking
  a feed in the list pane, Back to the feeds and the More button were never exercised. The independent review read Back
  and Mark All as Read from the code (2, 4, 5); what More holds is still unknown (3, 9).
- Launching with the phone already open: every capture launched on the cover. The P1 page's UIKit section, published
  after the run, warns that a detail minimum set in `viewDidLayoutSubviews` (as here, through `updateForFold`) left the
  catalog's split as an overlay on first launch in laptop. The independent review found that order in this code
  (8, plausible).
- State restoration: the flow starts every launch at the feeds list (`-selectedSidebarItem {} -selectedArticle {}` in
  [NetNewsWire.flow](NetNewsWire.flow)), so reopening the last feed and article was not exercised. By the independent
  review's reading of the code, restoration selects before the first layout, so the list pane gets the feeds and the
  timeline.
- Sheets and screens outside the flows: Add Folder, the feed and account inspectors, Current Activity, article search,
  the full-screen image. The skill's T10 static scan, as published now, also lists bar items in Account Setup,
  Current Activity, Activity Log and Error Log on the unmodified source. By the code, Add Folder and the sheets Settings
  presents itself skip the sheet rule (review, 12).
- Split View's half window (475 × 669 pt): not drivable headless.
- The detector rerun's figure of 26 findings (README): the list behind it is not published; only the table's 21 marks
  in 15 cells can be checked here.

## Known misses and regressions (independent review, 2026-10-31)
Three Claude Code agents with no part in the run reviewed the branch from the evidence and the source: a layout reviewer
(every before and after shot, every accessibility snapshot measured) and a code reviewer (the diff without `.duo/`, the
source at both revisions, the routes `tools/routes.sh` lists into the touched types), neither of whom read this report,
[audit.md](audit.md) or [migration.json](migration.json); then a skeptic, who checked each of their items against the
frames, the lines, the shots, the iOS 27.1 SDK headers and Foldsmith's rules. They raised 24 items: 15 stand, 9 of them
regressions (2 high, 5 medium, 2 low) and 6 not (1 medium, 5 low); 5 repeated one of the 15 and were merged into it; 4
were refuted. [README.md](README.md) lists what they found right and what was refuted. The review's ids are in
parentheses. Lines are in the source at `duo-migration` (base `20a90a6` is `duo-baseline`), not in
[NetNewsWire.patch](NetNewsWire.patch) as in the rest of this report; `before/` and `after/` are the snapshots and shots
in the run's working copy (path pattern under [Screens](#screens)). None of the 15 is fixed.

1. **The change does not build with the project's CI toolchain (C-R1; high; regression).** The "iOS Simulator Tests"
   job (`.github/workflows/ci.yml:75-110`) selects Xcode 26.6. The new code calls iOS 27 SDK symbols:
   `UIView.reservedRegions(kind:options:)` (iOS 27.1, `UIViewReservedRegion.h:18`) at `iOS/FoldAlignedSplit.swift:27`
   and `:35`; `UISheetPresentationController.preferredPlacement` and `.Placement` (iOS 27.0,
   `UISheetPresentationController.h:40`, `:120`) at `:43`, `:52`, `:55`, `:57`; `UIHingeInteraction` (iOS 27.1,
   `UIHingeInteraction.h:51`) at `iOS/RootSplitViewController.swift:38`. Only the runtime `#available` guards them;
   nothing guards them at compile time, so the 26.6 SDK cannot build the iOS target. The base built with 26.6, and the
   diff touches neither `ci.yml` nor `project.pbxproj`. This breaks `CONTRIBUTING.md` ("merge cleanly", "add no
   warnings") and `Technotes/CodingGuidelines.md:149` ("No code that triggers compiler errors"). Read from the CI file
   and the SDK headers, not from a build. Fix: guard at compile time, or move CI to Xcode 27.1 in the same change
   (decision 5).
2. **Mark All as Read brings up an empty column and closes the article (C-R2; high; regression).** Book, laptop, tent,
   flat-sideways and flat-upright (feeds, timeline, article). `markAsReadAndShowSidebar` still sets
   `preferredDisplayMode = .twoBesideSecondary` and calls `showColumn(.primary, bypassDisplayModeRestriction: true)`
   (`iOS/SceneCoordinator.swift:1536-1541`). While the feeds list sits in the list pane, the primary column's stack
   holds only `emptyPrimaryViewController` (`:886`, `:907`), so an empty column appears beside the list pane and the
   article in book and flat-sideways, or over them upright, where three columns do not fit. It stays until the phone is
   closed: `.oneBesideSecondary` and `displayModeButtonVisibility = .never` are set only at `:909-910`, and
   `updateSidebarBesideArticle` returns early while `isSidebarBesideArticle` is true (`:894`). The same call pops the
   list pane to the feeds list (`showInSidebarBesideArticle`), whose `didShow` deselects the feed and the open article
   (`:1969-1971`). Before, the same call showed the feeds sidebar. Triggers: the timeline toolbar's Mark All as Read
   (`MainTimelineViewController.swift:501`), ⌘K in the timeline (`:514`) and in the feeds
   (`MainFeedCollectionViewController.swift:634`), and mark all as read and go to the next unread
   (`RootSplitViewController.swift:144`). The button is on screen beside the article in every inner pose:
   `after/article-book.json` node 146 at (25, 602), `after/article-laptop.json` node 161 at (25, 884). Read from the
   code; no capture runs it.
3. **Upright, Share has left the article pane (L-L2; medium; regression).** Laptop, tent and flat-upright, article: no
   node for Share in `after/article-laptop.json`, `after/article-tent.json` or `after/article-flat-upright.json`.
   Before, it was enabled at x 603.3–644.0, y 884–922, beside Reader View at x 545.3–589.3 (`before/article-laptop.json`
   node 103; tent node 86; flat-upright node 115). After, the bottom bar ends with Reader View at x 600–644
   (`after/article-laptop.json` node 190), with no overflow of its own; the shot `after/article-laptop-inner.png` shows
   only ○ ☆ ⌄ and Reader View under the article. The items come from `Main.storyboard` (Share `9Ut-5B-JKP`), with
   Reader View inserted at index 5 (`iOS/Article/ArticleViewController.swift:146-147`), and the diff sets no
   `visibilityPriority`. Whether Share went into the top More is unverified: no capture opens it. The timeline row's
   context menu still offers Share (`MainTimelineViewController.swift:602`).
4. **Back to the feeds closes the open article (L-L4; medium; regression).** Book, laptop, tent, flat-sideways and
   flat-upright, timeline or article: the timeline's Show Sidebar button became a Back button, "Feeds"
   (`before/article-book.json` node 10, Show Sidebar, x 24–64; `after/article-book.json` node 10, a BackButton, x
   20–68, and the same in `after/article-{laptop,tent,flat-sideways,flat-upright}.json`). Going Back deselects the feed
   and closes the open article, so the detail pane empties to "No selection": `didShow` of the feeds list while
   `isSidebarBesideArticle` is true calls `selectFeed(nil, …)` (`iOS/SceneCoordinator.swift:1969-1971`), which
   deselects the article (`:1248-1250`) and sets `articleViewController.article = nil` (`:1321-1324`). Getting it back
   takes a feed and an article picked again. Before, Show Sidebar changed only the display mode and kept the timeline
   and the article. Read from the code; no capture taps Back.
5. **Back pushes and pops the timeline in one turn (C-R3; medium; regression).** The same poses, with an article open:
   going Back from the timeline to the feeds list pushes the timeline back and pops it again in the same run-loop turn.
   The new `didShow` branch (`iOS/SceneCoordinator.swift:1969-1971`) calls `selectFeed(nil)`; `selectSidebarItem` runs
   `selectArticle(nil)` (`:1248-1250`), which, with an article open, calls `showColumn(.supplementary)` (`:1321-1324`)
   → `RootSplitViewController.swift:75-77` → `showInSidebarBesideArticle`, which pushes the timeline, animated, since
   the pop just removed it (`SceneCoordinator.swift:944-946`). Then `setTimelineFeed(nil)` completes synchronously
   (`fetchAndReplaceArticlesAsync`, `:2674-2676`) and calls `showColumn(.primary)` (`:1271`), which runs
   `popToViewController(feeds, animated: true)` (`:940-942`) while that push is starting. At least a flicker; UIKit may
   refuse the second transition and leave the person on an empty timeline, or log a corrupted navigation bar. The
   keyboard route `navigateToFeeds` (`MainTimelineViewController.swift:460`) reaches the same branch. In the base,
   collapsed, the article was already closed by then, so this did not happen. Read from the call chain; not captured.
6. **Outside routes may lose the feed they open (C-R4; medium; regression; plausible).** The same poses, with a feed
   already open in the list pane: outside routes deselect, which pops the list pane to the feeds list, animated
   (`iOS/SceneCoordinator.swift:1271` → `:940-942`), and then select a feed at once, which pushes the timeline in the
   same turn. `discloseFeed` waits 1 s before selecting again only in compact width (`:1669-1673`), where the base had
   a navigation stack; at regular width, now a navigation stack too, it selects at once. The new `didShow` branch
   deselects whenever the feeds list appears (`:1969`), and its `!isTimelineViewControllerPending` guard never applies:
   the flag is never set to true (declared `:97`; read `:1969`, `:1979`; reset to false only at
   `MainTimelineViewController.swift:255`). So a push that UIKit refuses mid-pop, or a `didShow` of the feeds list that
   arrives after the new selection, can drop or clear the feed and article the route opened. Routes: Handoff, Spotlight
   and Siri activities (`handle(_:)` → `selectSidebarItem(indexPath: nil)`, `:492`; `SceneDelegate.swift:72`, `:84`),
   notification taps that fall back to `discloseFeed` (`SceneDelegate.swift:106`), and ⌘F and the Search quick action
   (`showSearch`, `:528-535`; `SceneDelegate.swift:225`). It depends on UIKit's timing, and the flow runs none of these
   routes on the inner display.
7. **Unfolding drops a Search All (C-R5; medium; regression).** From closed or closed-sideways into any inner pose
   (T7). `showSearch`, reached by ⌘F (`iOS/RootSplitViewController.swift:186-188`) or the Search home-screen quick
   action (`iOS/SceneDelegate.swift:224-225`), calls `selectSidebarItem(indexPath: nil)`
   (`iOS/SceneCoordinator.swift:528-535`), so `currentFeedIndexPath` stays nil (`:1245`) while the results show in the
   timeline's search controller (`MainTimelineViewController.swift:389-393`). On unfold, `updateSidebarBesideArticle`
   puts only the feeds list on the list stack (`SceneCoordinator.swift:908`), which takes the timeline, with its search
   and results, out of the window. The coordinator keeps `isSearching` and the search's smart feed (`:1347-1353`,
   `:1384-1388`), but nothing shows them, and an article opened from the results stays in the detail pane with no list
   to return to. Before, the timeline kept its column on unfold (`before/timeline-book.json`: the "All Unread" bar at
   x 0–320 beside the detail at 321–951). Searching from the timeline's own field keeps the feed selected and is not
   affected. Read from the code; not captured.
8. **A launch directly upright may still show an overlay (L-L5; medium; not a regression; plausible).** Laptop, tent
   and flat-upright, on a cold launch or a scene reconnection on the inner display: the 320 pt
   `minimumSecondaryColumnWidth` that makes the split tile two panes upright is set only after the split's first layout
   pass. `iOS/RootSplitViewController.swift:45-48` runs `updateForFold()` after `super.viewDidLayoutSubviews()`;
   `iOS/SceneCoordinator.swift:847` sets the minimum only from `splitViewFoldDidChange`, and `init` applies the widths
   for no fold first (`:361`, `:852-853`). Foldsmith's P1 page (`references/patterns/fold-aligned-split.md`, § UIKit,
   "Two panes upright") asks for it in `viewWillLayoutSubviews`, before `super`, when the split exists in every pose;
   set later, the catalog's split stayed an overlay on 5 of 5 first launches in laptop. The later
   `preferredDisplayMode = .oneBesideSecondary` (`:910`) may rescue it. Every capture launched on the shut phone and
   opened the inner poses from it ([NetNewsWire.flow](NetNewsWire.flow); the skill's `tools/capture-corpus-flow.sh`), so
   this path is untested. Not worse than before, which showed the detail alone upright (`before/article-laptop.json`
   node 9, a bar at x 0–669).
9. **Upright, Previous Article and Next Article are behind More (L-L1; low; regression).** Laptop, tent and
   flat-upright, article: the detail pane is 328 pt wide (navigation bar x 341–669, was 0–669), and Previous Article and
   Next Article have left its top bar (before at x 425–465 and 481–521, `before/article-laptop.json` nodes 12–13). In
   their place a system overflow, More (`TopOverflowBarButtonItem`, x 481–521, y 28–68), sits beside "enter full
   screen" (x 365–405): `after/article-laptop.json` nodes 195–197, the same in `after/article-tent.json` and
   `after/article-flat-upright.json`; the shot `after/article-laptop-inner.png` shows only ⤢ and ••• in the right pane's
   bar. Each now takes an extra tap, and no capture opens More to show they are in it; the diff sets no
   `visibilityPriority` to choose what stays. More also appears in feeds and timeline (`after/feeds-laptop.json` node
   196, `after/timeline-laptop.json` node 199), where both buttons were disabled before. Low, lowered from medium: the
   timeline is now beside the article, so the next or previous article is one tap on its row, and Next Unread stays in
   the bottom bar.
10. **The edge swipe can bring up the empty column (C-R8; low; regression; plausible).** The same inner poses, list
    pane at the feeds level: the split's edge-swipe gesture is still on (`iOS/SceneDelegate.swift:25`,
    `presentsWithGesture = true`, unchanged) while the primary column holds only an empty controller
    (`iOS/SceneCoordinator.swift:886`, `:907`); the change hides only the display-mode button (`:909`,
    `displayModeButtonVisibility = .never`). At the feeds level the list stack has no pop gesture to take the swipe, so
    one from the leading edge can bring up the empty column of item 2, where before it showed the feeds sidebar. Low:
    an overlay can be dismissed. No capture exercises it.
11. **Reader View outside the vertical bar (L-L3; low; not a regression).** The article closed and closed-sideways, and
    article, feeds and timeline in book and flat-sideways: 8 cells (T10, the R marks under [Screens](#screens)). The
    change left it as it was. `after/article-closed.json` node 98 at x 333–377, y 608–652, while the bar's items are at
    x 399–437 (Share, node 104); `after/article-closed-sideways.json` node 97 at x 609–653, y 396–440, in the trailing
    bottom corner, while the bar is on the leading edge at x 29–67; `after/article-book.json` node 161,
    `after/feeds-book.json` node 167 and `after/timeline-flat-sideways.json` node 162 at x 818–862, y 599–643, while the
    bar's items are at x 884–922. Before, the same (book x 798–842). The item is made at
    `iOS/Article/ArticleViewController.swift:143-147` as `UIBarButtonItem(customView:)` with no `axisBehavior =
    .verticalPreferred`, the local fix the decision table names (decision 4). Low: it works and can be reached, only
    misplaced.
12. **Sheets presented off the root split skip the sheet rule (L-L6; low; not a regression; plausible).** Book: Add
    Folder is presented by the feeds list (`iOS/SceneCoordinator.swift:1764-1768`,
    `mainFeedCollectionViewController.present` with `.formSheet`); Settings presents its import and export document
    pickers (`.formSheet`) and Safari (`.pageSheet`) on itself (`iOS/Settings/SettingsViewController.swift:582`, `:634`,
    `:658`). UIKit does not route a child's `present` through the root's override, so the placement is set before
    presentation only in that override (`iOS/RootSplitViewController.swift:50-53`) and corrected only on the root's
    next layout (`:64`, `updateSheetPlacement`), which presenting a form sheet need not cause. These sheets likely still
    open on the leading half over the list pane (T15), where automatic placement put Settings before
    (`before/settings-book.json` node 4, x 8–467.5). No flow line reaches them. Low: they are rarely opened.
13. **Add Feed's symbols are not gated on a vertical bar (L-L7; low; not a regression).** On every iOS 27.1 device,
    including the Duo in laptop, tent, flat-upright and flat-sideways, where the sheet's bar stays horizontal, Cancel
    and Add became icon-only ✕ and ✓, gated only on `#available(iOS 27.1, *)` (`iOS/Add/AddFeedView.swift:87-111`,
    `Button(title, systemImage: "xmark"/"checkmark")`) rather than on the window having a vertical bar. The decision
    table's "Local and structural changes" names this case and asks for the gate (decision 3).
    `before/add-feed-laptop.json`: Cancel x 124.5–201.2 and Add 488.5–544.5 (words); `after/add-feed-laptop.json`:
    Cancel 124.5–164.5 and Add 504.5–544.5 (40 × 40 glyphs); tent and flat-upright the same; flat-sideways Cancel
    265.5–342.2 → 265.5–305.5. Where the bar is vertical (closed, closed-sideways, book: Cancel and Add at x 883.8–921.8
    in `after/add-feed-book.json`) the change is right. Low: the titles stay their accessibility labels and the buttons
    work.
14. **"Loading…" still lands on the fold (C-R12; low; not a regression).** Laptop and tent, article pane: only the empty
    "No selection" page got the placeholder moved up. The "Loading…" page shown while Reader View extracts an article
    still uses `.systemMessage { top: 45% }` and briefly sits on the horizontal fold (T1).
    `iOS/Article/WebViewController.swift:688-689` renders `loadingHTML` unmodified (`<h3 class="systemMessage">`,
    `Shared/Article Rendering/ArticleRenderer.swift:192-195`), and only the `noSelection` branch adds
    `FoldAlignedSplit.placeholderStyle` (`:700-706`). Placement: `Shared/Article Rendering/stylesheet.css:28-32`; the
    same 45 % put "No selection" at y 444–469 in `before/feeds-laptop.json`, across the fold at 455.5–495.5. Transient,
    and as before.
15. **Contribution-rule nits (C-R13; low; not a regression).** Every commit subject starts "Duo: …" rather than with a
    present-tense verb (`Technotes/CodingGuidelines.md:167`). New stored properties sit mid-file
    (`iOS/SceneCoordinator.swift:883-889`; `iOS/RootSplitViewController.swift:32`), against "Properties go at the top"
    (`:49`). `updateSidebarBesideArticle` (`SceneCoordinator.swift:893`) and `restoreThreeColumns` (`:914`) are
    internal though only `SceneCoordinator.swift` uses them (`:43` asks for private as often as possible). The new
    `iOS/FoldAlignedSplit.swift` header (lines 1–16) has no Created by or Copyright line and refers to an outside
    "duo-patterns" project. Commit `2b2e1dc`, unlike the other six, has no LLM note; `CONTRIBUTING.md:25` asks for one
    per pull request, so this matters only if that commit goes alone ([Project rules](#project-rules)).

## Left to do
- The independent review's regressions (1–7, 9, 10 under
  [Known misses and regressions](#known-misses-and-regressions-independent-review-2026-10-31)), the CI build (1) and
  Mark All as Read (2) first, and its other misses (8, 12–15; 11 is the Reader View button).
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
