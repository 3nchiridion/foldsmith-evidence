# NetNewsWire on iPhone Duo, second run — migration report

> Edited on 2026-11-02, after the run: [Corrections](#corrections) lists what changed and why, including the
> independent review recorded under
> [Known misses and regressions](#known-misses-and-regressions-independent-review-2026-11-01). Notes meant for
> Foldsmith's own team (timing, the run agent's 12 frictions, where the skill's references and the SDK differ) are in
> [RUN-NOTES.md](RUN-NOTES.md); how this run compares with the first is in
> [README.md](README.md#compared-with-the-first-run).

## At a glance
- **Status: by the run's own checks, done for four of the five screens it captured, with feeds left (T7, a structural
  change proposed and not made); 3 regressions that an independent review confirmed after the run are not fixed.**
  The high one: the change does not build with the Xcode 26.6 the project's CI selects (C-R1). Held upright, the
  article pane's Share button is no longer on screen and no capture shows where it went (L-L1, medium), and Previous
  Article and Next Article have left its top bar (L-L2, low)
  ([Known misses and regressions](#known-misses-and-regressions-independent-review-2026-11-01)). Not a pull request
  ([Project rules](#project-rules)).
- **For an iPhone Duo user:** in book the timeline and the article each get one half, meeting in the fold, instead of
  the article crossing it; half open or flat upright the timeline stays beside the article, which was alone there
  before; the empty article pane's "No selection" is off the fold in laptop and tent; the article's Reader View joins
  the vertical bar on the upright cover and in book and flat-sideways (on the turned cover, by the code, it is in that
  bar's More); Settings and Add Feed open on the right half in book; Add Feed's Cancel and Add join the vertical bar on
  the cover and in book. Unfolding from the feeds list still shows no feeds list (feeds T7). What it costs them is under
  Known regressions, below.
- **Coverage:** 4 of the 5 screens in the flow done (timeline, article, settings, Add Feed), feeds left. Of the 35
  cells, 17 verified (changed and re-checked), 13 fine as is, 5 left (feeds in the five inner poses). One of the 17,
  Add Feed flat-sideways, is verified with the same snapshot before and after and keeps its T10, waived (the sheet is
  centred there and has a horizontal bar of its own), so strictly 16 cells were changed and re-checked. Screens
  outside the flow were not captured.
- **Change:** NetNewsWire d9ec7df; seven code commits of the branch's 11 ([commits.txt](commits.txt)); 11 files,
  +359 −41 ([NetNewsWire.patch](NetNewsWire.patch)). The branch also tracks five Foldsmith files under `.duo/` (+859),
  which the patch leaves out.
- **Checked by:** the run's detectors, from plugin 0.8.0 ([detectors-before.md](detectors-before.md) and
  [detectors-after.md](detectors-after.md) are the run's own tables): 29 marks in 19 cells before (34 lines), 6 in 6
  after (7 lines: feeds T7 in five poses, and Add Feed flat-sideways T10, waived), no pose with a class it did not have
  before; the run's look at every shot; and the run's own review of the diff, since it could not start a subagent (it
  found I1, fixed in `523be11`, and I3, a false alarm). After the run, on 2026-11-01, an independent review: a layout
  reviewer and a code reviewer working blind, then a skeptic who checked their items; 18 raised, 9 confirmed (3 of
  them regressions), 3 merged into a confirmed item, 6 refuted
  ([Known misses and regressions](#known-misses-and-regressions-independent-review-2026-11-01)). Unit tests not run;
  no simulator without a fold run; no build with the CI's Xcode 26.6, which was not installed
  ([Not checked](#not-checked)).
- **Known regressions** (from the independent review, none fixed): the change does not build with the CI's Xcode 26.6
  (C-R1, high); in laptop, tent and flat-upright the article pane is 328 pt wide, Share is in none of its bars (L-L1,
  medium), and Previous Article and Next Article are not in its top bar (L-L2, low); they may be inside a More button
  that no capture opened (the M cells under [Screens](#screens)).
- **Known misses** (not regressions): feeds T7 in the five inner poses (L-L3); on the turned cover Reader View is in
  the vertical bar's More, which no capture opened (L-L6); about a dozen sheets presented from inside another sheet
  skip the sheet rule (C-R4); the "Loading…" page lands on the fold in laptop and tent (C-R6); the empty page loads
  twice, with no visible effect (C-R9); points of the project's contribution rules (C-R10).
- **Decisions for NetNewsWire's maintainers:** two panes upright; feeds in three levels (proposed in
  [PLAN.md](PLAN.md), not made); symbols for Add Feed's Cancel and Add where the bar is vertical; Reader View in the
  turned cover's More; the Xcode the CI uses; the `.duo/` files and the commit subjects
  ([details](#decisions-for-the-maintainers)).

## Glossary
| Term | Meaning |
|---|---|
| iPhone Duo | Apple's folding iPhone (iOS 27.1). |
| cover | the outer display, 466 × 678 pt. |
| inner display | 951 × 669 pt held wide, 669 × 951 pt held tall. |
| pt | points, UIKit's layout unit. |
| fold, division | the band the system reserves over the hinge: 455.5–495.5 pt across the inner display. |
| reserved regions | how an app reads the fold: `UIView.reservedRegions(kind: .division)` (iOS 27.1). |
| active, inactive | a fold is active while the phone is part folded (book, laptop, tent), inactive when it lies flat. |
| closed, closed-sideways | shut, with the cover upright; shut, with the cover turned. |
| book | half open, held like a book (fold vertical). |
| laptop, tent | half open, resting like a laptop (fold horizontal); as laptop, standing on its edges. |
| flat-sideways, flat-upright | open flat, wide or tall. |
| upright | laptop, tent and flat-upright: the inner display held tall. |
| vertical bar | on the cover and in book and flat-sideways, a column at one edge where the system puts bar items. |
| joining it | an item joins the vertical bar only with a symbol, or a custom view set to prefer it (`axisBehavior`). |
| More | the system's overflow button, holding the items a bar has no room for; no capture of this run opened one. |
| cell | one screen in one pose: 5 screens × 7 poses = 35. |
| verified | a cell's state in [migration.json](migration.json): changed by the migration, then re-checked. |
| fine as is | state `unchanged`: looked at, nothing to fix; the gate checks its snapshots match before and after. |
| left | state `finding`: a class remains; here feeds' five inner cells (T7). |
| waived | a detector line or a check the run set aside, with its reason, in migration.json. |
| local, structural | a structural change reroutes navigation; this run made local ones only and proposed the other. |
| T1 crease-straddle | something that does not scroll sits on the fold. |
| T7 state-loss-on-fold | after unfolding, the screen the person was on is gone (here: behind a button). |
| T10 stranded-horizontal-item | a bar item that stays outside the vertical bar. |
| T14 columns-off-fold | split-view panes or columns not aligned with the fold. |
| T15 sheet-in-book | in book, a sheet sits on the leading half, over the list. |
| Fold-aligned split (P1) | Foldsmith's pattern for a list and its detail: two panes, one on each side of the fold. |
| Three levels | P1's recipe for feeds, timeline and article: the list pane holds the first two (structural here). |
| sheet rule | P1's rule for sheets: on the trailing half while the book fold is active. |
| detectors (`duo-detect`) | Foldsmith's checks: they read each shot's accessibility snapshot for the classes above. |

## Corrections
Corrected on 2026-11-02 against the run's evidence (migration.json, the before and after snapshots, the branch and
[NetNewsWire.patch](NetNewsWire.patch)) and the independent review of 2026-11-01. The run's own text is `.duo/REPORT.md`
at `d5cd9a2` in the run's working copy, not published. The code, the run and the other evidence files in this folder
are unchanged, but for the simulator's UDID, left out of [audit.md](audit.md) and [migration.json](migration.json).
- "done: verified in every pose" as the state of timeline (5 verified, 2 fine as is), settings (1 and 6) and Add Feed
  (4 and 3), the wording the first run's corrections took out: replaced with per-cell counts from migration.json.
  Article's seven cells are all verified, so its "verified in every pose" holds by the counts; what its line adds
  about More does not (next item). The same words stay in migration.json's done lines, in the subject of `e71d74b`
  ([commits.txt](commits.txt)) and in the body of `d5cd9a2` (not published), as the run left them; Foldsmith 0.8.1's
  gate refuses the three done lines ([foldsmith-0.8.1-check.txt](foldsmith-0.8.1-check.txt)).
- "Previous Article, Next Article, Share … are in the detail's More overflow: one more tap each" (the controls that
  moved, issue I2, the article's done line): stated as fact, though no capture opened that More. The snapshots show
  only that Share is in none of the 328 pt pane's bars and that Previous and Next are not in its top bar. The
  independent review confirmed both as regressions (L-L1, medium; L-L2, low), where the run had marked I2 "not a
  regression" and "wontfix". Added: the article's share sheet carries Find in Article and Open in Browser, which the
  share sheet of the timeline row's context menu, the other way to share upright, does not.
- The sheet rule set "before every sheet the app presents": the patch sets it before the sheets listed under
  [What changed](#what-changed), but about a dozen sheets presented from inside another sheet never get it (C-R4).
- Added: the change does not build with the Xcode 26.6 the project's CI selects (C-R1, high). The run's report did not
  mention the CI, and gave the iOS 27 APIs as "behind `#available`", a run-time check that does not make code compile
  with an older SDK.
- "All local, gated on the fold or the vertical bar": not true as worded;
  [Devices without a fold](#devices-without-a-fold) lists what runs on every iOS 27.1 device and on every device.
  "Without a fold … Reader View stays `.automatic` with no overflow item, Add Feed keeps its text buttons": those two
  are gated on a vertical bar, not on a fold, so they change on the Duo's cover too.
- `8e7c888` "(hardening; no shot changed)", and migration.json's "feeds, timeline and article recaptured twice after
  two fixes": only feeds and timeline were captured after `8e7c888`, and their earlier shots were overwritten, so "no
  shot changed" cannot be checked here; article was recaptured once, after `523be11` and before `8e7c888`; settings
  and Add Feed were not recaptured after either fix. The capture times are under [Screens](#screens).
- "No pose got worse by the detectors; `progress.py check` passes, with no open issue": kept as the verdict of plugin
  0.8.0's detectors and gate. The 3 regressions the review confirmed are of kinds those detectors do not report, and
  Foldsmith 0.8.1's gate refuses the run: 37 controls gone with no record of where, three done lines, no review on
  record ([README.md](README.md#what-foldsmith-081-changed-because-of-this-run)).
- Added: [Project rules](#project-rules). The branch tracks five `.duo/` files and its 11 commit subjects begin with
  "Duo:", against NetNewsWire's rules (C-R10); both are what plugin 0.8.0 told the run to do.
- "No independent reviewer": kept as the run's account (it could not start a subagent). The independent review that
  followed on 2026-11-01 is added: At a glance, [Known misses and
  regressions](#known-misses-and-regressions-independent-review-2026-11-01), and references to it in Screens,
  Controls that moved, Decisions, Not checked and Left to do.
- "No selection" at "233–246 pt in book, 291–306 pt upright": these are not the snapshots' frames of that text, which
  are given instead (228–253 held wide, was 317–342; 286–311 upright, was 444–469).
- The simulator's UDID in the header is left out. The paths to the run's `.duo/` files point to the copies in this
  folder ([audit.md](audit.md), [PLAN.md](PLAN.md), [migration.json](migration.json),
  [NetNewsWire.flow](NetNewsWire.flow)). The 70 shot links of the per-pose tables, which do not exist in this folder,
  are replaced by one grid, the path pattern and the capture times. The note of issue I3 in migration.json cites
  `87584b9`, which is not on the branch; the run's report had `8e7c888` right.
- "Where the references and the SDK differ" moved to RUN-NOTES.md; "Review of the diff" is folded into At a glance and
  Devices without a fold; "App Store screenshots" is one line under Screens. Added: this summary, the glossary,
  decisions for the maintainers, what was not checked, what is left and how to reproduce.

## Project rules
NetNewsWire's `CONTRIBUTING.md` asks contributors to ask first, with a ticket and a topic on the project's forum,
before working on a change, and says a pull request opened without that is ignored or closed. It asks that a PR note
any LLM use, that its author review every line, that it merge cleanly with `main` and add no warnings, and it takes no
documentation PRs. This report is not a PR, and nothing was sent to the project. The code was written by a Claude Code
agent with the Foldsmith skill; all 11 commits carry a `Co-Authored-By` line naming the model. The run's log records
no warning count. The independent review found the branch short of these rules: it does not build with the Xcode 26.6
the project's CI selects, against "merge cleanly" and `Technotes/CodingGuidelines.md` line 149, no code that triggers
compiler errors or warnings (C-R1); it tracks five Foldsmith files under `.duo/`, which would ship in a PR as
LLM-written documentation; its commit subjects begin with "Duo:" where line 167 asks for a present-tense verb; and new
stored properties sit below a method, where line 49 puts properties at the top (C-R10). The `.duo/` files and the
subjects are what plugin 0.8.0 told the run to do; 0.8.1 commits no `.duo/` file and follows the project's commit
style ([README.md](README.md#what-foldsmith-081-changed-because-of-this-run)).

## Screens
Cell states from [migration.json](migration.json); the classes from the run's detector tables (plugin 0.8.0); the M
and O marks from the after snapshots, and W from migration.json's waivers.

| Screen | closed | closed-sideways | book | laptop | tent | flat-sideways | flat-upright |
|---|---|---|---|---|---|---|---|
| feeds | =, W | =, W | ✗ T10 T14 T7 → T7 | ✗ T1 T7 → T7, M | ✗ T1 T7 → T7, M | ✗ T10 T7 → T7 | ✗ T7 → T7, M |
| timeline | = | = | ✓ T10 T14 | ✓ T1 T7, M | ✓ T1 T7, M | ✓ T10 | ✓ T7, M |
| article | ✓ T10 | ✓ T10, O | ✓ T10 T14 | ✓, M | ✓, M | ✓ T10 | ✓, M |
| settings | = | = | ✓ T15 | = | = | = | = |
| add-feed | ✓ T10 | ✓ T10 | ✓ T10 T15 ᵃ | = | = | ✓ T10, W ᵃ | = |

- **✓** verified; the classes after it are what the run's detectors found there before (a ✓ with none: the change
  altered the cell, which was then re-checked; the run re-marked the article's three upright cells from fine as is to
  `migrated` (to be re-checked) at 00:07, since the tiling changes them; the proof then verified them). **=** fine
  as is. **✗** left: the classes before → after.
- **M**: two panes upright. The article pane is 328 pt wide (x 341–669); Share is in none of its bars (L-L1), and
  Previous Article and Next Article are not in its top bar (L-L2), where a More sits beside "enter full screen"; no
  capture opened that More. In the feeds and timeline cells no article is open, so these buttons were disabled before
  as well. The independent review confirmed both as regressions.
- **O**: Reader View is no longer on the turned cover; by the code it is in the vertical bar's More, which no capture
  opened (L-L6, not a regression).
- **W**: waived. Add Feed flat-sideways keeps its T10: open flat the sheet is centred and its own bar is horizontal
  (`toolbarVerticalEdge` is nil inside it), and its snapshot is the same before and after. Feeds closed and
  closed-sideways: only the header's live subtitle and the unread counts differ, so the `unchanged` check is waived.
- **ᵃ**: Foldsmith 0.8.1's T10 detector checks an item only in a bar that reaches the vertical bar's band: Add Feed
  before is then `T15` in book and nothing flat-sideways, and after, nothing flat-sideways
  ([README.md](README.md#what-foldsmith-081-changed-because-of-this-run)).
- C-R1, the build with the CI's Xcode, concerns every cell and is not marked.

Per screen, measured in the accessibility snapshots (before → after):
- **feeds** (Fold-aligned split; T7 left). Unfolding from the feeds list showed an empty timeline beside "No
  selection" (book, flat-sideways) or "No selection" alone (laptop, tent, flat-upright), with the feeds list behind
  Show Sidebar ([audit.md](audit.md)). Now an empty timeline pane sits beside "No selection" in every inner pose:
  0–474.7 | 475.7–951 in book, 0–340 | 341–951 flat-sideways, 0–340 | 341–669 upright; the list is still behind Show
  Sidebar (L-L3). The local changes cleared feeds' T14, T1 and T10, as for timeline below.
- **timeline** and **article** (Fold-aligned split; one split view serves both). Book: timeline 0–320 and article
  321–951, its text column 340–827 across the fold → the panes meet in the fold (474.7 | 475.7); the timeline's rows
  end at 454.7 and the article's text starts at 514, so neither crosses 455.5–495.5; the article pane is 475 pt wide
  instead of 630. Flat-sideways: 0–320 | 321–951 → 0–340 | 341–951. Upright: the article alone at 0–669, the timeline
  hidden (timeline's T7) → the timeline at 0–340 beside the article at 341–669 (M). "No selection" in the empty
  detail: y 444–469 in laptop and tent, on the fold (T1) → y 286–311; held wide, y 317–342 → 228–253. Reader View: on
  the cover from x 333, outside the vertical bar (T10), into it at x 396; in book and flat-sideways from x 798 and 818
  into the bar at x 881; on the turned cover off screen (O).
- **settings** (the sheet rule). Book: the sheet at x 8 (459.3 pt wide, over the list) → x 483.5, and Done from the
  sheet's horizontal bar into the vertical bar (x 407.3 → 883.8). In the other poses the sheet's frame and Done are
  the same before and after.
- **add-feed** (the sheet rule, and symbols where the bar is vertical). Book: the sheet at x 8 → 483.5, Cancel and Add
  from words in its horizontal bar (76.7 × 36 and 56 × 36 pt) to 38 × 38 pt symbols in the vertical bar (x 883.8).
  Cover: words in a horizontal bar → symbols in the vertical bar, at x 399 (closed) and x 29 (closed-sideways).
  Laptop, tent and flat-upright: the same words in the same frames. Flat-sideways: the sheet is centred (its bar at
  245.5–705.5) with the words in its own horizontal bar, as before (W).

When the after shots were taken, by their file times and migration.json's log: settings and Add Feed 00:19–00:24,
before `523be11` (committed 00:27:48) and `8e7c888` (00:38:56); article 00:35–00:37, after `523be11`, before
`8e7c888`; feeds and timeline 00:39–00:44, after both. The first after pass of feeds, timeline and article
(00:09–00:18) and the first recapture of feeds and timeline (00:28–00:34) were overwritten. Plugin 0.8.0's captures
record no code identity.

The shots and snapshots are not published, as for every run. In the run's copy they are
`.duo/captures/NetNewsWire/{before,after}/<screen>-<pose>-cover.png` (closed poses) or `…-inner.png` (the others), each
with `<screen>-<pose>.json` beside it; migration.json names every shot. The run also exported 18 App Store JPEGs to
`.duo/appstore/`, out of git; none is published.

The run's issues and waivers, from migration.json:
- **I1**, article closed-sideways, a regression the run's own review found: once `.verticalPreferred`, Reader View
  lands in the turned cover's More, where a custom-view item has no title or image. Fixed in `523be11` with a menu
  item built by code; checked by its code path and the snapshot, not by a tap (L-L6 finds it still unverified).
- **I2**, article laptop, tent and flat-upright: Previous, Next and Share are not in the 328 pt pane's bars. The run
  marked it low, not a regression, and "wontfix" as the cost of two panes upright, and wrote that they are in More; the
  independent review confirmed two regressions there (L-L1, L-L2), and that they are in More is not shown.
- **I3**, timeline book and flat-sideways: a suspected placeholder at 45 %, which the run then measured at 25 % in its
  after shots (since overwritten); `8e7c888` kept as hardening. Its note cites `87584b9`, the commit before an amend,
  which is not on the branch.
- Waivers: Add Feed flat-sideways T10, and feeds closed and closed-sideways `unchanged` (W above).

## What changed
One commit per local change in [PLAN.md](PLAN.md), then two fixes made after the first proof capture. Line numbers
point into [NetNewsWire.patch](NetNewsWire.patch). The new files are in `iOS/Duo/`, part of the iOS app target through
its synchronized `iOS` folder (checked in the Xcode project).
- `iOS/Duo/DuoFold.swift` (new, lines 249–291): `windowHasFold` (a division, active or not) and `isBookFoldActive` (an
  active division taller than wide), read from the window's `reservedRegions(kind: .division…)` and gated
  `#available(iOS 27.1, *)`, and the `windowFoldDidChange` notification. Nothing reads the device, its idiom or its
  orientation.
- **timeline, article, feeds: column widths and tiling** (`0dd7f93`). `RootSplitViewController` (lines 392–490): in
  `viewWillLayoutSubviews`, before `super`, where the window has a fold, it keeps the app's six column widths and sets
  them to `automaticDimension`, so the system puts the boundary on the fold in book and sizes the leading pane at
  340 pt flat-sideways, and sets `minimumSecondaryColumnWidth` (iOS 26 API) to 320 pt, so the timeline tiles beside
  the detail upright; without a fold it puts the app's widths back. A `UIHingeInteraction` asks for a layout when the
  hinge moves, and again 0.35 s later (lines 418–430). `SceneCoordinator` saves no column width while the fold sizes
  the columns (lines 503–506, 514–517).
- **timeline, feeds: the placeholder off the fold** (`e241cb7`). `WebViewController` (lines 193–245): where the window
  has a fold the empty page adds `.systemMessage { top: 25%; }` (45 % in the app's stylesheet), and it is drawn again
  when the fold comes or goes (on layout, and on the notification).
- **article, timeline, feeds: Reader View in the vertical bar** (`c091a32`). `ArticleViewController` (lines 126,
  138–158): the custom-view item keeps its view and is `axisBehavior = .verticalPreferred` where the window has a
  vertical bar (`traitCollection.verticalBarEdge` not unspecified), `.automatic` elsewhere, read again when those traits
  change.
- **settings, add-feed: the sheet rule** (`4f6de2c`). `iOS/Duo/FoldAwareSheets.swift` (new, lines 292–336):
  `applyFoldAwareSheetPlacement(to:)` sets `preferredPlacement` (iOS 27.0) to `.trailing` while the book fold is
  active, `.automatic` otherwise; `updateFoldAwareSheetPlacement()` walks the sheets already up and moves them, and the
  root split calls it when the fold changes (lines 449–450). It is called before presenting Settings, Current Activity,
  the account, notification and feed inspectors and Add Feed (lines 525–569), Add Folder from the feeds list (line
  577), Add Feed from Settings, Settings' OPML import and export pickers and its Safari pages (lines 589–613), the
  account inspector's Safari page (line 384) and the credentials sheet after an account error (line 625). Not before
  about a dozen nested sheets (C-R4).
- **add-feed: bar items with symbols** (`3201df1`). `iOS/Duo/VerticalBarReader.swift` (new, lines 337–375) reads
  SwiftUI's `toolbarVerticalEdge` (iOS 27.1); in `AddFeedView` (lines 5–117, mostly re-indentation inside the reader),
  where the sheet's bars are vertical, Cancel and Add are `Button(title, systemImage: "xmark"/"checkmark")`; elsewhere
  they are the text buttons they were.
- **article: Reader View in the overflow** (`523be11`, the fix for I1). `ArticleViewController` (lines 142–152,
  160–180): where the window has a vertical bar the item also has a `menuRepresentation`, built when the overflow
  opens: a "Reader View" action with the button's image, on state and enabled state, toggling the extractor as the
  button does.
- **all: the fold read from the window** (`8e7c888`). `DuoFold.swift` asks the window, not the view it is called on
  (`window ?? self`, lines 281 and 289), so a pane or a sheet reads the same fold as the split.
- The API names match the iOS 27.1 SDK: `UIView.reservedRegions(kind:options:)` and `.includeInactive`,
  `UISheetPresentationController.Placement` and `preferredPlacement` (27.0), `UIBarButtonItem.axisBehavior` (27.1),
  `UITraitCollection.verticalBarEdge` and `systemTraitsAffectingVerticalBarEdge`, `UIHingeInteraction`,
  `minimumSecondaryColumnWidth` (26.0), SwiftUI's `toolbarVerticalEdge`. The iOS 27 ones are not in the SDK of the
  CI's Xcode 26.6 (C-R1).

## Controls that moved
From the snapshots, before → after. Where a control went when it left the screen, no capture shows.
- **Reader View**: from a horizontal toolbar outside the vertical bar (T10) into the vertical bar on the cover, in
  book and in flat-sideways, with no extra tap. On the turned cover it is no longer on screen; by the code it is in the
  vertical bar's More, as a menu item with its title, image, on state and enabled state (`523be11`): one more tap than
  before, unverified (L-L6).
- **Share** (article, feeds and timeline, in laptop, tent and flat-upright): in none of the 328 pt pane's bars (L-L1,
  regression). The run wrote that it is in the detail's More.
- **Previous Article, Next Article** (the same nine cells): no longer in the pane's top bar, where More takes Next's
  slot; presumably in More, unverified (L-L2, regression).
- **The split's display-mode button** (the same cells): at the leading edge of the detail's top bar, "split view
  horizontally left" at x 24 while the detail filled the display, now "enter full screen" at x 365 beside the timeline
  (L-L2). Foldsmith 0.8.1's gate, which compares labels, counts it among the 37 controls gone.
- **Add Feed's Cancel and Add**: into the vertical bar as symbols on the cover and on the trailing sheet in book, with
  the same taps; elsewhere the text buttons, as before.
- Every other labelled control of the before snapshots is in the after ones (Foldsmith 0.8.1's gate compares them in
  the 22 cells not fine as is: [foldsmith-0.8.1-check.txt](foldsmith-0.8.1-check.txt); the 13 fine as is, compared
  label by label after the run, lose none).

## Devices without a fold
Off the fold the layout should be as before, but not every change is gated on the fold, and no simulator without a fold
was run.
- **Gated on a fold** (`windowHasFold`, a division active or not, on iOS 27.1): the column widths and the 320 pt detail
  minimum, not saving widths, the placeholder at 25 %. On the Duo this includes flat-sideways and flat-upright, where
  the fold is inactive. Sheets go `.trailing` only while the book fold is active.
- **Gated on a vertical bar** (iOS 27.1): Reader View's `axisBehavior` and overflow item (`verticalBarEdge`), and Add
  Feed's symbols (`toolbarVerticalEdge`). On the Duo that includes the cover, which has no fold; whether a device
  without a fold ever has a vertical bar was not checked.
- **Runs on every iOS 27.1 device** (iPhone, iPad): a `UIHingeInteraction` on the root split's view (lines 418–430);
  `preferredPlacement = .automatic` set on each sheet the patch reaches whenever no book fold is active (lines
  313–318); the trait-change registration for Reader View (lines 155–157); `VerticalBarReader` around Add Feed's form,
  which then shows the text buttons as before.
- **Runs on every device and system**: the root split's `viewWillLayoutSubviews` override, which calls
  `updateColumnsForFold()` on every layout pass and returns at once without a fold (lines 432–455); the empty page's
  `viewDidLayoutSubviews` override and notification observer, which draw it again only when the window's fold changed
  (lines 202–229); the width-save guards, which let every save through without a fold. Before iOS 27.1 the fold reads
  return false and `VerticalBarReader` hands Add Feed `false`.
- **Mac**: the Mac app's target compiles `Mac/` and `Shared/`; every changed file is in `iOS/`, which only the
  NetNewsWire-iOS target includes (checked in the Xcode project), and that target sets `SUPPORTS_MACCATALYST = NO`.
  The Mac app's source is unchanged; it was not built.

## Decisions for the maintainers
1. **Two panes upright** (`0dd7f93`, the 320 pt `minimumSecondaryColumnWidth`). Laptop, tent and flat-upright show
   the timeline (0–340) beside a 328 pt article (341–669), as the decision table asks for hierarchical content. The
   cost is L-L1 and L-L2: Share and Previous and Next leave the pane's bars, perhaps for a More no capture opened, and
   the diff sets no `visibilityPriority` to choose what stays (Foldsmith 0.8.1's P1 page names it and
   `menuRepresentation`, neither yet measured on a run). Without the minimum the article is alone at 669 pt there, as
   before, and the timeline is gone upright (T7).
2. **Feeds in three levels** (proposed in [PLAN.md](PLAN.md), not made). Without it feeds keeps T7: unfolding from the
   feeds list shows an empty timeline beside an empty detail, with the list one tap away behind Show Sidebar. The
   proposal puts the feeds list on the timeline column's navigation stack while the split is expanded on a display
   with a fold: the design the first run committed as `2b2e1dc`, in which its independent review found six
   regressions ([first run](../NetNewsWire/REPORT.md#known-misses-and-regressions-independent-review-2026-10-31)).
   PLAN.md's risk list names the same places (Mark All as Read, state restoration, keyboard navigation, user
   activities, notifications, shortcuts, the iPad), and its routes table has 104 lines from `tools/routes.sh`. It is a
   navigation change, not a layout one.
3. **Symbols for Add Feed's Cancel and Add where the bar is vertical** (`3201df1`): `xmark` and `checkmark` with their
   titles where `toolbarVerticalEdge` is set (the cover, the trailing sheet in book); the text buttons elsewhere,
   including the sheet centred open flat, whose bar is horizontal.
4. **Reader View in the vertical bar** (`c091a32`, `523be11`): the custom view kept, set to prefer the vertical bar.
   On the turned cover the bar is short and the item goes into its More, where a menu item built by code stands in for
   it, never opened by a capture (L-L6).
5. **The Xcode the project's CI uses** (the iOS 27 uses in `0dd7f93`, `c091a32`, `4f6de2c`, `3201df1`, `523be11` and
   `8e7c888`). Behind run-time `#available` checks only, they cannot be built by the CI's Xcode 26.6 (C-R1). Either move
   CI to Xcode 27.1 in the same change, or put each use in a compile-time guard with the pre-Duo code in `#else`;
   Foldsmith 0.8.1's CHANGELOG notes that Xcode 27.0 and 27.1 both ship Swift 6.4, so `#if compiler(>=6.4)` keeps no
   27.1 symbol from an Xcode 27.0 build.
6. **The `.duo/` files and the commit subjects** (C-R10): the five Foldsmith files would have to leave the branch, and
   the subjects would have to begin with a present-tense verb, before anything goes to the project.

## Not checked
- The app's unit tests (`./buildscripts/build_and_test.sh`, as the app's `CLAUDE.md` gives it). Build warnings were
  not counted. Nor was the change built with the Xcode 26.6 the project's CI selects, which was not installed; by the
  SDK headers it cannot compile there (C-R1).
- Any simulator without a fold (iPhone, iPad); the Mac app was not built.
- What the two More menus hold: the upright detail's (Share? Previous and Next?) and the turned cover's (Reader View).
  The tap runner reaches only the upright cover, and a menu opened before a fold closes at the next one, so neither
  was opened (L-L1, L-L2, L-L6).
- Taps on the inner display: the captures reach each screen with taps on the shut phone and then unfold it, so picking
  a feed or an article in a pane, Show Sidebar, the display-mode button and Mark All as Read there were never
  exercised. The run's reading of the routes is that no change moves a controller between stacks or columns.
- Launching with the phone already open: every capture launched on the cover. The 320 pt minimum is set in
  `viewWillLayoutSubviews` before `super`, the order the P1 page asks for against an overlay on a first launch in
  laptop, but no launch directly upright was captured.
- State restoration: the flow starts every launch at the feeds list (`-selectedSidebarItem <dict/> -selectedArticle
  <dict/>` in [NetNewsWire.flow](NetNewsWire.flow)), so reopening the last feed and article was not exercised.
- Sheets and screens outside the flow: Add Folder, the inspectors, Current Activity, the account sheets, article
  search, the full-screen image. The nested sheets of C-R4 and the "Loading…" page of C-R6 were read from the code,
  not captured. Nor were other window sizes on the inner display.
- The code behind the after shots: settings' and Add Feed's predate `523be11` and `8e7c888`, article's `8e7c888`
  (Screens). Those commits touch the article's Reader View item and the fold helpers; that they cannot change the
  settings and Add Feed shots is the independent review's reading (L-L8, refuted), not a capture. "No shot changed"
  after `8e7c888`, and I3's verdict, rest on shots since overwritten.

## Known misses and regressions (independent review, 2026-11-01)
After the run, Claude Code agents with no part in it reviewed the branch: a layout reviewer (the before and after shots
and accessibility snapshots) and a code reviewer (the diff and the source at both revisions), working blind (what
exactly they were kept from is not recorded; their items cite migration.json's cell states), then a skeptic who checked
each item against the `duo-baseline..duo-migration` diff, both source revisions, the snapshots and shots, the iOS 27.1
SDK headers and Foldsmith's rules. Nothing was built with Xcode 26.6: only 27.1 is installed, and the review was not
allowed to build. They raised 18 items (layout L-L1 to L-L8, code C-R1 to C-R10): 9 stand, 3 of them regressions (1
high, 1 medium, 1 low) and 6 not; 3 repeated a confirmed item and were merged into it; 6 were refuted.
[README.md](README.md#independent-review-2026-11-01) has each item's evidence and the skeptic's notes. Lines are in the
source at `duo-migration` (`d5cd9a2`), not in the patch; `before/` and `after/` are the run's snapshots and shots, not
published (path pattern under [Screens](#screens)). None of the 9 is fixed.

1. **The change does not build with the project's CI toolchain (C-R1; high; regression).** NetNewsWire's "iOS
   Simulator Tests" job, run on every pull request, selects Xcode 26.6 (`.github/workflows/ci.yml:87–88`) and builds
   the scheme NetNewsWire-iOS. The new code uses symbols only the iOS 27 SDKs have (`reservedRegions`,
   `UIHingeInteraction`, `axisBehavior`, `verticalBarEdge` and `systemTraitsAffectingVerticalBarEdge`,
   `preferredPlacement`, SwiftUI's `toolbarVerticalEdge`), behind run-time `#available` checks only, which do not hide
   a symbol an older SDK lacks. For the project: the change would fail its CI, against
   `Technotes/CodingGuidelines.md` line 149. Read from the SDK headers and the CI file, not from a build; Foldsmith
   0.8.1's `sdkguard.py` finds 11 lines Xcode 26.6 does not compile and 8 availability checks outside a compile-time
   guard ([foldsmith-0.8.1-check.txt](foldsmith-0.8.1-check.txt)). Fix: decision 5. The first run had the same defect.
2. **Upright, Share has left the article pane (L-L1; C-R2 merged in; medium; regression).** Laptop, tent and
   flat-upright: the split tiles the timeline at 0–340 pt and the article at 341–669 pt. `before/article-laptop.json`
   has Share `[603,884,41,38]`, enabled; in `after/article-{laptop,tent,flat-upright}.json` no button is labelled
   Share, the bottom bar ends with Reader View `[600,881,44,44]` with no overflow of its own, and the only overflow is
   the navigation bar's More `[481,28,40,40]`, which no capture opened. The empty detail beside feeds and timeline
   loses its Share (disabled there) the same way. For a reader holding the phone upright: sharing an article takes the
   timeline row's context menu, whose share sheet lacks the article sheet's Find in Article and Open in Browser
   (`WebViewController.swift:370`); if More does not hold Share, Find in Article has no route in these poses. Cause:
   tiling with `minimumSecondaryColumnWidth = 320` (`RootSplitViewController.swift:109`). Lowered from high: high only
   if More does not hold Share. The first run had the same (its L-L2).
3. **Upright, Previous Article and Next Article have left the top bar (L-L2; C-R2 merged in; low; regression).** The
   same poses: before, the detail's bar `[0,24,669,58]` held them at `[425,28]` (disabled) and `[481,28]`; after, it is
   `[341,24,328,58]` and holds the display-mode button, "enter full screen" `[365,28]` (the button that was "split view
   horizontally left" at `[24,28]`, relabelled since the timeline is now visible), and More `[481,28]`, in Next's slot.
   Presumably they are in More; no capture opened it. Closed, closed-sideways, book and flat-sideways still show both.
   For a reader: one more tap, or a tap on the timeline row beside the article, or Next Unread in the bottom bar. The
   first run had the same (its L-L1).
4. **Feeds T7 is left (L-L3; C-R5 merged in; medium; not a regression).** Book, laptop, tent, flat-sideways and
   flat-upright: unfolding from the feeds list shows no feeds list. In book the timeline pane is 0–475 pt with no title
   and no rows; in laptop it is 0–340 pt, empty, beside an empty detail. The cover's Feeds, Filter Read Feeds,
   Settings, Current Activity and Add are in none of the five inner poses; the way back to the list is Show Sidebar
   `[24,28]`. For a reader: one more tap after unfolding, as before the change, which had the same T7. The fix, "Three
   levels", is structural and was proposed in [PLAN.md](PLAN.md), not made (decision 2).
5. **On the turned cover, Reader View is behind More (L-L6; C-R3 merged in; low; not a regression).** Before, it
   floated at `[609,396,44,44]` outside the vertical bar (T10); after, the bar holds Back, Previous Article, Next
   Article, More (`BottomOverflowBarButtonItem`, `[29,251]`), Mark Article Unread and Star Article, and the shots'
   difference is the removed button. By the code (`ArticleViewController.swift:222–264`) it is in that More as a menu
   item; no capture opened it, so its presence, its on/off state and its action are unverified. For a reader: two taps
   where it took one; Next Unread and Share were already behind the same More. Not a regression, since its old place
   was the T10 defect.
6. **Nested sheets skip the sheet rule (C-R4; low, lowered from medium; not a regression).** The Import Theme picker
   (`ArticleThemesTableViewController.swift:34–37`), the feed inspector's home page
   (`FeedInspectorViewController.swift:177–179`) and the SwiftUI `.sheet` calls in `AddAccountView:97`,
   `CredentialsAccountView:83`, `CloudKitAccountView:46`, `DinosaursView:110/113`, `ErrorLogView:50`,
   `ActivityLogView:50`, `CloudKitStatsView:89`, `AccountStatsView:82` and `CurrentActivityView:56` never set
   `preferredPlacement`. All are presented from inside another sheet, and none is in the flow;
   `updateFoldAwareSheetPlacement` moves them only if the fold changes while they are up. For a reader in book: they
   likely open on the leading half, over the list (T15), as every sheet did before; inferred from the pattern page and
   T15's measured automatic placement, not captured.
7. **"Loading…" still lands on the fold (C-R6; low; not a regression).** Laptop and tent, while Reader View extracts
   an article: only "No selection" moved to 25 %. The "Loading…" placeholder (`ArticleRenderer.swift:193`) keeps the
   stylesheet's `.systemMessage` rule (`top: 45%`), which put "No selection" at y 444–469, across the fold at
   455.5–495.5. For a reader: a brief message on the fold, as before. Not captured.
8. **The empty page loads twice (C-R9; low; not a regression).** On a window with a fold and no article, `loadWebView`
   calls `self.view.layoutIfNeeded()` (`WebViewController.swift:658`) after inserting the web view and before setting
   its delegates and script message handlers; that layout reaches `redrawNoSelectionIfFoldChanged`, which renders the
   empty page, and the block renders it again at its end, so the first load runs without delegates. For a reader:
   nothing visible.
9. **NetNewsWire's contribution rules (C-R10; low; not a regression).** (1) The branch tracks five Foldsmith files
   under `.duo/` (PLAN.md, REPORT.md, audit.md, flows/NetNewsWire.flow, migration.json), which `.gitignore` does not
   exclude: they would ship in a pull request, and `CONTRIBUTING.md` takes no documentation PRs and no LLM-generated
   documentation. (2) All 11 commit subjects begin with "Duo:", where `Technotes/CodingGuidelines.md` line 167 asks for
   a present-tense verb. (3) `RootSplitViewController`'s new stored properties (lines 32–38) come after
   `viewDidAppear` (line 24), against "Properties go at the top". The LLM note is there: all 11 commits carry a
   `Co-Authored-By` line. (1) and (2) are what the skill told the run to do (decision 6). For a reader: nothing.

Merged into a confirmed item: C-R2 into L-L1 and L-L2, C-R5 into L-L3, C-R3 into L-L6. Refuted: L-L4 and C-R8, since
Add Feed's sheet is centred open flat and has its own horizontal bar, so text items are right there (the cell is
waived for that reason); L-L5, since the narrow article pane is the two-pane layout the decision table prescribes and
the link ellipsis is the theme's own CSS; L-L7, since the changes behind the sheets come from the split, which other
items cover; L-L8, since the commits after the settings and Add Feed captures cannot change those shots; C-R7, since
the idiom checks are unchanged from the baseline and no capture or route shows them causing a defect on the Duo. The
skeptic's summary line says 19 items checked and ten surviving; its own lists name the 18 above, and those counts are
used here, as in the README.

## Left to do
- The three regressions: the CI build first (C-R1; decision 5), checked with a build by the CI's Xcode; then Share
  (L-L1) and Previous and Next (L-L2) upright: find what the upright detail's More holds, on a device or with a driver
  that can open it, and choose what stays in the 328 pt pane (decision 1).
- Feeds T7 (L-L3): the structural proposal in [PLAN.md](PLAN.md), three levels in two panes, with its 104 routes, if
  the maintainers want it (decision 2). Foldsmith 0.8.1's P1 page adds six checks a structural three-level change must
  pass, from the first run's breakages ([README.md](README.md#what-foldsmith-081-changed-because-of-this-run)).
- The misses: open the turned cover's More (L-L6); the nested sheets (C-R4); "Loading…" (C-R6); the double load
  (C-R9); the contribution points (C-R10; decision 6).
- Capture settings, Add Feed and the article again from the branch's last code, and the sheets and screens listed under
  [Not checked](#not-checked).

## Reproduce
- The work is branch `duo-migration` (11 commits, ending at `d5cd9a2`), cut from `duo-baseline` (`20a90a6`: NetNewsWire
  d9ec7df with its secrets generated with no keys, by the baseline commit's message; the same baseline commit as the
  first run), in a copy of the app outside this repository.
- [NetNewsWire.patch](NetNewsWire.patch) is `duo-baseline..duo-migration` without `.duo/`, under NetNewsWire's MIT
  license ([LICENSE-MIT-NetNewsWire](LICENSE-MIT-NetNewsWire)). It applies cleanly to `duo-baseline`'s tree
  (`git apply --check`, 2026-11-02; `git apply` skips its three-line preamble), and so to d9ec7df: the eight files it
  modifies have the same blobs at d9ec7df as at `duo-baseline`, and the three it adds, under `iOS/Duo/`, do not exist at
  d9ec7df (by GitHub's API, 2026-11-02). To build it: check out d9ec7df, `git apply NetNewsWire.patch`, generate the
  secrets as the project's CI does (`./buildscripts/updateSecrets.sh`), and build the scheme NetNewsWire-iOS with
  Xcode 27.1 against the iOS 27.1 SDK.
  The run's first build of a fresh checkout failed while `SecretKey.swift` was being generated; the second built.
- The run used Foldsmith plugin 0.8.0 (commit `da6b941` of Foldsmith) through `/foldsmith-start` with its defaults
  (local changes only, no `--structural`), Xcode 27.1 (27A9275) with the iOS 27.1 SDK, and the iPhone Duo simulator
  (iOS 27.1), driven by the skill's bundled driver and `tools/capture-app.sh` with
  [NetNewsWire.flow](NetNewsWire.flow).
- Foldsmith 0.8.1's gate and toolchain check on this run, from 2026-11-01:
  [foldsmith-0.8.1-check.txt](foldsmith-0.8.1-check.txt).
