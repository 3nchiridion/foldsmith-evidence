# Ice Cubes on iPhone Duo — migration report

*Edited on 2026-10-31, after the run: see [Corrections](#corrections-2026-10-31). The report as first published (the
run's text, simulator UDID redacted) is in this repository's history (commit 7de0b09).*

## At a glance

| | |
|---|---|
| Status | Done for the five screens the run could reach without an account. **The logged-in app was never run, and the tab-bar rebuild that crashed the app logged out may crash it there**; this branch does not touch that code. |
| For an iPhone Duo user (logged out) | In the after capture, folding, unfolding and closing the phone no longer crashed the app or brought the dismissed Add Account sheet back over Settings, Display Settings or a post. Held like a book, the Add Account sheet opens on the trailing half instead of the leading one. The timeline needed nothing. |
| Coverage | 5 of the 5 screens reached, × 7 poses = 35 cells: 8 had a defect and are fixed and verified, 27 had no finding before or after (`unchanged`; 11 of them upright, short of the decision table's two panes), 0 are left ([migration.json](migration.json)). 13 more screens are listed in `todo` and were never captured: 12 that need an account, and a stranger's profile. |
| Code | Dimillian/IceCubesApp at 98098b9 (the local baseline commit 8614ae9 is that tree with an xcconfig made from the app's template, by its commit message); branch `duo-migration`, 2 code commits, 15 files, +115 / −6 lines ([commits.txt](commits.txt), [IceCubesApp.patch](IceCubesApp.patch)). |
| How it was checked | `tools/capture-app.sh` captured every cell before and after, and `duo-detect` read the accessibility snapshot taken with every shot: before, 2 findings in 33 snapshots; after, 0 in 35 ([detectors-before.md](detectors-before.md), [detectors-after.md](detectors-after.md): the run's own output from the skill's tools at the time, not regenerated). The other 6 defects came from the capture's own lines and a crash, not the detectors. The run looked at every shot; a spot check by eye followed ([README.md](README.md)). No independent review. The project's unit tests and SwiftFormat were not run. |
| Known regressions | None found by the detectors or the run's look; no independent review looked for them. |
| Known misses | Upright, every screen shows one pane; in 11 of those cells the decision table asks for two (no detector class covers it). The sheet rule is on 14 sheet call sites; one sheet, Add Account, was opened. |
| Left to the app's owner | Testing logged in; the logged-out tab change applies on iPad, Mac and visionOS too (not run there); every new sheet must carry `foldAwareSheetPlacement()`; the upright layout. See [Decisions](#decisions-for-the-apps-owner). |
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
  sidebar-only (`Tabs.swift`). By the run's account (the crash report's stack and a logging build, neither kept), each
  change of the vertical bar (a fold, a turn of the cover) makes `UITabBarController` rebuild its items: with Settings
  selected, the hidden Timeline tab appeared and disappeared within 50 ms, and its `onAppear` (logged out: present Add
  Account, `TimelineTab.swift:73`) put the sheet back over what the person was reading; with Timeline selected the same
  rebuild put it back over a post. With Settings selected, closing the phone from tent crashed the app: in both flat
  poses the capture's snapshot failed ("not running") and the shot shows the Home Screen. The run's audit quotes the
  crash report, which was not kept: `NSInvalidArgumentException: -[__NSArrayM insertObject:atIndex:]: object cannot be
  nil` in `-[UITabBarController _tabs_rebuildTabBarItemsAnimated:]`, from `_UITabBarControllerAdaptiveVisualStyle
  _verticalBarAvailabilityDidChange`; the run says it reproduced twice in the same pose.
- **Sheets on the leading half in book (T15).** The system places sheets on the leading half while the fold is active
  (Add Account at 8–467.5 pt); the app set no placement.
- **Already right.** In book the system tab sidebar's rows end at 455.3 pt, its background reaches the fold's centre
  (475.5 pt), and the tab's content starts at 495.7 pt, so the boundary is on the fold with no width set (none is set
  anywhere: no T14). Flat sideways the sidebar is the narrower pane (its bar 0–280 pt). In laptop and tent the timeline,
  the post and both settings forms scroll across the horizontal fold, which scrolling content may do (no T1); flat
  upright the fold is inactive. On the cover the tab bar and the navigation items sit in the vertical bar, every item a
  symbol.

## What changed (one commit per change)

- `761fc95`, **post, Settings, Display Settings**: the logged-out tabs are plain `Tab`s, the path the app already takes
  for its iPhone tabs (`AppView.swift`: a comment, and two changed lines, the `if` condition and the tabs it iterates).
  The after capture shows no crash, no lost screen and no returning sheet in any pose. Before that capture the run
  checked the fix with the logging build (Settings in all seven poses) and with probes of the post and Display
  Settings; neither output was kept, so the after capture is the evidence.
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
- **Logged-out tabs, every device**: the same two tabs, in the same order in code. Seen on the Duo (snapshots): the
  sidebar no longer shows the empty section header with its chevron, and the cover's bar lists Timeline above Settings
  (before: Settings above Timeline, on the cover only; the sidebar listed Timeline first both times). On iPad, Mac
  (Catalyst) and visionOS, logged out, the same change applies: Settings was sidebar-only and may now also appear in the
  floating tab bar. Not run.
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
  presented at launch through the router, was captured.
- **A stranger's profile** (`AccountDetailView`) and the two static T10 hits (below).
- **Split View's half window** (475 × 669 pt): the simulator cannot drive it headless.
- **Other devices**: iPad, Mac and visionOS not run, though the logged-out tab change reaches them; Mac Catalyst not
  built.
- **The project's checks**: the package tests its `CLAUDE.md` names (AccountTests, ModelsTests, NetworkTests,
  TimelineTests, EnvTests) and SwiftFormat (`.swiftformat`, 2-space indent) were not run.
- **Files not kept**: the crash report, the logging build's output, the probes' output.
- **Independent review**: none. The skill at the time had no review step.

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
2. **The logged-out tab change reaches every device.** Keep it (`AppView.swift:99`; iPad, Mac and visionOS then use
   plain tabs logged out, and Settings may show in the floating tab bar), or limit it. Not run on those devices.
3. **The sheet rule is a convention.** Every new sheet needs `.foldAwareSheetPlacement()` on its content, or it opens
   on the leading half in book; nothing enforces it.
4. **Upright.** Accept the tab sidebar's upright layout (content full width, sidebar a tap away), or build two panes
   there; Foldsmith's two-pane upright split replaces a `NavigationSplitView`, not a `TabView`.
5. **T10.** Give the two text-only bar buttons a symbol (for example `Button("action.done", systemImage: "checkmark")`)
   so they can go vertical with the bar. Not done here.
6. **Tests, format, Mac Catalyst.** Run the package tests and SwiftFormat on the branch, and build Mac Catalyst (not
   built in the run).

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
