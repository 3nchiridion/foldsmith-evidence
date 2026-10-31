# Wikipedia on iPhone Duo — migration report

Edited on 2026-10-31, after the run, to correct and restructure it: see [Corrections](#corrections). The run's own
text is in this repository's git history.

## At a glance

| | |
|---|---|
| Status | **Not ready to merge: 2 medium and 6 low known regressions, and 2 incomplete fixes**, all found by the last check, after the last capture. 2 of 11 screens done |
| For an iPhone Duo user | Held open like a book or flat sideways, Saved, Search and Activity show their list on the left half and the article opened from them on the right, with its contents on the left when open. Explore's cards and the tabs overview put the fold in the gutter between their columns. The Account sheet opens on the right half in book. On the outer display the article's toolbar joins the system's vertical bar, and in book the W no longer sits on the fold. Still across the fold: articles opened from Home or Places, Settings, For You, Places' search field, and the Account sheet's text when flat sideways. The split panes' top bars stop short of their edges |
| Coverage | 11 screens (logged out) × 7 poses = 77 cells: **13 verified** (fixed or changed, and re-checked), **47 fine as is**, **17 left** with a finding. Screens: 2 done (community, tabs), 9 in `todo` |
| How it was checked | A shot and an accessibility snapshot per cell, before and after; Foldsmith's detectors (cells with a finding 23 → 16, lines 44 → 17, from the detectors as synced in the third review round; the run's first report counted 21 → 10 with older copies); the run's own look; a look by the coordinator, three independent reviews and a last check ([How it was checked](#how-it-was-checked)) |
| Not run | The app's tests, its Experimental and Alpha targets, any device but the Duo simulator, a Swift 6 strict-concurrency check ([Not checked](#not-checked)) |
| Known regressions | Contents cover the list on unfold (medium); the random-article shortcut opens in the list pane (medium); six low regressions and two incomplete fixes ([list](#known-regressions-and-open-items)) |
| Decisions for the maintainers | An invisible container around Saved, Activity and Search, and plain titled toolbar items, both on every iOS 27.1 device; actions that now take two taps on the outer display; narrower grid columns; whether to take all of the change or part ([Decisions](#decisions-for-the-apps-maintainers)) |
| The change | wikipedia-ios 2031b89, `main..duo-migration`: 19 files, +1284 −66, 28 commits ([`Wikipedia.patch`](Wikipedia.patch), [`commits.txt`](commits.txt), [`audit.md`](audit.md)). Notes for Foldsmith's own team: [`RUN-NOTES.md`](RUN-NOTES.md) |

## Glossary
- **Poses**: `closed` (the outer display, "the cover", upright) and `closed-sideways` (the cover turned sideways); on
  the inner display `book` (part-folded, held sideways), `laptop` and `tent` (part-folded, the fold running across),
  `flat-sideways` and `flat-upright` (open flat).
- **pt**: points, UIKit's layout unit. The inner display held sideways is 951 × 669 pt.
- **The fold**: the inner display's division, a reserved region along the crease, 455.5–495.5 pt along the folded axis,
  margins included. Active while part-folded, inactive while flat; vertical in book and flat-sideways, horizontal in
  laptop, tent and flat-upright.
- **Vertical bar**: the system bar on a side edge that takes a screen's bars wherever the window has one (the cover,
  book, flat-sideways). Its **overflow** is the system's More menu in that bar.
- **List pane, detail pane**: the two halves of the split: the list 0–455 pt, the detail from 496 pt.
- **The W**: Wikipedia's home button in the article's top bar.
- **Cell**: one screen in one pose. **Verified**: it had a finding or the change touched it, and the run judged the
  after capture right (the last check later found the icon wrong in three contents cells: note ᶜ). **Fine as is**: no
  finding before or after (`unchanged` in `migration.json`). **Left**: a finding remains.
- **T-classes** (Foldsmith's [taxonomy](https://github.com/3nchiridion/foldsmith/blob/main/patterns/TAXONOMY.md)):
  T1 crease-straddle (a control or text on the fold), T4 under-system-bar (content under the system's bar), T5
  stretched-single-column (one text column across the fold), T7 state-loss-on-fold (what the cover shows is gone on the
  inner display), T10 stranded-horizontal-item (a toolbar item left horizontal where bars go vertical), T14
  columns-off-fold (columns, panes or a pane's content not aligned to the fold), T15 sheet-in-book (a sheet on the
  leading half in book).
- **Patterns** ([pages](https://github.com/3nchiridion/foldsmith/tree/main/patterns)): Fold-aligned split (list and
  detail on either side of the fold; its **sheet rule** puts sheets on the trailing half in book), Even grid (half the
  columns on each side, the fold in the gutter), Reflowing columns (reading text in two columns, one each side),
  Arrangement (a view and the one that supports it, such as an article and its contents).
- **Detectors**: Foldsmith's checks that read each accessibility snapshot for the T-classes.
- **The coordinator**: the session that started the run and later ran the reviews.

## Corrections
Made on 2026-10-31 against the published files, the run's working copy and the README's "Last check":
- Added this summary, the glossary, "Known regressions and open items" (the last check's items, which the README gave
  and this report did not), "Not checked", and how the run stands against the skill's `todo` rule.
- Removed "None of the others is worse than before": true of every capture (`progress.py check` finds no pose clean
  before with a finding after), not of the routes the last check read.
- Removed the random-article shortcut from the routes "checked and unaffected": it opens in the list pane (item 2).
- "Verified" now means a cell `migration.json` marks verified. The old Result table called the other poses of search,
  saved and activity "verified"; they are fine as is. The article's cover cells, labelled "Reflowing columns (bars)" in
  `migration.json`, changed only the bars: Reflowing columns was not built.
- The inner display held upright does have a reserved region: a horizontal division, which the app does not read. Its
  layout is unchanged there for that reason, not for want of a region.
- The themes item reads "font size" in the five inner poses, not "in every pose": on the cover it is in the overflow.
- Before capture 18:32–19:10 UTC (contents and tabs again 19:15–19:21), not 19:14–19:53; time total about 7 h 35 min,
  not 7 h 45 min. Both are now in RUN-NOTES.md, with a pixel count and a review id corrected there.
- Moved to RUN-NOTES.md: the capture record, the review rounds item by item, "Skill problems", "Time and disk".
- Replaced the per-pose tables of shot paths, which point at files not published, with one grid built from
  `migration.json`; linked the detector tables instead of copying them.
- Places: the skill as published now has a rule for maps; at the run it had none.

## Known regressions and open items
The last check (two reviewers, one of the fourth round's code and one of this report, each with a skeptic) confirmed 15
items, none refuted: ten defects in the code, below (two of them reported by both reviewers), and slips in this report,
corrected above. None is fixed. Most need a tap on the inner display, the contents preference left on (every capture
holds it off), a quick action or a notification, which no capture here reaches; the person running the run chose to
stop after the last check. Unless the route says otherwise, each happens in book and flat sideways with Saved,
Activity or Search selected. Lines are on `duo-migration` at `065557e`.

| # | Severity | What happens | Route | Where to start |
|---|---|---|---|---|
| 1 | medium, regression | Unfolding with an article open covers the list pane with its contents whenever the app's "contents visible" preference is true, which it is by default and which closing them on the cover no longer clears | open an article on the cover, unfold. Every capture starts with the preference off (`-TableOfContentsIsVisibleInline <false/>` in [`Wikipedia.flow`](Wikipedia.flow)), so none shows it | `ArticleTableOfContentsDisplayController.swift:196` reads the preference on every entry into the detail pane, not only for a new article; `NSUserDefaults+WMFExtensions.swift:275-280` returns true when unset |
| 2 | medium, regression | The random-article shortcut opens the article in the 455 pt list pane, in place of the list | the Random home-screen quick action or the `.random` user activity (`WMFAppViewController.swift:1385-1386`, `:1444-1446`) | `RandomArticleCoordinator.swift:52` puts the article in place with `setViewControllers`; the container sees only pushes (`WMFComponentNavigationController.swift:71-80`) |
| 3 | low, regression | A second push-notification tap stacks a second Notifications Center | an article open beside the list, two notification taps | `WMFAppViewController+Extensions.swift:304`: the "already showing" check reads the list's stack, while `:240` pushes onto the detail stack |
| 4 | low, regression | Closing the tab whose article is open beside the list leaves the detail pane blank, with no placeholder and the tab bar still hidden | the tabs overview, close that tab | `WMFAppViewController+Extensions.swift:1009-1037` (`debounceRemoveArticlesForDeletedTabParts`) removes the detail stack's only screen |
| 5 | low, regression | Tapping the current tab in the tabs overview re-creates its article instead of closing the overview | the overview opened from the list pane, that tab's article beside it | `TabsOverviewCoordinator.swift:227` compares with the list stack's last screen |
| 6 | low, regression | The Search quick action pushes Search over the open article, and the tab bar reappears beside it | an article open beside the list | `WMFAppViewController.swift:1378-1380`; `WMFFoldSplitContainerViewController.swift:137-142` (`detailHidesTabBar`) looks only at the detail's top screen |
| 7 | low, regression | Screens the list pane opens through the router (Activity's diffs and contributions) stack on an unrelated article in the detail pane when one is open, and open in the list pane when none is | Activity logged in (not captured) or Search | the front stack follows what the detail pane holds, not where the action came from: `WMFFoldSplitContainerViewController.swift:126-128`, used by `WMFAppViewController.swift:2326-2342` |
| 8 | low, regression | The open-contents icon drawn on its restored fill is about 18 % smaller and dark grey instead of black (the last check's measurement) | laptop, tent, flat-upright with the contents open, on any iOS 27.1 device; also the hide item in the vertical bar | `ArticleToolbarController.swift:411-424` (`openContentsImage`) |
| 9 | low, incomplete fix | A cancelled back swipe can still bring back the previous article's contents | a screen above an article in the detail pane, a back swipe that is cancelled | `ArticleTableOfContentsDisplayController.swift:186-191`: the check at `:189` is already true during an interactive pop; it covers a trait change only |
| 10 | low, incomplete fix | `ArticleTableOfContentsDisplayController` reads main-actor state (the container, `topViewController`) without isolation | code | `ArticleTableOfContentsDisplayController.swift:11`, `:189` |

The severities are the last check's; "regression" is this report's word for items 1–8, each of which behaves worse than
main on its route. The last check classed 1, 3 and 8 as regressions, 2 and 4 as bugs, 5 as a remaining defect, 6 and 7
as unreported behaviour changes, and 9 and 10 as fixes not done.

**Against the skill's own rule.** SKILL.md step 6 says "`todo` holds what is left, never what the run broke". Items 1–8
are regressions the run made, and 9–10 are fixes it left half done in code it added; by that rule the run is not
finished, and each must be fixed, or the change behind it reverted, before a merge. They are not in `migration.json`'s
`todo`, which was written before the last check.

What they share: the container decides where a route goes by what the detail pane holds, and several of the app's
routes reach a stack without it (`setViewControllers`, a coordinator's own stack, a check that reads the list).
Foldsmith's split page now says to route by where an action comes from and to cover every way onto a stack
([`fold-aligned-split.md`](https://github.com/3nchiridion/foldsmith/blob/main/patterns/fold-aligned-split.md), "UIKit
without a split view").

## Screens and poses
Every cell's state in [`migration.json`](migration.json): ✓ verified · = fine as is · ✗ left, with its T-class; "eye"
marks a finding no detector reported. Totals: 13 ✓, 47 =, 17 ✗.

| Screen: where (what was applied) | closed | closed-sideways | book | laptop | tent | flat-sideways | flat-upright | Status |
|---|---|---|---|---|---|---|---|---|
| community: Home › Community, the Explore feed (Even grid) | = | = | ✓ | = | = | ✓ | = | done |
| tabs: the tabs overview, four article tabs (Even grid) | = | = | ✓ | = | = | ✓ | = | done |
| profile: the Account sheet (Fold-aligned split, sheet rule) | = | = | ✓ | = | = | ✗ T5 (eye) | = | `todo` |
| saved: Saved › All articles (Fold-aligned split) | = | = | ✗ T14 | = | = | ✗ T14 | = | `todo` |
| search: the Search tab, its history (Fold-aligned split) | = | = | ✗ T14 | = | = | ✗ T14 | = | `todo` |
| activity: the Activity tab, logged out (Fold-aligned split) | = | = | ✗ T14 (eye) | = | = | ✗ T14 (eye) | = | `todo` |
| article: "Moon", opened from Search (bars, and Search's split) | ✓ ᵃ | ✓ ᵃ | ✗ T14 | ✓ ᵇ | ✓ ᵇ | ✗ T14 | ✓ ᵇ | `todo` |
| contents: the article's contents, opened from Search (classed Arrangement, which had no page then; put in the list pane beside the article) | = | = | ✗ T14 (eye) | ✓ ᶜ | ✓ ᶜ | ✗ T14 (eye) | ✓ ᶜ | `todo` |
| for-you: Home › For You, empty logged out (none applied) | = | ✗ T4 (eye) | ✗ T5 | = | = | ✗ T5 | = | `todo` |
| settings: the Account sheet › Settings (split reverted) | = | = | ✗ T5 | = | = | ✗ T5 | = | `todo` |
| places: the Places tab (none: a map) | = | = | ✗ T1 | = | = | = | = | `todo` |

- The finding behind each ✓, from [`detectors-before.md`](detectors-before.md): community and tabs book T14; profile
  book T15; article closed and closed-sideways T10 (ᵃ). Community's and tabs' flat-sideways cells had none; the Even
  grid keeps clear of the inactive division too, so they changed. Article and contents laptop, tent, flat-upright: ᵇ, ᶜ.
- ᵃ The toolbar's items joined the cover's vertical bar (T10 before). `migration.json` labels these cells
  "Reflowing columns (bars)"; only the bars changed.
- ᵇ No finding before; the toolbar's items became plain titled items, a change on every iOS 27.1 device
  ([Devices without a fold](#devices-without-a-fold)), so the cells count as verified, not fine as is.
- ᶜ As ᵇ, with the contents open inline as before. The detector reports T7 in these cells (the cover's overlay has a
  Close button the inline contents do not need); the run judged it a false hit, as SKILL.md step 2 allows, but recorded
  no waiver, which step 6 asks for. The last check later found the open-contents icon here smaller and grey (item 8).
- Articles opened from Home or Places are not in the grid (the flow opens the article from Search): one column across
  the fold in book and flat sideways (T5), from probe captures only.

Shots and snapshots are not published. In the run's working copy they are
`.duo/captures/Wikipedia/{before,after}/<screen>-<pose>-{cover,inner}.png` (`cover` for the two closed poses) with
`<screen>-<pose>.json` beside each; `migration.json` names every cell's pair. The probes are in `…/probes/`.

## Decisions for the app's maintainers
Choices the run made on the app's behalf:
1. **An invisible container around Saved, Activity and Search on every iOS 27.1 device**, fold or not
   (`WMFAppViewController.swift:415-419`); it splits only where a vertical division appears. It changes the view
   hierarchy everywhere: every walk of it the run found had to be unwrapped or routed
   ([appendix](#appendix-walks-of-the-hierarchy-the-container-changes)), and items 2–7 come from routes that reach a
   stack around it or that route by what its detail pane holds.
2. **Plain titled toolbar items on every iOS 27.1 device** in place of the custom-view buttons
   (`ArticleToolbarController.swift:46-51`), so that they can join a vertical bar. VoiceOver labels are kept; Save's
   long press becomes a one-item menu; the open-contents icon differs (item 8). The skill notes that keeping the custom
   views with `axisBehavior = .verticalPreferred` changes less; this run did not use it.
3. **Actions that now take two taps on the outer display**, in the system overflow ([Costs](#costs)).
4. **Narrower columns** in Explore and the tabs overview in book and flat sideways, with descriptions truncated in the
   tabs overview ([Costs](#costs)).
5. **All of it or part.** Profile's sheet rule (`cd5c34e`), community (`0c288f1`, `4ee89e9`) and tabs (`5a84010`) stand
   apart: community and tabs touch only their own files, and community uses `WMFFold.swift` from `cd5c34e`. The
   container, article and contents commits are interleaved across the review rounds and do not come apart one screen at
   a time. Taking only part was not tried.

## Left to do (`todo` in `migration.json`)
- **saved, search, activity, article, contents — T14 in book and flat sideways: the top bars of both panes keep the
  vertical bar's inset.** The list pane's navigation-bar search field stops at 371 of 455 pt, or 316 with Cancel
  beside an article (saved, search, article; detector; `searchField [20,82,351,44]` in `after/saved-book.json`); the
  list's top-bar buttons end at 413 pt and their capsule about 18 pt short of its margin (saved, search, activity; eye);
  the detail pane's top bar stops 38–42 pt short of its edge, the W at 512 and its last item ending at 825 of 867
  (article, contents; eye). UIKit gives a navigation bar the vertical bar's trailing inset wherever its stack sits.
  Tried: the bar's layout margins, the search bar's margins, a vertical-bar opt-out for the list's stack, a negative
  `additionalSafeAreaInsets` on the stack, a `UINavigationBar` subclass reporting no trailing inset; none moves them.
  Everything else in both panes reaches its edges. Start at `WMFComponentNavigationController.swift:97`
  (`updateVerticalBarInsetCancellation`), which cancels the inset for each screen of a pane but not for its bar.
- **article and contents opened from Home or Places — T5 in book and flat sideways.** Reflowing columns is not built:
  the article is a `WKWebView`, and contents jumps, find in page, scroll restoration and the native lead image use
  vertical offsets. There the article is one 827 pt column across the fold (`probes/article-home-book-inner.png`), its
  contents open in the app's inline form beside it, across the fold, and the W moves into the vertical bar while the
  book fold is active. Start from Reflowing columns'
  ["When the text is a web page"](https://github.com/3nchiridion/foldsmith/blob/main/patterns/reflowing-columns.md).
- **settings — T5 in book and flat sideways, as before.** The split (`aa81129`) was reverted (`a04b528`): Settings'
  pages are pushed and popped on one stack by a dozen coordinators, the donation reminder pops it, and the `.settings`
  and `.appearanceSettings` routes present it; splitting it means rewriting Settings' navigation
  (`SettingsCoordinator.swift`).
- **for-you — T5 in book and flat sideways, T1 in book, T4 closed sideways.** Fold-aligned split not applied: the Home
  tab holds For You and Community in one stack, so a split for the tab would undo Community's grid. Logged out the feed
  is empty and only its end card was captured; its illustration (x 434–539, not in the accessibility tree) sits on the
  fold in book.
- **profile — T5 flat sideways, as before.** The centred Account sheet's text column (186–760 pt) crosses the inactive
  division: the sheet rule centres sheets while flat, and no pattern page says how a sheet's own content should
  reflow. Book is fixed.
- **places — T1 in book.** The search field spans the fold (20–867 pt). The detector's T14 there (a column 100–480 pt)
  is hidden list text, a false hit, not recorded as a waiver. At the run the skill had no rule for a map ("no pattern
  yet"); as published it has one (SKILL.md step 3: the map stays whole, what sits on it moves off the fold), not
  applied here.

## What changed
`main..duo-migration` without `.duo/` ([`Wikipedia.patch`](Wikipedia.patch); MIT, as wikipedia-ios is,
[`LICENSE-MIT-Wikimedia`](LICENSE-MIT-Wikimedia)): 19 files (16 Swift sources, 3 localization tables), 1284 lines added,
66 removed; two new Swift files in the WMFComponents package, `WMFFold.swift` and
`WMFFoldSplitContainerViewController.swift` (no Xcode project edit). 28 commits ([`commits.txt`](commits.txt)): the
audit, 22 code commits (one a revert) and 5 report commits; the review-round commits span several screens.
- **saved, activity, search → Fold-aligned split**, the largest change: `1067adb`, `b790031` (Search kept as it was
  beside the article), `26cf97a` (a second tap on the tab pops the list); first review `75d98fe` (the bar's 84 pt inset
  cancelled in both panes, an empty-pane placeholder); second `328cb92` (search results, the tab bar beside an article,
  theme, Activity's copy); third `aa1085b` (every walk of the hierarchy unwraps the container); fourth `4e0f095` (what
  is opened from the detail pane stacks there, root checks that count the detail pane, the imported reading list, the
  tab bar with the transition, main-actor fixes) and `3c66733` (two more walks: the watchlist route, a second tap on
  the tab).
- **article: toolbar and W off the fold** (also contents): `6e8b992`; second review `0ffa2ce` (overflow, priorities,
  contents button, W) and `00db16f` (the Search item titled for the overflow); third `962e2f2` (contents follow their
  article, margins, the W's label, the Search item's gate, the overflow disabled with the toolbar); fourth `3c385e8`
  (Edit disabled, not missing, in the merged overflow; the open-contents fill and VoiceOver labels; open contents carry
  over between articles; contents come back only on top).
- **contents beside the article, in the list pane**: `03e1d80` (second review), replacing the overlay of `9c259a0`
  (first review); `162a1c6` (third: the lead image's margin kept in step).
- **profile → the sheet rule**: `cd5c34e`. `WMFFold.swift` reads the fold from
  `UIView.reservedRegions(kind: .division, options:)` behind `#available(iOS 27.1, *)`. While a vertical division is
  active every sheet presented from a `WMFComponentNavigationController` or the root gets
  `preferredPlacement = .trailing`, otherwise `.automatic`; sheets already up follow the fold. In book the Account
  sheet runs from 483.5 to 943 pt, under the vertical bar's band, with its own vertical bar holding Close (before, on the
  leading half at 8–467.5 pt: T15); flat it is centred.
- **community → Even grid**: `0c288f1`; `4ee89e9` (second review: card heights measured with the live cell's margins).
- **tabs → Even grid**: `5a84010`.
- **settings → Fold-aligned split**: `aa81129` (second review), reverted by `a04b528` (third).
- New strings: `fold-split-detail-empty`, `fold-split-activity-detail-empty`,
  `fold-split-home-button-accessibility-label` (en and qqq written by the app's build);
  `fold-split-settings-detail-empty` went with the Settings revert.

## Devices without a fold
The layout changes read only a vertical division (`WMFFold.swift:29`, and `WMFArticleTabsView.swift:545-547` for the
tabs grid), so layout does not change on an iPhone, an iPad or the Duo's inner display held upright (its division is
horizontal). Before iOS 27.1 nothing changes, by the code (not run): `WMFFold` and the tabs grid report no fold, and
the container and the plain toolbar items are not created (`WMFAppViewController.swift:415`,
`ArticleToolbarController.swift:47`). What does change on iOS 27.1:
- **Any iOS 27.1 device, the article toolbar**: plain titled items. They keep the VoiceOver labels the old buttons took
  from their image names (reading themes: "font size"); Save's long press shows a one-item menu, "Add to reading list";
  with the contents open inline the hide item keeps its filled rounded square, drawn into its image (its icon differs:
  item 8). Where bars are vertical the app's More menu is part of the system overflow, and while the toolbar is
  disabled every one of its actions shows disabled, Edit included; visibility priorities decide what overflows; the
  Search item's title and priority come with them.
- **Any iOS 27.1 device, Saved, Activity and Search** sit in the invisible container. The tab bar over an open article
  is hidden and shown by the app (`setTabBarHidden`) from the navigation delegate, not by UIKit's
  `hidesBottomBarWhenPushed`, because the tab is the container, not the stack; it moves with the push or pop through
  the transition coordinator and is put back if a back swipe is cancelled. Not checked with an interactive swipe (the
  tap runner has none; captures show only the settled states, which match main). Home and Places, their own stacks,
  keep UIKit's handling. Every walk of the hierarchy the container changes:
  [appendix](#appendix-walks-of-the-hierarchy-the-container-changes).
- **The Duo's outer display**, which has a vertical bar: the article's toolbar goes into it and some actions move into
  the system overflow ([Bars per pose](#bars-per-pose); [Costs](#costs)).
- **The sideways inner display**: Saved, Activity and Search show a list pane beside a detail pane; an article opened
  from them opens in the detail pane, its contents take the list pane, and the tab bar hides while it is open. Open
  contents carry over to the next article there, as the inline contents did on main (the same preference); the same
  read also opens them over the list on every unfold with an article open while the preference is on (item 1).

## Bars per pose
The article's bars, opened from Search (the flow's after snapshots) and from Home (`probes/article-home-*`, taken on
the second round's builds and not again after the third and fourth rounds changed the toolbar):

| Pose | In the vertical bar | In the system overflow (one tap on More) |
|---|---|---|
| closed | Back, Tabs, Profile, More, Contents, Languages, Save, Find in page | Search, Reading themes, and the app's More actions (Edit, Revision history, Talk page, Watch, Share, the tab's previous article): `probes/overflow-closed-cover.png` |
| closed-sideways | Back, Tabs, Profile, More, Contents, Save | Search, Languages, Find in page, Reading themes, the app's More actions |
| book, flat-sideways, from Search (detail pane) | Contents, Languages, Save, Find in page, Reading themes; no tab bar (an open article hides it, as before) | the detail pane's top bar holds W, Search, Tabs, Profile and the system More with the app's More actions |
| book, from Home (no split) | Back, W, Tabs, Profile, More, Contents, Languages, Save, Find in page | Search, Reading themes, the app's More actions |
| flat-sideways, from Home (no split) | Back, Search, Tabs, Profile, More, Contents, Languages, Save, Find in page (the W is the centred title: the fold is not active) | Reading themes, the app's More actions |
| laptop, tent, flat-upright | none (horizontal bars, as before: the app's own More in the bottom toolbar) | — |

The overflow menu was opened only on the cover: a tap reaches only the shut phone, and the menu closes on a fold. In
book and flat sideways what it holds is read from the snapshot (what is not in the bars) and
`additionalOverflowItems`.

Saved and Activity in book and flat sideways: their More, Tabs and Profile moved from the vertical bar into the list
pane's top bar, because the vertical bar goes with the detail pane (Apple: in split views only the detail column
participates). Search's Clear, Tabs and Profile were already in its top bar and stay there, in the list pane's.

Beside the list an open article is the root of the detail pane: there is no Back. The W is the way back to the list
(its label says so: "Wikipedia, return to Search"), as Back was; the tab bar is hidden while the article is open, as it
was full screen, so the count of taps to another tab is unchanged (W, then the tab, against Back, then the tab).

## Costs
- Community (Even grid): two columns of 366 pt instead of 418 pt in book and flat sideways, with a 125 pt gutter at the
  hinge (371–496 pt); the leading column now stops about 85 pt short of the fold, against 32.5 pt before. Cards get
  taller and their text wraps more.
- Tabs (Even grid): four columns of 177 pt instead of 207 pt, a 126 pt gutter (370–496 pt), the leading pair stopping
  about 85 pt short of the fold. Titles still fit; the description line under them now truncates ("Natural satellite
  orbiting E…"), and the extract loses two of its six lines.
- Article actions that now take two taps (the system overflow) where they took one: on the cover, Search and Reading
  themes; on the cover turned sideways, also Languages and Find in page; in book from Home, Search (its place in the
  vertical bar went to the W). Every action is still there (`probes/overflow-closed-cover.png`).

## How it was checked
- **Captures.** Built Debug, ad-hoc signed, against the iOS 27.1 SDK (Xcode 27.1), on the iPhone Duo simulator.
  Eleven screens ([`Wikipedia.flow`](Wikipedia.flow): reached by taps, logged out, the article by searching "Moon") ×
  seven poses with Foldsmith's `capture-app.sh`: a shot and an accessibility snapshot per cell, then the detectors.
  Before on the unmodified app. After, only each screen's latest capture is kept: article and contents from the fourth
  round's first build (`4e0f095`, `3c385e8`; `3c66733`, two routing fixes, was built and committed after this
  capture), saved, search, activity and settings from the third round's, community from the second's, and for-you,
  places, tabs and profile from the run's own check, before any review fix (the run says the fourth round changed no
  other screen's drawing; no round recaptured those four). Times and builds: RUN-NOTES.md.
- **Detectors.** [`detectors-before.md`](detectors-before.md), [`detectors-after.md`](detectors-after.md): cells with a
  finding 23 before → 16 after, lines 44 → 17. These are Foldsmith's detectors as synced into the run in its third
  round (the README's "Phase 4 step 9"), rerun over the full before and after in the fourth; no commit hash was
  recorded. The run's first report counted 21 → 10 with older copies. Of the 16 after-cells, 13 are ✗ in the grid and
  3 are contents' T7 judged false (note ᶜ). Six ✗ cells rest on the eye (marked); in contents' two the detector's line
  is that same false T7. `progress.py check` over `migration.json` and the captures reports no pose clean before with a
  finding after.
- **Looks.** The run looked at every shot. Its first look, from 300 px contact sheets, measured nothing; from the second
  round on it also measured every pane's controls against the pane in the snapshots (a small script). After the run's
  own check the coordinator looked at the shots, and three independent reviews (three or four reviewers each, a skeptic
  per finding) read the diff and both captures. Each found what the run had passed (2, 10, 22 and 23 items); in the next
  round the run fixed each confirmed item, reverted its change, or, where it was not a regression, kept it in `todo`.
  The last check found two of those fixes incomplete (items 9, 10) and confirmed the 15 items above, not fixed. Item by
  item: RUN-NOTES.md.

### Not checked
- The app's unit and UI tests; its Experimental and Alpha targets; any system before iOS 27.1; any device but the iPhone
  Duo simulator, so neither an iPhone or iPad on iOS 27.1, where the container and the plain toolbar items also apply,
  nor a real device.
- The app's pull-request template (a Phabricator task, test steps, screenshots, a Swift 6 strict-concurrency check):
  not prepared; item 10 is the kind of code that check is for (no build was run with it).
- Logged-in screens (Activity's timeline, the Account's log-in flow, the watchlist, the editors); Split View's half
  window; reading lists, the language picker, find in page, the theme popover.
- Whatever needs a tap on the inner display (the W in the detail pane, routes from it, the contents after an
  in-article link, a programmatic tab switch, the contents carried to the next article), a swipe (the tab bar during a
  back swipe), a notification or a quick action: read in code, not captured. Items 2–7 and 9 are on such paths. Item 1
  is on the flow's own path but hidden by its launch argument, which holds the contents preference off.
- The overflow menu in book and flat sideways, which cannot be opened headless.
- For-you, places, tabs and profile on the final build: last captured before the review rounds' changes.
- Waivers: the detector lines judged false (contents' T7 in three cells, Places' T14) are not recorded with
  `progress.py waive`.

## Where the references and the SDK differ
- Foldsmith's `references/apple.md` names `reservedRegions(kind:options:layoutDirectionBehavior:)` (SwiftUI). UIKit's
  27.1 Swift API is `UIView.reservedRegions(kind:options:)` returning `UIView.ReservedRegion` (`frame`, `margins`,
  `isActive`); `.includeInactive` matches. `UISheetPresentationController.preferredPlacement` (27.0) is the UIKit
  counterpart of `presentationPlacement`, ignored when a `sourceView` is set.
- The SDK has UIKit vertical-bar hooks the references do not mention: `UIViewController.preferredVerticalBarBehavior`,
  `childForPreferredVerticalBarBehavior`, `setNeedsUpdateOfVerticalBarConfiguration()` (27.1) and
  `UITraitCollection.verticalBarEdge` / `systemTraitsAffectingVerticalBarEdge` (which Wikipedia already used).
  `UITabBarController.sidebar.preferredPlacement` (27.0) can put tabs in a sidebar on an iPhone; not used.

## Appendix: walks of the hierarchy the container changes
As the run listed them after its fourth round. The last check found three of them wrong or missed: the random-article
shortcut (item 2), `debounceRemoveArticlesForDeletedTabParts` (item 4) and the tabs overview's `tappedTab` (item 5).
- Unwrapped or routed through the container: `NavigationStateController` (save and restore of the open article;
  main-actor), the theme pass (`applyTheme(_:toNavigationControllers:)`, `applyTheme(toPresentedViewController:)`,
  `allNavigationControllers`, `updateUserInterfaceStyleOfNavigationControllersForCurrentTheme`), the tab bar
  (re-evaluated on every selection change and tap), `currentTabNavigationController`, `visibleViewController()`, the
  back-to-root routes (`.savedPages`, `.activity`, `switchToSearch`, the imported reading list, a second tap on the
  tab, which counts the detail pane), `debounceRemoveArticlesForDeletedTabParts`, `logDidSelectViewController`,
  `logTabBarSelectionsForActivityTab`, the Activity visit count.
- The tab's front stack (the detail stack while it shows an article, else the list's): the router's
  `currentNavigationController` (More › Talk page, the footer's talk link, user, talk and in-app web links, the
  editor's cancel), its watchlist target, `processLinkUserActivity`, Notifications Center from a push notification
  (its "already showing" check still reads the list: item 3), and `visibleArticleViewController()` (a deep link to the
  open article, the return from the web editor, the Search quick action).
- "At the root" counts the detail pane: the language-variant alerts and the evergreen account-creation prompt stand
  down while an article is open beside the list, as they do over an open article on main.
- Read as unaffected: `AppViewControllerTipWrapper` (Home only; reads the tab's `tabBarItem`, which the container
  shares), `ViewControllerTransitionsController` and `DetailTransition` (they look for a detail source among the
  selected tab and fall through for a stack as for a container), `editingFlowViewControllerInHierarchy`,
  `topMostViewController` and `dismissPresentedViewControllers` (`presentedViewController` reaches the ancestors'
  presentations from the list stack), `isViewingEligibleMainTab` (the list's root), the orientation queries,
  `ViewControllerRouter`'s settings check, the Home and Places routes and Explore's Notifications Center (their tabs
  are their own stacks), a modal reading list's article (pushed onto the list, so it goes to the detail pane),
  `ExploreViewController` and `TalkPageViewController` (their own presentations), and the coordinators handed a
  navigation controller (`SavedCoordinator`, `HomeCoordinator`, `ArticleCoordinator`, `LinkCoordinator` get the list
  stack, or the detail stack from inside the detail pane and from the router, which is what they push onto).
