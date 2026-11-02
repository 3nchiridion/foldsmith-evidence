# NetNewsWire, migrated hands-free a second time (2026-11-01)

The second hands-free run on NetNewsWire, with the Foldsmith plugin 0.8.0 (commit `da6b941` of
[Foldsmith](https://github.com/3nchiridion/foldsmith)); the first is in [`../NetNewsWire/`](../NetNewsWire/). One prompt
(`/foldsmith-start`), no questions, run by a Claude Code agent with the plugin's `foldsmith-duo` skill and its bundled
simulator driver, on the project's iPhone Duo simulator (iOS 27.1, Xcode 27.1), from 23:10 on 2026-10-31 to 00:48 on
2026-11-01, local time. The app was a fresh copy of NetNewsWire at d9ec7df with the same `duo-baseline` commit as the
first run (`20a90a6`, by "duo-run", the secrets generated with no keys); the skill committed its work on `duo-migration`
there, 11 commits ending at `d5cd9a2`. It ran with the plugin's defaults: local changes only, structural ones proposed
and not made (plugin 0.7.0's rule; no `--structural`).

What it could lean on, and what not. No person decided anything for it, and it asked nothing. It could not start a
subagent, so the review the skill's step 6 asks for was done by the run itself. It was not a stranger's first run: the
skill's references already held what the first run had measured on this app (the 320 pt detail minimum, "No selection"
at 25 %, the Add Feed gate, Reader View with `.verticalPreferred`, the three-level recipe; 26 lines of `SKILL.md` and
its references name NetNewsWire at `da6b941`), and the copy's `origin` remote was the first run's folder, so the first
run's branch was one `git show` away. Whether the agent read it cannot be shown; the rerun's code is laid out
differently.

| File | What it is |
|---|---|
| `REPORT.md` | the run's report, corrected on 2026-11-02 (its "Corrections" lists what and why): status at a glance with the confirmed regressions first, a glossary, per screen the pattern and the state of each pose, what changed, the controls that moved, what runs on devices without a fold, the decisions left to NetNewsWire's maintainers, what was not checked, the known misses and regressions the independent review confirmed, and what is left |
| `RUN-NOTES.md` | for Foldsmith's team: when things happened, timing, the run agent's 12 frictions and what plugin 0.8.1 made of each, where the references and the SDK differ, and what the independent review and the corrections say about the skill |
| `audit.md` | the run's audit of the unmodified app and the classification of each screen (the simulator's UDID left out) |
| `PLAN.md` | the run's plan: the five local changes, each marked with its commit, and the structural proposal it did not make (feeds, three levels in two panes) with the 104 lines `tools/routes.sh` lists for it |
| `migration.json` | the progress file at the end of the run (what the Foldsmith pane reads), byte for byte but for the simulator's UDID |
| `NetNewsWire.flow` | the screens the run found and the taps that reach them, with the launch arguments that pin state restoration |
| `detectors-before.md`, `detectors-after.md` | the run's own `duo-detect` tables (plugin 0.8.0) on the 35 snapshots before and after: 29 marks in 19 cells before, 6 in 6 after |
| `NetNewsWire.patch` | the code change, `duo-baseline..duo-migration` without `.duo/` (NetNewsWire is MIT) |
| `LICENSE-MIT-NetNewsWire` | the license of the patch: NetNewsWire's `LICENSE` at d9ec7df, unchanged |
| `commits.txt` | the branch's 11 commits, newest first: the report, the proof, two fixes made after the first proof capture (`8e7c888`, `523be11`), the plan's update, five code commits, then the audit and plan |
| `foldsmith-0.8.1-check.txt` | Foldsmith 0.8.1's gate (`progress.py check`) and toolchain check (`sdkguard.py`) on this run, run on 2026-11-01 |

Result of the run, from migration.json: five screens, all under the Fold-aligned split (P1). Timeline, article,
settings and Add Feed are `done`; feeds is left in `todo` (T7 in the five inner poses: unfolding from the feeds list
shows an empty timeline beside "No selection" and no feeds list; the fix, three levels in two panes, is structural and
is proposed in `PLAN.md`, not made). Of the 35 cells, 17 are verified (changed and re-checked), 13 fine as is and 5
left. One of the 17, Add Feed flat-sideways, has the same snapshot before and after and keeps its T10, waived (the
sheet is centred there and has a horizontal bar of its own), so strictly 16 cells were changed and re-checked. The
run's detectors went from 29 marks in 19 cells (34 lines) to 6 marks in 6 cells (7 lines: feeds T7 in five poses, and
Add Feed flat-sideways T10, waived); no pose got a class it did not have. Plugin 0.8.0's `progress.py check` passed,
with no open issue.

