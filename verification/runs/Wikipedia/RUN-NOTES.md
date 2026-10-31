# Wikipedia run — notes for Foldsmith

For Foldsmith's own team, not for the app's maintainers: the capture record, what each review round found item by
item and what the run did about it, what in the skill was wrong, unclear or slow, and time and disk. The app-facing
report is [`REPORT.md`](REPORT.md).

This text was moved out of `REPORT.md` on 2026-10-31; the original is in this repository's git history. Facts corrected
on the way are marked "(corrected 2026-10-31)". "I" is the run's agent. The skill, its references and its tools are
described as they were on 2026-10-28, when Foldsmith's repository was still named `duo-patterns`; what Foldsmith took
from the run since is in the [README](README.md)'s "What this repository took from it". Ids used below: P1–P4 are
Foldsmith's patterns (P1 Fold-aligned split, P2 Reflowing columns, P3 Even grid, P4 Arrangement) and I1 the decision
table's rule for a screen's content relationship; C0–C21 (third round), E0–E22 and U0, U1, U3 (fourth round) are the
ids in the reviews' findings files, which are not published.

## Capture record
All shots and snapshots stay in the run's working copy (`.duo/captures/Wikipedia/`); none is published. Times are UTC
on 2026-10-28/29, from the log in [`migration.json`](migration.json) (which is in EDT, UTC−4), the `capture.txt`
headers and the shots' file times.

