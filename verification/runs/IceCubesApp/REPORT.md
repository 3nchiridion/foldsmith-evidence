# Ice Cubes on iPhone Duo — migration report

*Edited on 2026-10-31, after the run: see [Corrections](#corrections-2026-10-31). The report as first published (the
run's text, simulator UDID redacted) is in this repository's history (commit 7de0b09).*

## At a glance

| | |
|---|---|
| Status | Done for the five screens the run could reach without an account. **The logged-in app was never run, and the tab-bar rebuild that crashed the app logged out may crash it there**; this branch does not touch that code. |
| For an iPhone Duo user (logged out) | In the after capture, folding, unfolding and closing the phone no longer crashed the app or brought the dismissed Add Account sheet back over Settings, Display Settings or a post. Held like a book, the Add Account sheet opens on the trailing half instead of the leading one. The timeline needed nothing. On the cover the two tabs have swapped places: Timeline is now above Settings. |
| Coverage | 5 of the 5 screens reached, × 7 poses = 35 cells: 8 had a defect and are fixed and verified, 27 had no finding before or after (`unchanged`; 11 of them upright, short of the decision table's two panes; in 8 on the cover the two tabs swapped places), 0 are left ([migration.json](migration.json)). 13 more screens are listed in `todo` and were never captured: 12 that need an account, and a stranger's profile. |
| Code | Dimillian/IceCubesApp at 98098b9 (the local baseline commit 8614ae9 is that tree with an xcconfig made from the app's template, by its commit message); branch `duo-migration`, 2 code commits, 15 files, +115 / −6 lines ([commits.txt](commits.txt), [IceCubesApp.patch](IceCubesApp.patch)). |
| How it was checked | `tools/capture-app.sh` captured every cell before and after, and `duo-detect` read the accessibility snapshot taken with every shot: before, 2 findings in 33 snapshots; after, 0 in 35 ([detectors-before.md](detectors-before.md), [detectors-after.md](detectors-after.md): the run's own output from the skill's tools at the time, not regenerated). The other 6 defects came from the capture's own lines and a crash, not the detectors. The run looked at every shot; a spot check by eye followed ([README.md](README.md)). On 2026-10-31, after the run, an independent review: a layout reviewer (every shot and snapshot) and a code reviewer (the diff, the source at both revisions), working from the evidence alone and blind to this report, then a skeptic who checked their items ([Known misses](#known-misses-independent-review-2026-10-31)). The project's unit tests and SwiftFormat were not run. |
| Known regressions | None. The detectors and the run's look found none, and the independent review confirmed none: its 7 items are misses and unverified risks. |
| Known misses | From the run: upright, every screen shows one pane; in 11 of those cells the decision table asks for two (no detector class covers it). From the independent review (7 items, 1 medium, 6 low): logged in, untested, the fold swaps the whole tab set and holds what crashed logged out (medium); the logged-out tab change has no gate and changes the tab bar on every logged-out iPhone too (on the cover the two tabs swap); 13 of the 14 sheet sites were never opened, 3 of them reachable logged out; in-app Safari, the share sheet and the share extension's composer keep the system's placement in book; three style points in the new file. See [Known misses](#known-misses-independent-review-2026-10-31). |
| Left to the app's owner | Testing logged in; the logged-out tab change applies on every device, iPhones included (run only on the Duo); every new sheet must carry `foldAwareSheetPlacement()`; the upright layout. See [Decisions](#decisions-for-the-apps-owner). |
| Made by | A Claude Code agent with the Foldsmith skill (then `iphone-duo-patterns`, in the duo-patterns repository), hands-free, on 2026-10-28: Xcode 27.1, iOS 27.1 SDK, iPhone Duo simulator `<Duo simulator UDID>` (redacted), logged out, since no account may be created there. |

## Terms

- **Poses**: `closed` and `closed-sideways` use the **cover** (the outer display; 466 × 678 pt, 678 × 466 turned). The
  others use the **inner display**, the one that folds: `book` (held partly open, a vertical fold; 951 × 669 pt),
  `laptop` (resting partly open, a horizontal fold; 669 × 951 pt), `tent` (standing on its edges; laptop's geometry),
  `flat-sideways` (open flat, 951 × 669 pt), `flat-upright` (open flat, 669 × 951 pt). **Upright** means laptop, tent
  and flat upright.
- **pt**: layout points. **The fold**: the division the system reports over the hinge
  (`reservedRegions(kind: .division)`), 455.5–495.5 pt on the folded axis, active only while partly folded (book,
  laptop, tent). **Leading / trailing half**: left / right in a left-to-right language.
- **Vertical bar**: on the cover, in book and in flat sideways the system draws the tab bar and toolbars down one side.
- **Cell**: one screen in one pose. **Verified**: the cell had a defect, was fixed, recaptured, and found clean by the
  detectors and by eye. **Fine as is**: no finding before or after (`unchanged` in migration.json); 11 upright ones still
  miss the decision table (Known misses, above).
- Defect classes, from Foldsmith's
  [taxonomy](https://github.com/3nchiridion/foldsmith/blob/main/skills/foldsmith-duo/references/taxonomy.md):
  **T7** state-loss-on-fold (a fold loses what the person was looking at; here a dismissed sheet came back, or the app
  crashed); **T15** sheet-in-book (a sheet on the leading half while held like a book); **T10**
  stranded-horizontal-item (a text-only bar button that stays horizontal where the bar goes vertical); **T14**
  columns-off-fold (a pane or column edge inside the fold); **T1** crease-straddle (a control or text that does not
  scroll, lying on the fold).
- **P1, Fold-aligned split**: the pattern for hierarchical content (a list whose items open a detail): one pane closed,
  two panes open, their boundary on the fold when folded, the leading pane narrower when flat. **The sheet rule**: P1's
  rule that sheets open on the trailing half while the phone is held like a book.
- **Hierarchical** (the decision table's content relationship, I1): a list that selects a detail meaningful alone.
  **Viewing** / **editing** (its interaction mode, I2): what the hand does most of the time, reading or changing values.
  The pattern follows from the two ([audit.md](audit.md), Classification).
- **`duo-detect`**: Foldsmith's detectors, run on each shot's accessibility snapshot. **`todo`**: what the progress file
  ([migration.json](migration.json)) lists as left.

## Corrections (2026-10-31)

A check of this folder against the run's evidence found the following; corrected here, the original text stays in git
history.
- Every screen was marked "verified in all seven poses". Only 8 of the 35 cells are `verified`, 27 are `unchanged`, and
  11 upright cells do not match the decision table. migration.json's `done` lines and the report commit's subject still
  say "verified in every pose": they are the run's evidence and stay as written.
- Post, Settings and Display Settings were labelled "Fold-aligned split (state kept on fold)". Their change fixes the
  logged-out tab structure (a crash and a returning sheet); no split was added. The label stays in migration.json and
  the commit subject.
- The run was given as 07:51–09:52; the log starts at 07:52 and the report was committed at 09:50:58. Timing is now in
  RUN-NOTES.md.
- "Left to do (`todo`)" had six items; `todo` has five. The sixth, the upright shortfall, now stands on its own.
- The logged-in risk was the second item of "Left to do"; it now leads. visionOS is added to the devices the logged-out
  tab change reaches.
- The logged-in sections were called untitled; they are titled, each `TabSection` placed `.sidebarOnly` (migration.json's
  `todo` keeps the run's wording). Logged in, the tab set was said to change on every fold; it changes on opening and
  closing the phone (every inner pose is regular width, the cover compact).
- The sheet rule's reach was stated without saying that one sheet was opened; now said.
- The `AppView` change was "one condition and a comment": it is a comment and two changed lines.
- Book: "the sidebar ends at 455.5 pt, the content starts at 495.5 pt" was rounded; the snapshot gives 455.3 and 495.7.
- The T10 hits cited the `ToolbarItem` lines (61, 57), where the scan's match starts; the buttons are on 62 and 58.
- Claims resting on files that were not kept (the crash report, the logging build's output, the probes) are marked.
- Paths pointed into `.duo/` in the app's working copy: files published here are now linked, and the shots, which are
  not published, are named once by their path pattern.
- Added: At a glance, Terms, Not verified, Decisions. Moved to [RUN-NOTES.md](RUN-NOTES.md): the skill's problems,
  where the skill's references and the SDK differed, timing.
- 2026-10-31: added the independent review (At a glance; [Known misses](#known-misses-independent-review-2026-10-31)),
  so "no independent review" is gone. It contradicted two statements, corrected: the logged-out tabs were said to take
  "the path the app already takes for its iPhone tabs", but logged out, iPhones took the sidebar-only section too; and
  the tab change was said to reach iPad, Mac and visionOS, when it reaches every device, iPhones included.

## Result per screen

| Screen (flow name) | What it is | Before ([audit.md](audit.md)) | Change | After |
|---|---|---|---|---|
| Add Account (`add-account`) | the sheet the app opens at launch, logged out | T15 in book: the sheet at 8–467.5 pt, on the leading half (detectors) | the sheet rule, `725ff38` | 0 findings; book verified, 6 poses fine as is |
| Timeline (`timeline`) | mastodon.social's Trending timeline | nothing | none: the system tab sidebar already splits at the fold | 0 findings; 7 poses fine as is |
| Post (`status`) | a post's thread, pushed on the timeline | T7 in flat sideways: unfolding brought the dismissed Add Account sheet back (detectors and shot) | logged-out tabs as plain tabs, `761fc95` | 0 findings; flat sideways verified, 6 fine as is |
| Settings (`settings`) | the Settings form | T7 in laptop (the sheet came back; capture line); closing the phone from tent crashed the app (T7 in tent and both flat poses) | plain tabs, `761fc95` | 0 findings, no crash; 4 verified, 3 fine as is |
| Display Settings (`settings-display`) | pushed on the Settings stack | T7 in laptop and flat sideways (the sheet came back; capture lines) | plain tabs, `761fc95` | 0 findings; 2 verified, 5 fine as is |

Every screen is hierarchical, so its pattern is P1. The app's container, the system tab sidebar, already does what P1's
split does in book and flat sideways, so no screen got a new split: one change is the sheet rule, the other a fix to
the tab structure.

Per cell, from [migration.json](migration.json) (✓ verified, with the class it had before from audit.md, since
migration.json keeps only the state; = fine as is; no cell is left with a finding):

| Screen | closed | closed-sideways | book | laptop | tent | flat-sideways | flat-upright |
|---|---|---|---|---|---|---|---|
| add-account | = | = | ✓ T15 | = | = | = | = |
| timeline | = | = | = | = | = | = | = |
| status | = | = | = | = | = | ✓ T7 | = |
| settings | = | = | = | ✓ T7 | ✓ T7, crash | ✓ T7, crash | ✓ T7, crash |
| settings-display | = | = | = | ✓ T7 | = | ✓ T7 | = |

Settings in tent: the tent shot was right; the crash came on closing the phone after it, and a loss goes on the pose
whose round trip lost it. Before, Settings' two flat shots show the Home Screen and have no snapshot.

The shots are not published: the timeline and the post show strangers' posts. In the app's working copy they are
`.duo/captures/IceCubesApp/{before,after}/<screen>-<pose>-{cover,inner}.png` (cover for closed and closed-sideways),
each with its snapshot `<screen>-<pose>.json`; migration.json names each cell's pair.

## What was wrong, and why

- **A fold brought the Add Account sheet back, and closing the phone from tent crashed the app (T7).** Logged out,
  `AppView` put the two tabs (Timeline, Settings) in an untitled `TabSection` placed `.sidebarOnly`, Settings itself
  sidebar-only (`Tabs.swift`), on every device: the logged-out check comes before the compact one. By the run's account
  (the crash report's stack and a logging build, neither kept), each change of the vertical bar (a fold, a turn of the
  cover) makes `UITabBarController` rebuild its items: with Settings selected, the hidden Timeline tab appeared and
  disappeared within 50 ms, and its `onAppear` (logged out: present Add Account, `TimelineTab.swift:73`) put the sheet
  back over what the person was reading; with Timeline selected the same rebuild put it back over a post. With Settings
  selected, closing the phone from tent crashed the app: in both flat poses the capture's snapshot failed ("not
  running") and the shot shows the Home Screen. The run's audit quotes the crash report, which was not kept:
  `NSInvalidArgumentException: -[__NSArrayM insertObject:atIndex:]: object cannot be nil` in `-[UITabBarController
  _tabs_rebuildTabBarItemsAnimated:]`, from `_UITabBarControllerAdaptiveVisualStyle _verticalBarAvailabilityDidChange`;
  the run says it reproduced twice in the same pose.
- **Sheets on the leading half in book (T15).** The system places sheets on the leading half while the fold is active
  (Add Account at 8–467.5 pt); the app set no placement.
- **Already right.** In book the system tab sidebar's rows end at 455.3 pt, its background reaches the fold's centre
  (475.5 pt), and the tab's content starts at 495.7 pt, so the boundary is on the fold with no width set (none is set
  anywhere: no T14). Flat sideways the sidebar is the narrower pane (its bar 0–280 pt). In laptop and tent the timeline,
  the post and both settings forms scroll across the horizontal fold, which scrolling content may do (no T1); flat
  upright the fold is inactive. On the cover the tab bar and the navigation items sit in the vertical bar, every item a
  symbol.

## What changed (one commit per change)

- `761fc95`, **post, Settings, Display Settings**: the logged-out tabs are plain `Tab`s, as the app's logged-in iPhone
  tabs already are (`AppView.swift`: a comment, and two changed lines, the `if` condition and the tabs it iterates).
  Logged out, every device used the sidebar-only section before, iPhones included, so the comment's "as on iPhone" does
  not hold ([Known misses](#known-misses-independent-review-2026-10-31), 2). The after capture shows no crash, no lost
  screen and no returning sheet in any pose. Before that capture the run checked the fix with the logging build
  (Settings in all seven poses) and with probes of the post and Display Settings; neither output was kept, so the after
  capture is the evidence.
- `725ff38`, **Add Account**: the sheet rule. A new file,
  `Packages/DesignSystem/Sources/DesignSystem/Duo/FoldAwareSheetPlacement.swift`, adapted from Foldsmith's P1 reference
  (`FoldAlignedSplit.swift`): `readsBookFold()`, once on the window's root (`IceCubesApp+Scene.swift`), reads the active
  vertical fold from `GeometryProxy.reservedRegions(kind: .division)` and sets `duoBookFoldIsActive`;
  `foldAwareSheetPlacement()` on a sheet's content then asks for `presentationPlacement(.trailing)` while it is set and
  `.automatic` otherwise. It is on every sheet outside a preview, 14 call sites: the router's sheet
  (`withSheetDestinations`, 25 destinations, Add Account among them), the media viewer, Settings' own Add Account, the
  relationship note, the account switcher, list create, the notifications policy, the composer's custom emoji,
  language, media edit and drafts sheets, and a post's selectable text (two sites) and share-as-image sheets. Both
  modifiers are gated `#available(iOS 27.1, *)` and `#if os(iOS) && !targetEnvironment(macCatalyst)`. Nothing new reads
  the device, idiom or orientation; the drafts sheet's existing idiom branch is kept, now inside a `Group`.
- Timeline: no change.

## Beyond book on the Duo

- **Sheets**: by reading the code, the placement changes only while a vertical fold is active; elsewhere (flat, upright,
  the cover, any device without a fold) the modifier asks for `.automatic`, and before iOS 27.1 it does nothing. Mac
  Catalyst and visionOS compile both modifiers away. Not run anywhere but the Duo simulator.
- **Logged-out tabs, every device**: the same two tabs, in the same order in code, now plain `Tab`s with no gate. At the
  base the logged-out check came before the compact one (base `AppView.swift:63-67`), so every device, iPhones included,
  used the sidebar-only section, and the change reaches them all. Seen on the Duo (snapshots): the sidebar no longer
  shows the empty section header with its chevron, and the cover's bar lists Timeline above Settings (before: Settings
  above Timeline, on the cover only; the sidebar listed Timeline first both times). The cover is the compact path a
  logged-out iPhone takes, so an iPhone's bar should swap the same way (no iPhone run). On iPad, Mac (Catalyst) and
  visionOS, logged out, Settings was sidebar-only and may now also appear in the floating tab bar (on the Duo's inner
  display upright, regular width on iOS 27, no tab shows in a bar after). Not run.
- **Logged in**: `AppView`'s logged-in tab paths are untouched; the sheets they open carry the sheet rule (none opened).
- **Builds**: the iOS simulator Debug build built (the after capture ran on `725ff38`; the run calls it clean, no build
  log kept). DesignSystem does not build for the visionOS Simulator, with or without this branch: the unmodified
  `ToastOverlayView.swift:38` calls `glassEffect`, which the 27.1 SDK marks unavailable on visionOS (line and SDK
  checked after the run; the run says the new file raised no error there). Mac Catalyst was not built.

## Not verified

- **The logged-in app.** No screen behind an account was run. Logged in, the inner display (regular width) gets the
  iPad tab sections (titled `TabSection`s, each placed `.sidebarOnly`) and the cover the iPhone tabs
  (`AppView.availableSections`), so every opening or closing of the phone changes the tab set, and, by the run's
  account, every change of the vertical bar makes the tab bar rebuild: the kind of structure and rebuild that crashed
  logged out. It may crash there.
- **The crash fix beyond one capture.** The after capture (one pass) did not crash; the run's logging build and probes
  were not kept. Foldsmith's
  [`simulator.md`](https://github.com/3nchiridion/foldsmith/blob/main/skills/foldsmith-duo/references/simulator.md):
  a crash on the fold can be intermittent, and one capture without it proves nothing.
- **13 of the 14 sheet call sites**, and 24 of the router's 25 destinations: never opened on the Duo. Only Add Account,
  presented at launch through the router, was captured. Three of the others open logged out from captured screens, and
  no captured sheet held typed text ([Known misses](#known-misses-independent-review-2026-10-31), 3).
- **Presentations the sheet rule cannot reach**: in-app Safari, the share sheet, the pickers, and the composer run from
  the share extension ([Known misses](#known-misses-independent-review-2026-10-31), 4 and 5).
- **A stranger's profile** (`AccountDetailView`) and the two static T10 hits (below).
- **Split View's half window** (475 × 669 pt): the simulator cannot drive it headless.
- **Other devices**: none but the Duo simulator, though the logged-out tab change reaches every device, iPhones
  included; Mac Catalyst not built.
- **The project's checks**: the package tests its `CLAUDE.md` names (AccountTests, ModelsTests, NetworkTests,
  TimelineTests, EnvTests) and SwiftFormat (`.swiftformat`, 2-space indent) were not run.
- **Files not kept**: the crash report, the logging build's output, the probes' output.
- **Independent review**: none during the run (the skill had no review step then). One followed on 2026-10-31, below; it
  read the evidence and the source and ran nothing.

## Known misses (independent review, 2026-10-31)

Three Claude Code agents with no part in the run reviewed the branch from the evidence, read-only (nothing built, no
simulator touched): a layout reviewer (every before and after shot, every accessibility snapshot node by node) and a
code reviewer (the diff without `.duo/`, the source at both revisions, the routes into the touched types), neither of
whom read this report, [audit.md](audit.md) or [migration.json](migration.json); then a skeptic, who checked each of
their items against the diff, the source, the snapshots and shots, and Foldsmith's rules. They raised 15 items: 7
stand, none of them a regression (1 medium, 6 low); 5 repeated one of the 7 and were merged into it; 3 were refuted.
[README.md](README.md) lists what they found right and what was refuted. The review's ids are in parentheses; lines
are on `duo-migration` unless marked base (`8614ae9`); the snapshots and shots are in the app's working copy (path
pattern under Result per screen).

1. **Logged in, a fold may crash the app or lose the tab (C-R1; medium; not a regression; not run).** This branch
   fixes the crash and the returning sheet for the logged-out tabs only. Logged in, `availableSections`
   (`AppView.swift:62-80`, untouched) gives the compact cover five plain tabs (by default Timeline, Notifications,
   Explore, Links, Profile; `Tabs.swift:388-394`) and the regular inner display titled `TabSection`s placed
   `.sidebarOnly` plus the App section (`AppView.swift:148`), with Settings, Bookmarks, Favorites, Mentions, Messages
   and Lists sidebar-only (`Tabs.swift:183-190`). So every opening or closing of the phone swaps the whole tab set, and
   a sidebar-only tab selected on the inner display, such as Settings, has no tab on the cover by default (T7). The
   path also holds what this branch's own comment (`AppView.swift:94-98`) blames for the logged-out crash in
   `-[UITabBarController _tabs_rebuildTabBarItemsAnimated:]`: a sidebar-only Settings tab, selected inside a
   sidebar-only section, while the tab bar rebuilds. Medium, not high: the crash is read from the code, not shown, and
   the same compact/regular swap already runs upstream when an iPad window is resized. Whether it crashes or loses the
   selection is not known.
2. **The logged-out tab change has no gate and changes the tab bar where there is no fold (C-R5; low; not a
   regression).** At the base, `availableSections` returned the logged-out tabs before checking for compact width
   (base `AppView.swift:63-67`), so a logged-out iPhone too used the untitled `TabSection` placed `.sidebarOnly` (base
   `:105-143`), with Timeline `.pinned` and Settings `.sidebarOnly` (`Tabs.swift:183-190`). The branch's comment
   "plain tabs, as on iPhone" (`AppView.swift:94`) does not hold. `AppView.swift:99-108` now builds plain `Tab`s with
   no placement and no `#available`, reserved-region or size-class gate: logged out, on every iPhone, iPad, Mac
   Catalyst and visionOS device on iOS 18.5 and later. Seen on the cover, the compact path every logged-out iPhone
   takes: the two tabs swap places. `before/timeline-closed.json` has Settings at x 396–440, y 544–602 and Timeline at
   y 594–652; `after/timeline-closed.json` has them the other way round; closed sideways, the same at x 26–70, y
   332–390 and 382–440. The swap is the same for the post, Settings and Display Settings, closed and closed sideways: 8
   cells, all `unchanged` (no finding before or after), whose layout did change. On the Settings screens the selected
   tab moves from the top slot to the bottom one (`{before,after}/settings-closed-cover.png`). The new order matches
   the inner sidebar, and the sidebar's empty section header is gone, both harmless. Nothing captured a logged-out
   iPad, where Settings is no longer sidebar-only and may join the floating tab bar on iOS 18.5–26, nor Mac or visionOS
   (`{before,after}/capture.txt`: the Duo only); on the Duo's inner display upright, regular width on iOS 27, no tab
   shows in a bar after the change. Nothing is broken, but it is a change on devices with no fold.
3. **13 of the 14 sheet sites were never opened (C-R2; low; not a regression).** Only Add Account, through the
   router's sheet (`AppRegistry.swift:245`, `withSheetDestinations`, 25 destinations), was captured:
   `after/add-account-book.json` has its navigation bar at x 483.5–942.8 (459.3 pt wide) and Cancel at x 883.8–921.8,
   y 129–167, in the vertical bar. Three uncaptured sheets open logged out from captured screens: Settings' own Add
   Account (`SettingsTab.swift:363-366`; its row is in `after/settings-book.json`); the media viewer, presented at the
   scene's root with `.presentationSizing(.page)` and a zoom transition (`IceCubesApp+Scene.swift:30-41`); and Select
   text and Share as image from a post's ••• menu (`StatusRowContextMenu.swift:136-137, 161-162`;
   `StatusRowView.swift:233-238`; `StatusRowActionsView.swift:148-149, 272-290`). The rest are in the composer and the
   account and list screens: media edit (`MainView.swift:51-53`), drafts (`ToolbarItems.swift:44-52`), language
   (`LangButton.swift:36-41`), custom emoji (`AccessoryView.swift:205-208`), the account switcher
   (`AppAccountsSelectorView.swift:86`), list create (`ListAddAccountView.swift:70`), the notifications policy
   (`NotificationsListView.swift:114`) and the relationship note (`AccountDetailView.swift:181`). The captured sheet
   stayed up and changed sides at every fold, but held no typed text: whether a sheet with input, such as a draft,
   keeps it when its placement changes live is untested. Low: the modifier and its reach are the same at every site,
   and a failure would at worst leave a sheet on the leading half, as before.
4. **In-app Safari and the share sheet cannot take the sheet rule (C-R3; low; not a regression).** Presentations that
   `foldAwareSheetPlacement()` cannot reach keep the system's placement in book, and none was captured. In-app Safari,
   the default browser (`UserPreferences.swift:10`, `.inAppSafari`), is a UIKit `SFSafariViewController` presented at
   `SafariRouter.swift:98-109` from any link in a post; the system share sheet opens from each post's share button and
   ••• menu (`ShareLink` at `StatusRowActionsView.swift:229, 235` and `StatusRowContextMenu.swift:121, 132`). Both open
   logged out from captured screens. Behind an account: `.photosPicker` (`AccessoryView.swift:128`,
   `MediaPickerPanelView.swift:52`, `EditAccountView.swift:143`), `.fileImporter` (`AccessoryView.swift:136`,
   `MediaPickerPanelView.swift:60`) and `translationPresentation` (`Env/Ext/TranslationView.swift:12`). Whether any of
   them sits on the leading half in book (T15) is not known; a UIKit sheet could take
   `sheetPresentationController.preferredPlacement` (Foldsmith's P1 page, UIKit section). Alerts are not among them:
   the system moves alerts to the trailing side itself.
5. **The share extension never reads the fold (C-R4; low; not a regression).** `ShareViewController.swift:30-48`
   hosts the composer (`StatusEditor.MainView`) with `.environment(...)` and `.modelContainer` only, and
   `readsBookFold()` is applied only at `IceCubesApp+Scene.swift:72`, so `duoBookFoldIsActive` keeps its default, false
   (`FoldAwareSheetPlacement.swift:21`). Run from another app's share sheet, the composer's drafts, language, custom
   emoji and media edit sheets keep the system's placement in book (the leading half, T15). Not captured; the same as
   before the branch.
6. **Upright, one pane (L-L3; low; not a regression; already counted).** In laptop, tent and flat upright the inner
   display shows the tab's content across the full 669 pt, the tabs behind Toggle sidebar: `after/timeline-laptop.json`
   has the navigation bar at x 0–669, y 24–82 and Toggle sidebar at x 24–64, y 28–68, with no Timeline or Settings
   cell. The same in tent and flat upright, and for the post, Settings and Display Settings: 12 cells, and the same
   before (`{before,after}/settings-tent-inner.png`). Going from Timeline to Settings takes 2 taps there against 1 on
   the cover. The review held all four screens to the table's hierarchical/viewing row; by the run's classification
   Display Settings is editing, so this report counts 11 cells (under Left to do). Low: Foldsmith's P1 page accepts it
   as the system `TabView`'s known shortfall, and the content stays fully usable.
7. **Three style points in the new file (C-R8; low; not a regression; nothing functional).** (a)
   `FoldAwareSheetPlacement.swift:72` reads `nonisolated private func`; every other such declaration in the base (11)
   puts the access level first (`private nonisolated` at `StreamWatcher.swift:183, 200` and
   `NotificationServiceSupport.swift:52`), and SwiftFormat's default modifier order (`.swiftformat` sets only
   `--indent 2`) would likely flip it (not run). (b) The new `@Entry var duoBookFoldIsActive` (`:21`) is in
   DesignSystem; every other custom environment value is in `Env/CustomEnvValues.swift:5-18`, a package DesignSystem
   already depends on. (c) The header comment (`:1-15`) cites names that mean nothing upstream: "duo-patterns' P1",
   `patterns/fold-aligned-split.md`, `FoldAlignedSplit.swift`, "taxonomy T15", "tech talk 111463". A fourth point, that
   the branch lacked a Mac build, was refuted: the repository's `CLAUDE.md` accepts a build on the Mac or the iOS
   simulator.

## Left to do (`todo` in migration.json, five entries)

- Screens that need an account: Notifications, Mentions, Explore, Messages, Profile, Bookmarks, Favorites, Lists,
  Followed tags, Links, the composer, account settings. No account may be created on this simulator.
- A stranger's profile (`AccountDetailView`): reachable logged out only through avatars and names whose labels change
  with the timeline; no stable tap.
- Static T10 hits, not captured (behind an account): the text-only bar buttons `Button("action.done")` at
  `ListAddAccountView.swift:62` and `Button("action.cancel")` at `EditRelationshipNoteView.swift:58` ([audit.md](audit.md)
  cites 61 and 57, where the scan's match starts).
- Split View's half window (475 × 669): not drivable headless.
- Logged-in tab sections: as under Not verified; needs an account to capture.

Not in `todo`, and not fixed: **upright, one pane.** In laptop, tent and flat upright the tab's content fills the
669 pt window and the sidebar is a tap away. The decision table asks for two panes there [apple-apps/settings/laptop,
flat-upright] for the viewing screens (timeline, post, Settings: 9 cells) and, in flat upright, for the editing screens
(Add Account, Display Settings: 2 cells; their laptop cell names P1 without saying two panes, and is not counted; their
tent cell is `UNJUSTIFIED`). No defect class covers it and the detectors found nothing, so the skill's rules leave those
11 cells `unchanged`; Foldsmith's P1 page records the same shortfall for a `TabView` sidebar.

## Decisions for the app's owner

1. **Test logged in first.** Sign in on an iPhone Duo and fold from each pose with each tab selected, as the capture
   did logged out (on `duo-migration`, `AppView.swift:62` `availableSections`; `:110` and `:148`, the titled
   `TabSection`s placed `.sidebarOnly`). If the crash comes back there, the crash itself (a nil tab bar item inside
   UIKit's rebuild) may be worth reporting to Apple.
2. **The logged-out tab change reaches every device, iPhones included.** Keep it (`AppView.swift:99`; logged out,
   every device then uses plain tabs: an iPhone's bar lists Timeline above Settings, and on iPad, Mac and visionOS
   Settings may show in the floating tab bar), or limit it. Run only on the Duo.
3. **The sheet rule is a convention.** Every new sheet needs `.foldAwareSheetPlacement()` on its content, or it opens
   on the leading half in book; nothing enforces it.
4. **Upright.** Accept the tab sidebar's upright layout (content full width, sidebar a tap away), or build two panes
   there; Foldsmith's two-pane upright split replaces a `NavigationSplitView`, not a `TabView`.
5. **T10.** Give the two text-only bar buttons a symbol (for example `Button("action.done", systemImage: "checkmark")`)
   so they can go vertical with the bar. Not done here.
6. **Tests, format, Mac Catalyst.** Run the package tests and SwiftFormat on the branch, and build Mac Catalyst (not
   built in the run). Before offering it upstream, see the review's style points
   ([Known misses](#known-misses-independent-review-2026-10-31), 7).

## Evidence in this folder

- [audit.md](audit.md): the unmodified app's audit, the findings per screen × pose with their evidence, and the
  classification. Written by the run; its `.duo/` paths are in the app's working copy, not here.
- [detectors-before.md](detectors-before.md), [detectors-after.md](detectors-after.md): `duo-detect`'s tables.
- [migration.json](migration.json): the progress file (cell states, `todo`, the run's log).
- [commits.txt](commits.txt), [IceCubesApp.patch](IceCubesApp.patch) (AGPL-3.0, [LICENSE-AGPL-3.0](LICENSE-AGPL-3.0)),
  [IceCubesApp.flow](IceCubesApp.flow) (the screens and the taps that reach them).
- Not here: the shots and snapshots (path pattern above), the crash report, the logging build.

Notes meant for Foldsmith's maintainers rather than the app's (timing, the skill's problems as the run found them,
where the skill's references and the SDK differed) are in [RUN-NOTES.md](RUN-NOTES.md).