Timing: 1 h 37 min 29 s in one pass, from 23:10:27 to the report's commit at 00:47:56. Captures: the five screens on
the cover first (6.6 min), the audit (16.7 min, 35 cells, about 28.6 s a cell), the proof (16.6 min), and two
recaptures after two late fixes (9.4 and 6.0 min); the six probe captures took 13 min 20 s more (the agent's own
estimate is about 19 min). `RUN-NOTES.md` has the detail.

The run's own report overclaimed. It gives "done: verified in every pose" as the state of timeline (5 of its 7 cells
verified, 2 fine as is), settings (1 and 6) and Add Feed (4 and 3), the wording the first run's corrections took out;
it says the sheet rule is set "before every sheet the app presents", while about a dozen sheets presented from inside
another sheet never get it; it states as fact that Previous Article, Next Article and Share are in the upright article
pane's More, which no capture opened; and it does not mention that the change does not build with the Xcode the
project's CI selects. The independent review below confirmed 3 regressions; none of them is fixed on this branch.
`REPORT.md` is corrected, and its "Corrections" lists every change.

The run's own shots and snapshots stay outside the repository, as for every run. No App Store set is published either
(the run exported 18 JPEGs to `.duo/appstore/`, out of git).

## Independent review (2026-11-01)

After the run, Claude Code agents with no part in it reviewed the branch. Two worked blind: a layout reviewer, from the
before and after shots and accessibility snapshots, and a code reviewer, from the diff and the source at both
revisions. What exactly they were kept from is not recorded beyond "blind"; their items cite the progress file's cell
states (L-L1, L-L3). A skeptic then checked each item against the `duo-baseline..duo-migration` diff, both source
revisions, the before and after snapshots and shots, the iOS 27.1 SDK headers and Foldsmith's rules. Nothing was built
with Xcode 26.6: only 27.1 is installed, and the review was not allowed to build. Their notes and scripts are not
published.

They raised 18 items (layout 8, L-L1 to L-L8; code 10, C-R1 to C-R10). The skeptic confirmed 9, 3 of them regressions:
1 high (C-R1), 1 medium (L-L1, lowered from high) and 1 low (L-L2); the other 6 are not regressions: 1 medium (L-L3)
and 5 low (L-L6, C-R4 lowered from medium, C-R6, C-R9, C-R10). Three repeated a confirmed item and were merged into it
(C-R2 into L-L1 and L-L2, C-R5 into L-L3, C-R3 into L-L6), and 6 were refuted: L-L4 and C-R8, because the flat-sideways
sheet is centred and has its own horizontal bar, so text items are correct there, and the cell is already waived for
this reason; L-L5, because the narrow article pane is the two-pane layout the decision table prescribes, and the link
ellipsis is the theme's own CSS; L-L7, because the changes behind the sheets come from the split, which other items
already cover; L-L8, because the commits after those captures cannot change the settings or Add Feed shots; C-R7,
because the idiom checks are unchanged from the baseline, and no capture or route shows them causing a defect on the
Duo. The skeptic's summary line says 19 items checked and ten surviving; its own lists name the 18 items above, 9
confirmed, 3 merged and 6 refuted, and those are the counts used here.