| Capture | When (UTC) | Screens |
|---|---|---|
| before | 18:32–19:10 | all eleven, seven poses each |
| before, again | 19:15–19:21 | contents (its first tap landed before the page loaded; the flow now waits 5 s) and tabs (opened to four tabs so the grid shows its columns) |
| after, the run's own check | 20:32–21:08 | all eleven |
| after, first review | from 22:06 | saved, search, activity, article, contents |
| after, second review | from 23:29, then from 00:02 | saved, search, activity, article, contents, community, settings; then article and contents |
| probes (the second round's builds) | 23:54–00:13 | the system overflow opened on the cover; an article opened from Home in every pose (`.duo/flows/Wikipedia-probes.flow`, not published) |
| after, third review | from 00:57, then from 01:22 | saved, search, activity, article, contents, settings; then article and contents |
| after, fourth review | from 02:24 | article and contents, replacing a run at 02:12 whose flow passed a boolean as a string (Skill problems) |

(Corrected 2026-10-31: the report gave the before capture as 19:14–19:53 UTC. 19:14 is the header of the contents and
tabs recapture, which overwrote `before/capture.txt`; the shots' file times put the first pass at 18:32–19:10.)

Only each screen's latest after-shots are kept: for-you, places, tabs and profile from the run's own check
(20:36–21:05), community from the second review (23:30), saved, search, activity and settings from the third
(00:58–01:18), article and contents from the fourth (02:25–02:31, from that round's first build, made at 02:11;
`3c66733` was built at 02:39 and committed at 02:40, after them). Earlier after-shots were overwritten, so the first
round's regression (the contents in a 372 pt pane breaking words) has no surviving shot.

The detector tables went through two versions. The run's first report (`4b8955f`) counted 21 cells with a finding
before and 10 after with the detectors of that time. The second round's table still came from those older copies (the
coordinator had not resynced the skill after the first review's fixes); the run reran the synced detectors over the
full before and after in the third round and again in the fourth, which gave the published tables (23 → 16 cells,
44 → 17 lines). No detector commit was recorded with them.

## Review rounds, item by item

| Round | Found by | Items |
|---|---|---|
| first | the coordinator, from the shots | 2 defects and a decision |
| second | an independent review (three reviewers by screen group, a skeptic per finding) | 10 |
| third | an independent review of the second round (C0–C21) | 22 |
| fourth | an independent review of the third round (E0–E22 confirmed; U0, U1, U3 plausible) | 23 confirmed |
| last check | two reviewers, one of the fourth round's code and one of the report, a skeptic each | 15, not fixed: `REPORT.md`, "Known regressions and open items" |

### First round
The coordinator looked at the first after-capture and found two defects I had passed:
1. **contents, book and flat sideways — a regression, not "cramped".** In the 372 pt detail pane the inline contents
   column was about 90 pt wide and broke words ("etymol/ogy", "Classifi-/cation"), and the article beside it was clipped
   at the pane's edge (the two T4 links). Before the migration both were readable. Cause: the detail pane gave the
   article the window's regular size class, so the app chose its inline contents, and the stack inset the article by
   the bar's 84 pt on top. Fixed in `9c259a0` and `75d98fe`: the overlay, and the full pane for the article (web view
   496–867 pt, the contents 0–300 pt over the window). Closing and opening again keeps the contents open; upright the
   app's inline contents are unchanged. (The overlay was replaced in the second round.)
2. **saved, search, activity — the list stopped 84 pt short of the fold.** The search field 20–371 pt, the segmented
   control 16–355 pt, cells and separators to 371 pt in a pane that ends at 455 pt. Fixed in `75d98fe` for everything
   but the navigation bar's search field (still 20–371 pt; `REPORT.md`, "Left to do"): segmented control 16–439 pt,
   rows and separators 0–455 pt, Activity's card text to 404 pt.
3. Not a defect, decided: the blank detail pane. The pattern puts an empty detail's placeholder at the top of the pane
   (`fold-aligned-split.md`, Consequences; the catalog's sits at y 202–222 pt); the pane now says "No article selected"
   there.

My own look at the first after-capture missed both: I read the contents as "cramped" from a 300 px contact sheet and
did not measure the list's controls against the pane.

### Second round
Independent reviewers checked every screen and pose against the snapshots; all confirmed, all fixed or kept in `todo`
with what was tried:
1. **Search results beside the article ended at 371 pt** (content views [0,y,371,61], subtitles cut with an ellipsis):
   the results are the search controller's, presented over the search screen, not a screen of the stack, so the inset
   cancellation did not reach them. Now it covers what a stack's screens present inside the stack: results 0–455 pt,
   text to 435 pt (`328cb92`).
2. **The list's navigation bar** (search field 20–371, capsule to 417): not reached; see `REPORT.md`, "Left to do", for
   what was tried.
3. **The W in the detail pane did nothing** (popToRoot on a one-screen stack); it now takes the tab back to its list and
   empties the detail pane, as it returned Search to its root before (`0ffa2ce`). Not tappable headless.
4. **Contents in book and flat sideways**: the overlay covered the list and blurred the article. The contents now take
   the list pane, in their inline form, beside the readable article in the detail pane (`03e1d80`): contents [0,0,455,669],
   article 496–867 pt. Open on the cover, they come with the article into the pane; closing brings them back as the
   overlay. Upright unchanged.
5. **The contents button upright was a solid disc** (`isSelected` on a plain item): back to the outline icon.
6. **Article actions behind the overflow**: the app's More now joins the system overflow where bars are vertical
   (`additionalOverflowItems`), contents and save have high priority, reading themes and search low, the tab bar
   compresses first (`.prefersBarItems`), and the tab bar hides beside an open article as it did full screen; the overflow
   capture on the cover shows every action one tap away (`probes/overflow-closed-cover.png`). The search item had no
   title, so it showed as an icon-only row in the overflow; titled (`00db16f`).
7. **Community's "In the news" footer ended 7 pt below its cell**: card heights were measured on a placeholder with its
   default margins; now with the live cell's 15 pt (`4ee89e9`). Every card's content ends inside its cell (book: 925 ≤
   940 pt).
8. **Settings rows ran across the fold** (T5 per the taxonomy's own corpus case; the audit called it clean): classified
   hierarchical, made a Fold-aligned split (`aa81129`): the list 0–435 pt, "No setting selected" beside it until a page
   opens. (Reverted in the third round, `a04b528`.)
9. **Activity, logged out, has nothing to open.** Decided by the skill: the relationship comes from the model (Activity
   logged in is a timeline of articles, hierarchical), not from what the screen shows now, and unsplit the login card's
   text crosses the fold again (T5). It stays split; its empty pane now says "Articles you open from your activity
   appear here".
10. **The container's background did not follow the theme**: it does now (`328cb92`).

Report corrections from the second round: the Account sheet's extent in book (483.5–943 pt, Close in its own vertical
bar), the W claim (checked against `probes/article-home-book-inner.png`), the bar items that moved into the list pane's
top bar, the overflow per pose, For You's T1, and the Even grid's costs.

### Third round
An independent review of the second round confirmed 22 findings (C0–C21), several of them regressions on devices with no
fold. What I did:
- **C12** state restoration lost the open article with Saved, Activity or Search selected (any iOS 27.1 device):
  `NavigationStateController` now unwraps the container (`aa1085b`).
- **C13, C1** theme: the pass now follows a presented container's two stacks; Settings no longer presents one.
- **C16** tab bar carried hidden into another tab: re-evaluated on every selection change and tap, tabs that are their
  own stack left to UIKit (touched only to undo).
- **C18** the Search item's title and priority gated on iOS 27.1 (`962e2f2`).
- **C20** the More actions in the system overflow are disabled while the toolbar is.
- Every other walk of the hierarchy checked: listed in `REPORT.md`'s appendix.
- **C0, C15, C21, C2** Settings' split: reverted (`a04b528`). Its pages are driven by a dozen coordinators that push and
  pop one stack, the donation reminder pops it, and two routes present it; making pushes from a detail page stack on the
  detail, pops reach it and every route split is a rewrite of Settings' navigation, not a pattern applied, and the skill
  does not allow ending a screen worse than it started. Settings' T5 is back in `todo`; C2 no longer applies.
- **C14** the contents follow the detail pane's top screen: another screen taking the pane removes them, and they come
  back when their article is on top again (`aa1085b`, `962e2f2`). Checked by code path (links cannot be tapped on the
  inner display).
- **C4** the list the contents cover is hidden from VoiceOver: the synced detectors no longer read it (contents' list
  pane: only the contents, 0–455 pt).
- **C19** the lead image no longer jumps: 512–851 pt in the detail pane with the contents open or closed, book and flat
  sideways (`962e2f2`, `162a1c6`).
- **C8** contents from Home or Places: in `todo` with the article.
- **C17** the `.savedPages` and `.activity` activities, `switchToSearch` and a second tap on the tab go through the
  container's `popToRoot`.
- **C6** the W beside the list says where it goes ("Wikipedia, return to Search"; its bar-item title names the list), and
  the report says Back is gone there.
- **C3, C5, C7, C9, C10, C11** report corrections: Search's top-bar items, the two-tap actions and the Even grid's
  125/126 pt gutters, 85 pt short of the fold, descriptions not titles, the detail pane's top bar and profile's flat T5.
- Detector tables rewritten from the synced tools over the full before and after; `progress.py done --remove` took back
  the stale Settings, saved, search, article, contents and profile lines.

### Fourth round
The third review confirmed the round-3 fixes and found 23 smaller items (E0–E22 confirmed; U0, U1 and U3 plausible —
corrected 2026-10-31: the report said "U0–U3", but the findings file has no U2). What I did:
- **E5, E15** (medium) what the router opens from an article in the detail pane went onto the list's stack (in the list
  pane, or under open contents): the router's `currentNavigationController` now returns the tab's front stack, the
  detail stack while it shows an article (`4e0f095`). Covers More › Talk page, the footer's talk link, user, talk and
  in-app web links and the editor's cancel. (The last check found the other side of this: screens the list pane opens
  through the router now land in the detail pane when an article is open there.)