Confirmed, most severe first. Coordinates are `[x, y, width, height]` in points from the snapshots, or `[x, y]`;
`before/` and `after/` are the run's captures, not published. `REPORT.md` (Known misses and regressions) has each with
what it means for the app's users.
- High, regression (C-R1). The iOS target cannot compile with the Xcode the project's CI selects:
  `.github/workflows/ci.yml:87–88` select Xcode 26.6 for the "iOS Simulator Tests" job, which runs on every pull
  request and builds the scheme NetNewsWire-iOS. The new code uses symbols only the iOS 27.x SDK has: `reservedRegions`
  (`UIView.h:760/763`, iOS 27.1; `DuoFold.swift`), `UIHingeInteraction` (iOS 27.1; `RootSplitViewController.swift:56`),
  `axisBehavior` (`UIBarButtonItem.h:179`, iOS 27.1), `verticalBarEdge` and `systemTraitsAffectingVerticalBarEdge`
  (`UIVerticalBarEdge.h`, iOS 27.1), `preferredPlacement` (`UISheetPresentationController.h:120`, iOS 27.0) and
  SwiftUI's `toolbarVerticalEdge`, guarded only by run-time `#available` checks; the baseline's one 27 check
  (`Mac/AppDefaults+Mac.swift:250`) is a version test only. `Technotes/CodingGuidelines.md` line 149 forbids code that
  triggers compiler errors. Skeptic: the availability annotations verified in the installed iPhoneOS27.1.sdk headers
  and the SwiftUICore interface, and Xcode 27.1 ships Swift 6.4; `#available` does not hide symbols an older SDK lacks.
  Not built with 26.6. Fix: move CI to Xcode 27.1, or guard at compile time (a plain `#if compiler(>=6.4)` may not be
  enough if Xcode 27.0 also ships Swift 6.4 without the 27.1 symbols). The first run had the same defect.
- Medium, regression (L-L1; C-R2 merged in). Upright (laptop, tent, flat-upright) the split tiles the timeline at
  0–340 pt and the detail at 341–669 pt (328 pt), and the article's Share button is no longer on screen:
  `before/article-laptop.json` has Share `[603,884,41,38]`, enabled; in `after/article-{laptop,tent,flat-upright}.json`
  no button is labelled Share, the detail's toolbar ends with Reader View `[600,881,44,44]`, there is no bottom
  overflow, and the only overflow is the navigation bar's More (`TopOverflowBarButtonItem`) `[481,28,40,40]`, which no
  capture opened. The empty detail beside feeds and timeline loses its Share (disabled there) the same way. Share may
  be inside that More, or back once "enter full screen" gives the detail the full width; neither is verified. The
  timeline row's context menu still shares, but that sheet has no application activities, while the article's carries
  Find in Article and Open in Browser (`WebViewController.swift:370`), so Find in Article has no proven route in these
  poses. Cause: tiling with `minimumSecondaryColumnWidth = 320` (`RootSplitViewController.swift:109`). Skeptic: holds
  in the snapshots and in `after/article-laptop-inner.png` (read, star, next unread and Reader View in the bottom bar,
  no Share), and Share is missing in all nine upright after cells of article, feeds and timeline; lowered from high,
  since sharing remains possible from the timeline row and Share may sit in the unopened More: high only if More does
  not hold it.
- Low, regression (L-L2; C-R2 merged in). In laptop, tent and flat-upright, Previous Article and Next Article are no
  longer in the detail's top bar. Before, the bar was `[0,24,669,58]` and held them at `[425,28]` (disabled) and
  `[481,28]`; after, it is `[341,24,328,58]` and holds the split's display-mode button, "enter full screen" `[365,28]`
  (the same system button that was "split view horizontally left" at `[24,28]`, relabelled because the timeline is now
  visible), and More `[481,28]`. They are presumably inside More; no capture opened it. Closed, closed-sideways, book
  and flat-sideways still show both. One more tap; the timeline beside the article and Next Unread in the bottom bar
  are one-tap alternatives. Skeptic: confirmed in `after/{article,feeds,timeline}-{laptop,tent,flat-upright}.json` and
  the before and after laptop shots, with one correction: More takes Next's slot, and "enter full screen" is not new.
  Low, since one-tap alternatives are on screen.
- Medium, not a regression (L-L3; C-R5 merged in). Feeds T7 is left: unfolding from the feeds list still shows no
  feeds list on the inner display. In book the timeline pane is 0–475 pt with no title and no rows; in laptop it is
  0–340 pt, empty, beside an empty detail; the only way back to the list is Show Sidebar `[24,28]`. The cover's Feeds,
  Filter Read Feeds, Settings, Current Activity and Add appear in none of the five inner poses. The P1 page's "Three
  levels" puts the sidebar beside the detail; the decision table classes that change as structural, and it was not
  made. Skeptic: confirmed from `after/feeds-book.json` (a "Timeline" bar `[0,24,475,58]`, no text, no rows),
  `after/feeds-laptop.json` and `after/feeds-{laptop,book}-inner.png`; the before captures have the same T7.