- **E6** `visibleArticleViewController()` reads the front stack: a deep link to the open article scrolls it, the return
  from the web editor refreshes it in place, the Search quick action pushes Search onto it. (The last check: the tab
  bar then reappears beside Search.)
- **E7, E18** the evergreen prompt and the language-variant alerts count the tab as at its root only with nothing open
  in the detail pane (`isCurrentTabAtRoot`).
- **E8** the imported reading list goes back to Saved's root through the container (detail emptied, contents gone)
  before it is pushed; Notifications Center from a push notification goes on the front stack. (The last check: its
  "already showing" check still reads the list's stack.)
- **E0** for Home and Places, the re-check on a selection change no longer leaves the bar hidden: it only clears a hide
  the app set (`setTabBarHidden(false)`), and UIKit's `hidesBottomBarWhenPushed` does the rest, as on main.
- **E2, E12, E17** Edit is built when the More menu opens (a deferred element), so the disabled copy dropped it; it is
  now a disabled "Edit" with its pencil while the toolbar is disabled (`3c385e8`).
- **E9** the hide-contents item's filled rounded square is drawn into its image (a plain item has no background view).
  Measured as the review did, exact #F8F9FA pixels in the button's box: contents laptop 10,884 before, 10,918 after;
  tent 10,937 / 10,907; flat-upright 10,886 / 10,918; the article control (contents closed) 34 / 34. (Corrected
  2026-10-31: the article control in tent counts 60 / 60, not 34 / 34; laptop and flat-upright are 34 / 34. Every count
  here is exact #F8F9FA pixels in x 60–204, y 2640–2778 px of the 3x shots, ends excluded; the article control's before
  and after are equal in every pose. The last check also found the icon on the fill about 18 % smaller and dark grey.)
- **E10** plain items keep the labels the old buttons had: themes reads "font size" again in the five inner poses
  (snapshots), the hide item "Hide table of contents". (Corrected 2026-10-31: the report said "in every pose"; on the
  cover the themes item is in the system overflow, and no snapshot labels it there.)
- **E1** open contents beside the list carry over to the next article: show and hide write the app's preference, and a
  new article's first showing reads it, as the inline contents did on main. The capture flow now starts with the
  preference off (it was off in the before capture), so a shot shows contents only when its own taps open them. (The
  last check: the read runs on every entry into the pane, so an unfold covers the list with contents — medium.)
- **E3** `NavigationStateController`'s helper and `saveNavigationState` are main-actor. (The last check: E3 also named
  `ArticleTableOfContentsDisplayController`, which is still not.)
- **E4** the push override's comment no longer mentions a Settings page.
- **E11** Existing behaviour (now "Devices without a fold" in `REPORT.md`) no longer lists the outer display under "no
  change"; it points at the bars and costs there.
- **E13, E20** contents' book and flat-sideways cells are no longer "overlay in the Search tab's detail pane"; they are
  findings (T14, the detail pane's top bar), and contents left `done`; laptop, tent and flat-upright are verified
  (toolbar), not "verified in all seven poses".
- **E14, E19** the two stale Skill-problems bullets are corrected: `progress.py` has `done --remove` (it is in
  `references/simulator.md`; I missed it in the first pass), and the covered list is hidden from VoiceOver, so the
  snapshot holds only the contents.
- **E16** Activity's list capsule and the contents' detail top bar stop short like Saved's and the article's: activity
  and contents book and flat-sideways are findings (T14) in `todo`; activity left `done`.
- **E21** article and contents laptop, tent and flat-upright are "verified (toolbar: plain items)", not "unchanged".
- **E22** Places' T1 is the search field; its T14 line is hidden list text (a false hit).
- **U0** (plausible: confirmed by reading) the contents came back on `viewWillAppear` or a trait change whichever
  screen topped the detail stack. They now come back only when their article is the top screen. (The last check: this
  does not hold during a back swipe that is later cancelled, because the article is already the top screen then; only
  the trait-change case is fixed.)
- **U1** (plausible: confirmed by reading) `setTabBarHidden(_:animated:)` ran outside the transition, so the bar slid in
  at the start of a back swipe and snapped back after a cancel. It now runs in the transition coordinator's
  `animate(alongsideTransition:)`, so it follows the push, pop or swipe, and a cancelled swipe restores the previous
  state. Not checked with a swipe (no swipe in the tap runner).
- **U3** (plausible: confirmed by reading; the report called it "U2, U3") the plain-tab "undo" could leave an explicit
  hide on a stack whose screen hides the bar. Same fix as E0: the app clears only its own hide and never sets one for
  Home or Places.
- Not in the findings, found by grepping every reader of the list stack again: the router's watchlist target pushed
  onto the list (now the front stack), and a second tap on the tab checked only the list's count, which misses a talk
  page now stacked in the detail pane (now counts the detail pane) (`3c66733`). (The last check found one more reader
  of the list stack: the tabs overview's `tappedTab`.)
- Rebuilt; article and contents recaptured in all seven poses (14 cells, no "lost the screen"); no other screen's
  drawing changed. Detectors rerun over the full before (44 lines) and after (17 lines): the same cells as the third
  round. After, per changed cell: article laptop, tent, flat-upright: themes "font size" at [551,884,40,38], contents
  [25,884,38,38]; contents the same with "Hide table of contents" and the fill; lead image 512–851 pt in the detail pane
  with the contents open or closed (book, flat-sideways), as in the third round.

### After the fourth round
The last check confirmed 15 items, none refuted; the person running the run chose to stop there rather than start a
fifth round. Each code item is in `REPORT.md`, "Known regressions and open items", with its route and where to start;
the report slips it found are corrected in `REPORT.md` (its Corrections note) and above.

## Skill problems
What in the skill, its references or its tools was wrong, unclear or slow on this run, and what I did instead. Nothing
in Foldsmith's repository (then `duo-patterns`) was edited by the run.

- **The decision table and `even-grid.md` disagree about this very feed.** I1 says a feed whose tap opens the item's
  own page ("an article into its text") is hierarchical, and in the same sentence lists "an Explore grid" as peer;
  `even-grid.md` uses Wikipedia's Explore as its corpus "before" for P3, while its "When not to use it" sends "a list of
  articles" to P1. Explore's cards mostly open articles. I followed the text that names this feed: community → Even
  grid (logged in `audit.md`). IceCubesApp's run reported the same ambiguity.
- **P1 has no route for a UIKit app without a split view.** The page's UIKit section adjusts an existing
  `UISplitViewController`. Wikipedia's tabs are plain navigation stacks in a `UITabBarController`, and 24 call sites
  push articles onto them through `ArticleCoordinator`. A `UISplitViewController` expands at any regular width, so it
  would also split Saved, Activity and Search on an iPad and on the upright inner display, where the app has no finding.
  I built a small container whose panes come only from reserved regions, a departure from "prefer system containers"
  (logged). What it had to handle that the page does not mention: the tab bar's `hidesBottomBarWhenPushed` and its
  pop-to-root on reselect only work for a navigation controller that *is* the tab; a screen that reappears when its
  article moves to the other pane takes that for a return (Search raised the keyboard across both panes and lost its
  results one fold later); a compact trait for the narrow panes flips trait-driven layouts on every unfold, because the
  window is regular for a moment before the regions arrive (tried and dropped); and the detail pane must stop at the
  trailing safe area or its web view runs under the vertical bar (found by T4, fixed).
- **P2's reference does not fit the usual article view.** `ReflowingColumns.swift` lays out plain paragraphs with
  TextKit. Wikipedia's article is a `WKWebView`; contents, find in page, scroll restoration and the native lead image all
  use vertical offsets, and the page itself says images and tables need their own rule. Left in `todo`.
- **No pattern for a map.** I1 calls a map continuous; the continuous row leads to Reflowing columns, whose Context is
  text read in order. Places → `todo` "no pattern yet". (The skill as published now has a map rule: SKILL.md step 3.)
- **The scans and detectors missed the worst defect.** The T10 scan looks for text-only items; Apple's rule is
  "text-only *and custom-view* items never go vertical". The article's six `UIBarButtonItem(customView:)` buttons (app
  subclass `IconBarButtonItem`) stayed a horizontal bar on the cover and were not drawn at all on the sideways inner
  display. No scan line and no detector reported it (T7 did not see six buttons vanish); found by looking at the shots
  and snapshots.
- **T13's plist glob misses target-named plists.** `rg -l 'UIApplicationSceneManifest' -g 'Info.plist'` finds nothing
  here; the manifest is in `Wikipedia/Wikipedia-Info.plist` (and three more `*-Info.plist`). The other two T13 lines hit,
  so the verdict held. Used `-g '*.plist'`.
- **T7 detector, the other way.** It reports the contents as lost in laptop, tent and flat upright because the cover's
  overlay has a Close button and the inner display's inline sidebar does not; the contents stay open and the toolbar
  hides them. A presentation change; not counted (and not recorded with `progress.py waive`, which step 6 asks for).
- **`same-screen.py` and maps.** Places' map loads its pins after the taps and each pin counts as fixed content, so every
  return to the cover scored 0.33–0.35: Places was relaunched before every inner pose, five "the shut phone lost the
  screen" lines per capture, all false (every shot shows Places), and about a minute more per pose.
- **T1, T14, T4 false hits.** T1 counts map annotations (17 pt pins, cluster counts) as fixed controls in laptop and
  tent; a map pans. T14 reported a Places "column 100–542" from hidden list text, nothing drawn there. T4 counts web-view
  links and scroll indicators near the window's edge as controls. Each read against the shot; listed in `audit.md`.
- **A reachability check that passes but misses.** Step 1's closed capture passed for `contents`, but the full capture
  landed on the plain article: the Table of Contents button is disabled until the page loads, and a tap on a disabled
  button "succeeds". The flow now waits 5 s; `contents` was captured again.
- **A subset recapture rewrites the capture record.** `SCREEN=… capture-app.sh … before` re-runs the detectors over the
  whole folder (good) but overwrites `capture.txt`, losing the first pass's lines (Places' "lost the screen"); I had
  copied them aside. Step 2 asks for exactly this recapture. (Fixed in the synced tools: `capture.txt` now gets a
  "recaptured:" block appended.)