- Low, not a regression (L-L6; C-R3 merged in). On the turned cover (closed-sideways) Reader View is no longer on
  screen. Before, it floated at `[609,396,44,44]` outside the vertical bar (T10); after, the vertical bar shows Back,
  Previous Article `[29,147]`, Next Article `[29,199]`, More (`BottomOverflowBarButtonItem`) `[29,251]`, Mark Article
  Unread and Star Article, and the pixel difference's box, (603,394)–(659,442) pt, is the removed button. The code sets
  `axisBehavior = .verticalPreferred` and a `UIDeferredMenuElement` `menuRepresentation` while `verticalBarEdge` is set
  (`ArticleViewController.swift:222–264`), so Reader View should be in that More; no capture opened it, so its
  presence, its on/off state and its action are unverified. Two taps where it took one, the cost of fixing T10 as
  Apple's rule prescribes; Next Unread and Share were already behind the same More. Skeptic: not a regression, since
  the old one-tap position was itself the T10 defect.
- Low, not a regression (C-R4; lowered from medium). The sheet rule covers only some presentations: the Import Theme
  picker (`ArticleThemesTableViewController.swift:34–37`, `.formSheet`), the feed inspector's home page
  (`FeedInspectorViewController.swift:177–179`, an `SFSafariViewController`, `.pageSheet`) and the SwiftUI `.sheet`
  calls in `AddAccountView:97`, `CredentialsAccountView:83`, `CloudKitAccountView:46`, `DinosaursView:110/113`,
  `ErrorLogView:50`, `ActivityLogView:50`, `CloudKitStatsView:89`, `AccountStatsView:82` and `CurrentActivityView:56`
  never set `preferredPlacement`, so in book they open with automatic placement, which the pattern page says falls
  back to the leading half (T15). Sibling routes set it (Settings' OPML import and export pickers and Safari links);
  `updateFoldAwareSheetPlacement` moves the others only if the fold changes while they are up. All are sheets
  presented from inside another sheet, and none is in the flow. Skeptic: the omissions confirmed by grep against
  `duo-migration`; the leading-half outcome inferred from the pattern page and T15's measured automatic placement, not
  captured; lowered, since these routes are secondary; not a regression, since every sheet had automatic placement
  before.
- Low, not a regression (C-R6). Only "No selection" moved to 25 %: the "Loading…" placeholder shown while Reader View
  extracts an article (`ArticleRenderer.swift:193`, `<h3 class='systemMessage'>`) keeps `stylesheet.css`'s
  `.systemMessage` rule (`top: 45%`), which put "No selection" at y 444–469 in the before captures of laptop and tent,
  across the fold at 455.5–495.5. Skeptic: confirmed in the code (the processing branch renders `loadingHTML` with no
  override); transient, as before the change; not captured.
- Low (C-R9). On a window with a fold and no article, `loadWebView` calls `self.view.layoutIfNeeded()`
  (`WebViewController.swift:658`) after inserting the web view and before setting its delegates and script message
  handlers; that layout reaches `redrawNoSelectionIfFoldChanged`, which renders the empty page, and the block renders
  it again at its end: the empty page loads twice, the first time without delegates. No visible effect. Skeptic: the
  sequence checks out in the `duo-migration` source; nothing user-facing is worse.
- Low, not a regression (C-R10). NetNewsWire's contribution rules: (1) the branch tracks five Foldsmith working files
  under `.duo/` (PLAN.md, REPORT.md, audit.md, flows/NetNewsWire.flow, migration.json), which the baseline does not
  and `.gitignore` does not exclude; they would ship in a pull request, and `CONTRIBUTING.md` refuses documentation
  pull requests and LLM-generated documentation; (2) all 11 commit subjects begin with "Duo:", where
  `Technotes/CodingGuidelines.md` line 167 asks for a present-tense verb; (3) `RootSplitViewController`'s new stored
  properties (lines 32–38) come after `viewDidAppear` (line 24), against "Properties go at the top". The LLM note is
  there: all 11 commits carry a `Co-Authored-By` line. Skeptic: (1)–(3) confirmed with `git ls-tree`, `git log` and
  the source; a point on private extensions dropped (the codebase keeps private functions in class bodies), and the
  ticket and forum step dropped (outside the repository). Two of the three, the tracked `.duo/` files and the "Duo:"
  subjects, are what the skill told the run to do.

## Compared with the first run

| | First run (2026-10-27, with its same-day follow-up) | This run (plugin 0.8.0) |
|---|---|---|
| Screens done / left | 5 / 0; the first pass left feeds in `todo`, and a follow-up fixed it after Foldsmith's owner made the three-level decision | 4 / 1 (feeds) |
| Cells verified / fine as is / left | 19 / 16 / 0; 4 of the 16 (Add Feed's) had in fact changed | 17 / 13 / 5; one of the 17 has the same snapshot before and after |
| Detectors, before → after | the run's: 3 → 0; republished with later detectors: 21 marks in 15 cells → 0; Reader View's T10 in 8 cells in neither | the run's: 29 marks in 19 cells (34 lines) → 6 in 6 (7 lines); no class new to any pose |
| Wall time | 11:16–13:00 (1 h 44 min), then a follow-up logged 19:29–20:49 (1 h 20 min; when its code work began is not logged): at least 3 h 04 min over two sessions, with a person's decision between them | 23:10:27–00:47:56 (1 h 37 min 29 s), one pass, no human input |
| Capture time a cell | about 65 s in the audit and the proof; about 26 s in the follow-up's recapture, one test run per screen | about 28.6 s |
| Code changed | 5 files, +331 −26; `SceneCoordinator` +166 −17 (the stack moves), new `FoldAlignedSplit.swift` +73 | 11 files, +359 −41; `RootSplitViewController` +96, three new files in `iOS/Duo/` (109 lines), `AddFeedView` +55 −40 (mostly re-indentation), `SceneCoordinator` +16 (width-save guards and sheet placement) |
| `.duo/` on the branch | left out of the published patch | 5 files, +859, so 16 files and +1218 −41 in all |
| Commits | 7, one without an LLM note | 11, each with a `Co-Authored-By` line |
| Review: raised / confirmed | 24 / 15 | 18 / 9 |
| Regressions confirmed | 9: 2 high, 5 medium, 2 low | 3: 1 high, 1 medium, 1 low |

Regressions, 9 → 3. Six of the first run's are gone, all from its feeds follow-up `2b2e1dc`, which moved the split's
navigation stacks: Mark All as Read bringing up an empty column (C-R2, high), Back closing the article (L-L4), a push
and a pop in one turn (C-R3), outside routes losing the feed they open (C-R4), unfolding dropping a Search All (C-R5)
and the edge swipe bringing up the empty column (C-R8). They are gone because plugin 0.7.0's local-changes-only rule
kept that change out: it was not made better, it was not made, and the cost is feeds T7, left in 5 cells. Three
remain. Two come from what the decision table still prescribes, two panes upright with a 320 pt detail minimum: Share
off the upright article pane (the first run's L-L2, this run's L-L1, medium) and Previous and Next behind More (the
first run's L-L1, this run's L-L2, low). The third is the CI build (C-R1 in both runs, high): NetNewsWire's CI uses
Xcode 26.6, and a search of the 0.8.0 skill and its references finds no rule about the CI's toolchain or compile-time
guards. No new regression was confirmed. The run made one of its own, Reader View in the turned cover's overflow with no
title (I1), and fixed it in `523be11`, checked through the code, not by a tap.

The first run's misses that were not regressions: Reader View's T10 in 8 cells is fixed (on the turned cover it is in
the overflow: two taps where it took one, L-L6); the overlay on a launch directly upright is addressed in the code,
which now sets the detail minimum in `viewWillLayoutSubviews` before `super` (no launch directly upright was captured);
Add Feed's symbols are gated on `toolbarVerticalEdge`, not on iOS 27.1 alone; the sheet rule reaches far more sheets,
with about 12 nested ones still without it (C-R4); "Loading…" on the fold (C-R6) and the contribution-rule points
(C-R10) are still there.

What went to `PLAN.md` instead: one structural proposal, feeds in three levels in two panes. It is the stack-swap
design the first run committed as `2b2e1dc`, which the P1 page's UIKit section still prescribes; its risk list names
the routes where the first run's regressions came from (Mark All as Read's `markAsReadAndShowSidebar`, state
restoration, keyboard navigation, user activities, notifications, shortcuts, the iPad), and its routes table has 104
lines from `tools/routes.sh`.

The caveat: the references were written from the first run on this same app, and the first run's branch sat in this
copy's `origin`. The comparison shows that the lessons held on this app, not that a stranger's first run on a new app
would go as well.

## What Foldsmith 0.8.1 changed because of this run

Plugin 0.8.1, [commit `851b0cf`](https://github.com/3nchiridion/foldsmith/commit/851b0cf) on Foldsmith's `main`,
summarised in its [CHANGELOG](https://github.com/3nchiridion/foldsmith/blob/main/CHANGELOG.md) ("Plugin 0.8.1"):
- a new `tools/sdkguard.py`: the Xcode the project's CI selects, the Xcodes installed, and every iOS 27 use added
  since the base outside `#if compiler(>=6.4)`, an error when the CI is below Xcode 27 (C-R1); Foldsmith's reference
  implementations now sit whole inside that guard, and the report gets a "Builds with the project's toolchain" line;
- the gate (`progress.py`) refuses: a control gone from a cell with no `moved` record backed by a snapshot of that cell
  or a tracked line of code (L-L1, L-L2, L-L6); a `verified` cell that keeps its before class, unless waived (a hole
  found after the run: feeds' five T7 cells, marked `verified` on a copy, passed 0.8.0's check, `done feeds` and
  `phase done`); a done line saying "verified in every pose" when not every cell is; a cited commit not on the branch
  (I3's note cites `87584b9`, the commit before an amend); an after shot captured from other code than the code now
  (settings' and Add Feed's after shots predate `523be11` and `8e7c888`, article's `8e7c888`); and `phase done` with no
  review on record;
- the review becomes a step with a record: `references/review.md`, `progress.py review`, and `/foldsmith-review`,
  which runs it in a new session when the run cannot start a subagent;
- the migration never commits `.duo/`, follows the project's contribution rules and commit style, and never amends a
  cited commit (C-R10);
- T10 checks an item only in a bar that reaches the vertical bar's band, so a centred sheet keeps its own horizontal
  bar: Add Feed flat-sideways is no longer marked T10, before or after, and Add Feed in book before keeps only T15 (the
  published tables are the run's own, from plugin 0.8.0);
- the Fold-aligned split page: bar items in the 328 pt upright detail (`visibilityPriority`, `menuRepresentation`; SDK
  API, not yet measured on a run), the sheet rule for every sheet route including nested sheets (C-R4), every
  transient message off the fold (C-R6), and six checks a structural three-level change must pass;
- reference fixes for the frictions the run reported (`RUN-NOTES.md`): `LAUNCH_ARGS` quoting, apps that restore their
  last selection, a first build that fails on a generated source, zsh and `rg`.

0.8.1's checks on this run, in [foldsmith-0.8.1-check.txt](foldsmith-0.8.1-check.txt):
- The gate, `progress.py check` on a copy of the run's `.duo/` (no git repository): exit 1. It refuses for 37 controls
  gone with no record (Previous Article, Next Article, Share and the display-mode button "split view horizontally
  left", which L-L2 shows was relabelled "enter full screen", in each of the nine upright cells of feeds, timeline and
  article, and Reader View on the article's turned cover), for three done lines saying "verified in every pose"
  (timeline 5 verified and 2 unchanged, settings 1 and 6, Add Feed 4 and 3) and for no independent review on record.
  Run again in a clone of the branch with the same snapshots, it also refuses I3's note for citing `87584b9`, not on
  the branch (it names `8e7c888`); the captures predate 0.8.1, so their code identity is noted, not checked.
- The toolchain, `sdkguard.py` on a clone of the branch with `--base origin/duo-baseline --scheme NetNewsWire-iOS`:
  exit 1. The CI selects Xcode 26.6 (`.github/workflows/ci.yml:43` and `:88`) for the schemes NetNewsWire and
  NetNewsWire-iOS; the only Xcode installed is 27.1 (27A9275); 11 lines do not compile with Xcode 26.6 and 8
  availability checks sit outside `#if compiler(>=6.4)`, in `ArticleViewController.swift`, `DuoFold.swift`,
  `FoldAwareSheets.swift`, `VerticalBarReader.swift` and `RootSplitViewController.swift`.

Not changed by 0.8.1: the empty page's double load (C-R9), a local fix for feeds T7 (L-L3), and opening an overflow
the tap runner cannot reach (taps reach only the upright cover; a moved control is proven by a line of code instead).