- **Content a defect needs.** The tabs grid only shows T14 with three or more tabs, and Saved was empty; the skill says
  nothing about seeding. I saved two articles and opened three tabs through the app with the tap runner before the
  audit (logged), since the app's own reading-list seed needs the `UITEST` build flag.
- **Navigation stacks inset their screens for the vertical bar wherever they sit.** In UIKit every screen of a
  `UINavigationController` gets a trailing safe-area inset the width of the vertical bar when the bar is on that edge,
  even when the stack's own frame ends far from it (the stack's view reads 0, its screens 84 pt) — and its navigation
  bar's search field keeps it with no public setting I found. The pattern page's UIKit section and the reference files
  (SwiftUI `NavigationSplitView` / a system `UISplitViewController`, which handle it themselves) do not mention it; any
  custom pane container on the Duo hits it. Cancelled per screen with negative `additionalSafeAreaInsets`.
- **Size-class layouts in a narrow pane.** A pane narrower than the outer display still has the window's regular size
  class, so the app's own regular-width layouts (Wikipedia's inline contents) land in a 372 pt pane; and a compact trait
  for the pane flips them on every unfold, because the window is regular for a moment before the reserved regions
  arrive. The page says nothing about what a pane's screens should be told; I decided per screen from the pane.
- **A presentation does not survive its presenter moving between stacks.** The contents overlay presented on the cover
  stayed attached but undrawn after the article moved into the detail pane, which blocked presenting it again; it has
  to be dismissed first. Worth a line wherever the page says "move the detail screens between the stacks".
- **Neither the detectors nor my look caught the review's defects.** No class covers content that stops short of its
  pane (84 pt of blank before the fold) or a column too narrow to set words; T4 caught only two clipped links. The skill
  says "look at every shot", and a contact sheet is not enough: measure controls against their pane in the snapshot.
- **Speed.** As documented: 39 min for 77 cells (about 30 s a cell), taps a few seconds, no 60 s SpringBoard stalls.
  A build beside a capture slowed the capture noticeably; I stopped doing that. Each one-screen probe took 3–6 min.
- **The skill's reference copy was behind its source.** SKILL.md (step 5) sends a UIKit app to "the page's UIKit
  section … with or without a split view controller", and the second review pointed at "UIKit without a split view" in
  `references/patterns/fold-aligned-split.md`; the skill's copy had no such section. It existed only in the repository's
  `patterns/fold-aligned-split.md` (the sync script was not run for that file). Read it there. (The skill's copy has it
  now.)
- **`progress.py`'s `--remove` is easy to miss** (corrected in the fourth review; the first pass said it did not exist).
  `todo --remove <prefix>` and `done --remove <prefix>` are in `references/simulator.md` and the tool's usage, but
  SKILL.md's list of its commands (`phase`, `screen`, `cell`, `todo`, `done`, `log`) left them out; I found them only
  in the third round and used them then and in the fourth. (SKILL.md lists them now.)
- **Taps reach only the cover.** The overflow menu in book, the W in the detail pane and the contents in the list pane
  cannot be tapped headless; and an overflow screen opened on the cover closes on the next fold, so the capture reports
  "the shut phone lost the screen" (a false T7) for it. Checked on the cover and from snapshots instead.
- **A screen inside a custom pane may not carry `verticalBarEdge`.** The article in the detail pane kept the app's More
  item because its own trait read unspecified; reading the window root's trait works.
- **What the accessibility tree shows under an accessory.** Until the third round, with the contents over the list pane
  the snapshot also held the list beneath them, and a detector reading frames saw both. The covered list is now hidden
  from VoiceOver (`accessibilityElementsHidden`), so the snapshot holds only the contents. The page could say that a
  pane's accessory must hide what it covers.
- **The new step-6 rule worked as a checklist.** Measuring every pane's controls against its edges in the snapshots
  (a small script) found what contact sheets hid: content stopping short of the fold, text clipped at a pane's edge,
  a card's footer below its cell.
- **A container on every iOS 27.1 device multiplies the walks to check.** Wrapping tab stacks in a container that is
  invisible without a fold still changes the hierarchy everywhere: state restoration, the theme pass and the tab bar
  broke on devices with no fold until each walk was unwrapped. The pattern page's "UIKit without a split view" says to
  unwrap the container where the app casts a tab; it should say to grep every walk (`as? UINavigationController`,
  `selectedViewController`, `presentedViewController`, `viewControllers`) and to re-evaluate the tab bar on every
  selection change.
- **A split is only as good as the routes into it.** Settings had two presentation routes and a dozen coordinators that
  push and pop one stack; splitting one route and redirecting pushes broke Back and Confirm. The page could say when not
  to split: a list whose pages navigate on their own.
- **The tools I ran in the second round were out of date** (the coordinator's note): with the synced copies Settings'
  T5, the list pane's search field (T14) and the covered list under the contents showed up, and map pins stopped
  counting.
- **A boolean launch argument must be written as plist** (fourth review). `capture-app.sh` and
  `capture-corpus-flow.sh` describe `LAUNCH_ARGS="A B"` as launch arguments, and they reach `NSUserDefaults` through the
  argument domain: `-Key NO` arrives as the string "NO". Wikipedia reads its contents preference with
  `object(forKey:) as? NSNumber`, so the string was ignored, the default (open) applied, the article screen opened with
  its contents, and the capture logged "the shut phone lost the screen" for the contents screen. `-Key <false/>` works.
  The flow docs could say so.
- **Flows share the app's saved state.** A preference one screen's taps write (open contents) changes the next screen's
  shot, and a relaunch does not reset it; the flow format has no reset step. Set with a launch argument. (A side effect
  found by the last check: with the preference held off, no capture can show the medium regression that depends on its
  default.)
- **Which stack is "current" depends on what is shown.** The third round unwrapped every walk to the list stack; the
  fourth review found routes that should land on the detail stack while it shows an article (the router, link
  handling, the visible article, root checks). "UIKit without a split view" could say: give the container a front
  stack and an at-root test, and route by them. (The last check showed the front stack is not enough: route by where
  the action comes from; the split page now says so.)
- **A change the detectors and a contact sheet cannot see.** The open-contents fill (#F8F9FA on glass) was lost in the
  second round; neither the detectors nor my look saw it. The review found it by counting exact pixels in the button's
  box before and after, which is what I used to confirm the fix.

## The skill's rule on `todo`
SKILL.md step 6 says "`todo` holds what is left, never what the run broke". Through the fourth round every confirmed
regression was fixed or its change reverted (Settings' split). The last check's regressions were not: the person
running the run stopped after it, so they are in `REPORT.md` and not in `migration.json`'s `todo`, which was written
before the last check. By the skill's rule the run is not finished; `progress.py check` passes only because no
detector reads these defects (most are on routes no capture reaches).

## Time and disk
- 14:11–17:15 EDT, about 3 h 5 min: audit about 75 min; migration and probes about 75 min; after capture 37 min; report
  about 15 min. First review 17:17–18:26, about 1 h 10 min. Second review 18:30–20:20, about 1 h 50 min (the 49-cell
  and 14-cell recaptures about 35 min). Third review 20:50–21:35, about 45 min (the 42-cell and 14-cell recaptures
  about 30 min). Fourth review 22:06–22:50, about 45 min (two 14-cell recaptures, about 8 min each, the first replaced;
  two builds). In all about 7 h 35 min of work over 8 h 40 min. (Corrected 2026-10-31: the report said 7 h 45 min; its
  own parts add up to 7 h 35 min.)
- One derived data folder, `~/Library/Caches/duo-wikipedia` (3.0 GB, steady). Free space went from 12 GB at the start to
  2.1–2.8 GB in the third round (the coordinator's reviews ran beside the run); in the third round I removed only my own
  temporary probe folders in the scratchpad (about 0.3 GB). In the fourth round free space read 2.1 GB at every check
  (before and after each build, capture and detector run); it never went under the 2 GB stop line, and I deleted
  nothing.
