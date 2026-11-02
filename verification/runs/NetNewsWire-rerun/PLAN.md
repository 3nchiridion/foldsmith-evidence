# NetNewsWire on iPhone Duo: plan

Local changes are made by this run, one commit each, gated on the fold or the vertical bar; structural changes are
proposals only (`/foldsmith-start --structural` makes them). See `.duo/audit.md` for the findings.

## Local

1. **timeline, article, feeds → Fold-aligned split: column widths and tiling** made: 0dd7f93. (`iOS/RootSplitViewController.swift`).
   Where the window has a fold (`view.reservedRegions(kind: .division, options: .includeInactive)` not empty, iOS
   27.1), set the six primary and supplementary widths to `automaticDimension`, so the system puts the boundary on
   the fold in book (T14), and the detail's minimum to 320 pt (`minimumSecondaryColumnWidth`), so the timeline tiles
   beside the detail upright (T7 on timeline in laptop, tent, flat upright), set in `viewWillLayoutSubviews` before
   `super`. Without a fold the app's own widths and minimum come back, and the column widths are not saved to the
   app's defaults while the fold sets them (`SceneCoordinator.saveTimelineWidth`/`saveSidebarWidth`).
2. **timeline, feeds → placeholder off the fold** made: e241cb7. (`iOS/Article/WebViewController.swift`). Where the window has a
   fold, the empty detail's "No selection" sits at 25 % of the pane instead of 45 % (T1 in laptop, tent); the empty
   page is drawn again when the fold comes or goes.
3. **article, timeline, feeds → Reader View into the vertical bar** made: c091a32. (`iOS/Article/ArticleViewController.swift`).
   The custom-view item keeps its view and is set `axisBehavior = .verticalPreferred` where the window has a vertical
   bar (`traitCollection.verticalBarEdge` not unspecified), `.automatic` elsewhere (T10).
4. **settings, add-feed → sheet rule** made: 4f6de2c. (`iOS/Duo/FoldAwareSheets.swift`, the presentation sites in
   `SceneCoordinator`). Sheets get `sheetPresentationController.preferredPlacement = .trailing` while a vertical
   division is active, `.automatic` otherwise, set before `present` and changed inside `animateChanges` for a sheet
   already up when the fold changes (a `UIHingeInteraction` on the split's view asks for a layout) (T15).
5. **add-feed → bar items with symbols** made: 3201df1. (`iOS/Add/AddFeedView.swift`). Where the window has a vertical bar
   (`toolbarVerticalEdge` not nil), Cancel and Add are buttons with a title and a symbol (`xmark`, `checkmark`); the
   text buttons stay elsewhere (T10). Open flat sideways the sheet is centred and its own bar horizontal, so the text
   buttons stay there, as Apple places a centred sheet's items.

## Structural (proposed)

### Feeds: three levels in two panes (Fold-aligned split, "Three levels", UIKit)

- **What**: while the window has a fold and the split is expanded, the two panes are the list pane and the detail.
  The list pane navigates the first two levels: the supplementary column's navigation stack holds the feeds list,
  with the timeline pushed on top once a feed is picked; the primary column's stack holds an empty controller;
  `displayModeButtonVisibility = .never`. Back in the list pane returns to the feeds. On collapse,
  `splitViewController(_:topColumnForCollapsingToProposedTopColumn:)` puts the stacks back and answers from the app's
  own state (article if one is open, else the timeline if a feed is picked, else the feeds). Not by moving
  controllers between columns with `setViewController(_:for:)`, and not in `viewWillTransition`
  (`references/patterns/fold-aligned-split.md`, UIKit, "Three levels").
- **Why**: opened from the feeds list, every inner pose shows an empty timeline beside "No selection", or "No
  selection" alone, and the cover's feeds list, Add and Current Activity are nowhere (T7). A feed picked from it is
  pushed onto a stack it was never on, so the change reroutes navigation: structural even inside the app's own
  split (`references/decision-table.md`, "Local and structural changes").
- **Cells it fixes**: feeds book, laptop, tent, flat-sideways, flat-upright (T7).
- **Commits**: none (not asked for: the run had no `--structural`).
- **Risks to check when made**: every route below that walks the split's columns or navigation stacks
  (`viewController(for:)`, `navigationController`, `isCollapsed`, state restoration of the selected feed and article,
  Mark All as Read's `markAsReadAndShowSidebar`, keyboard navigation between columns, user activities, notifications,
  shortcuts), and the iPad, where the split has no fold and must stay three columns.
- **Routes** (`bash tools/routes.sh . SceneCoordinator RootSplitViewController MainFeedCollectionViewController
  MainTimelineViewController`):

| file:line | kind | code |
|---|---|---|
| iOS/Article/ArticleViewController.swift:40 | walk | `return pageViewController?.viewControllers?.first as? WebViewController` |
| iOS/Article/ArticleViewController.swift:87 | set | `self.pageViewController.setViewControllers([controller], direction: .forward, animated: false, completion: nil)` |
| iOS/Article/ArticleViewController.swift:114 | restore | `var restoreState: State?` |
| iOS/Article/ArticleViewController.swift:185 | restore | `if let state = restoreState {` |
| iOS/Article/ArticleViewController.swift:201 | set | `self.pageViewController.setViewControllers([controller], direction: .forward, animated: false, completion: nil)` |
| iOS/Article/ArticleViewController.swift:230 | walk | `navigationController?.navigationBar.topItem?.subtitle = nil` |
| iOS/Article/ArticleViewController.swift:235 | walk | `if let parentNavController = navigationController?.parent as? UINavigationController,` |
| iOS/Article/ArticleViewController.swift:461 | walk | `navigationController?.setToolbarHidden(true, animated: true)` |
| iOS/Article/ArticleViewController.swift:469 | walk | `navigationController?.setToolbarHidden(false, animated: true)` |
| iOS/Article/ArticleViewController.swift:548 | set | `self.pageViewController.setViewControllers([pending], direction: .forward, animated: false, completion: nil)` |
| iOS/Article/ArticleViewController.swift:605 | walk | `guard let gestureRecognizer = poppableDelegate.navigationController?.interactivePopGestureRecognizer,` |
| iOS/Article/WebViewController.swift:127 | walk | `if presentedViewController is UIActivityViewController {` |
| iOS/Article/WebViewController.swift:267 | walk | `navigationController?.setNavigationBarHidden(false, animated: animated)` |
| iOS/Article/WebViewController.swift:268 | walk | `navigationController?.setToolbarHidden(false, animated: animated)` |
| iOS/Article/WebViewController.swift:280 | walk | `navigationController?.setNavigationBarHidden(true, animated: true)` |
| iOS/Article/WebViewController.swift:281 | walk | `navigationController?.setToolbarHidden(true, animated: true)` |
| iOS/Article/WebViewController.swift:351 | present | `present(activityViewController, animated: true)` |
| iOS/Article/WebViewController.swift:494 | present | `self.present(alert, animated: true, completion: nil)` |
| iOS/Article/WebViewController.swift:777 | walk | `if navigationController?.navigationBar.isHidden ?? false {` |
| iOS/Article/WebViewController.swift:859 | walk | `if navigationController?.isNavigationBarHidden ?? false {` |
| iOS/Article/WebViewController.swift:933 | url | `func openURL(_ url: URL) {` |
| iOS/Article/WebViewController.swift:943 | url | `func openURLInSafariViewController(_ url: URL) {` |
| iOS/Article/WebViewController.swift:952 | present | `present(viewController, animated: true)` |
| iOS/MainFeed/MainFeedCollectionViewController.swift:136 | walk | `navigationController?.isToolbarHidden = false` |
| iOS/MainFeed/MainFeedCollectionViewController.swift:142 | walk | `self.navigationController?.navigationBar.prefersLargeTitles = true` |
| iOS/MainFeed/MainFeedCollectionViewController.swift:148 | walk | `guard self.navigationController?.topViewController === self else {` |
| iOS/MainFeed/MainFeedCollectionViewController.swift:151 | walk | `self.navigationController?.navigationBar.sizeToFit()` |
| iOS/MainFeed/MainFeedCollectionViewController.swift:280 | present | `self.present(alert, animated: true)` |
| iOS/MainFeed/MainFeedCollectionViewController.swift:699 | present | `present(vc, animated: true)` |
| iOS/MainFeed/MainFeedCollectionViewController.swift:983 | present | `appDelegate.manualRefresh(errorHandler: ErrorHandler.present(self))` |
| iOS/MainFeed/MainFeedCollectionViewController.swift:1020 | present | `present(alertController, animated: true)` |
| iOS/MainFeed/MainFeedCollectionViewController.swift:1473 | present | `self.present(alertController, animated: true) {` |
| iOS/MainFeed/MainFeedCollectionViewController.swift:1508 | present | `self.present(alertController, animated: true)` |
| iOS/MainFeed/MainFeedCollectionViewController.swift:1514 | present | `let deleteCommand = DeleteCommand(nodesToDelete: [deleteNode], undoManager: undoManager, errorHandler: ErrorHandler.present(self)) else {` |
| iOS/MainTimeline/MainTimelineViewController.swift:208 | walk | `guard let gesture = self.navigationController?.interactivePopGestureRecognizer as? UIPanGestureRecognizer else {` |
| iOS/MainTimeline/MainTimelineViewController.swift:232 | walk | `self.navigationController?.isToolbarHidden = false` |
| iOS/MainTimeline/MainTimelineViewController.swift:235 | walk | `if navigationController?.navigationBar.isHidden ?? false {` |
| iOS/MainTimeline/MainTimelineViewController.swift:236 | walk | `navigationController?.navigationBar.alpha = 0` |
| iOS/MainTimeline/MainTimelineViewController.swift:256 | walk | `if navigationController?.navigationBar.alpha == 0 {` |
| iOS/MainTimeline/MainTimelineViewController.swift:258 | walk | `self.navigationController?.navigationBar.alpha = 1` |
| iOS/MainTimeline/MainTimelineViewController.swift:442 | present | `appDelegate.manualRefresh(errorHandler: ErrorHandler.present(self))` |
| iOS/MainTimeline/MainTimelineViewController.swift:832 | present | `self.present(alert, animated: true)` |
| iOS/MainTimeline/MainTimelineViewController.swift:1476 | present | `present(activityViewController, animated: true)` |
| iOS/MainTimeline/MarkAsReadAlertController.swift:35 | present | `controller.present(alertController, animated: true)` |
| iOS/RootSplitViewController.swift:167 | present | `appDelegate.manualRefresh(errorHandler: ErrorHandler.present(self))` |
| iOS/SceneCoordinator.swift:122 | restore | `var stateRestorationActivity: NSUserActivity {` |
| iOS/SceneCoordinator.swift:123 | restore | `activityManager.stateRestorationActivity` |
| iOS/SceneCoordinator.swift:373 | walk | `self.mainFeedCollectionViewController?.navigationController?.delegate = self` |
| iOS/SceneCoordinator.swift:378 | walk | `self.mainTimelineViewController?.navigationController?.delegate = self` |
| iOS/SceneCoordinator.swift:382 | walk | `self.articleViewController?.navigationController?.delegate = self` |
| iOS/SceneCoordinator.swift:411 | restore | `func restoreWindowState(activity: NSUserActivity?, restoreSelection: Bool) {` |
| iOS/SceneCoordinator.swift:413 | restore | `restoreWindowState(stateInfo, restoreSelection: restoreSelection)` |
| iOS/SceneCoordinator.swift:416 | restore | `private func restoreWindowState(_ stateInfo: StateRestorationInfo, restoreSelection: Bool) {` |
| iOS/SceneCoordinator.swift:417 | restore | `Self.logger.debug("SceneCoordinator: restoreWindowState")` |
| iOS/SceneCoordinator.swift:487 | restore | `func handle(_ activity: NSUserActivity) {` |
| iOS/SceneCoordinator.swift:514 | notification | `func handle(_ response: UNNotificationResponse) {` |
| iOS/SceneCoordinator.swift:1359 | walk | `if let coordinator = self.mainTimelineViewController?.navigationController?.transitionCoordinator {` |
| iOS/SceneCoordinator.swift:1566 | walk | `let settingsNavController = UIStoryboard.settings.instantiateInitialViewController() as! UINavigationController` |
| iOS/SceneCoordinator.swift:1567 | walk | `let settingsViewController = settingsNavController.topViewController as! SettingsViewController` |
| iOS/SceneCoordinator.swift:1571 | present | `rootSplitViewController.present(settingsNavController, animated: true)` |
| iOS/SceneCoordinator.swift:1575 | swiftui | `let hostingController = UIHostingController(rootView: NavigationStack { CurrentActivityView() })` |
| iOS/SceneCoordinator.swift:1580 | present | `rootSplitViewController.present(hostingController, animated: true)` |
| iOS/SceneCoordinator.swift:1585 | walk | `UIStoryboard.inspector.instantiateViewController(identifier: "AccountInspectorNavigationViewController") as! UINavigationController` |
| iOS/SceneCoordinator.swift:1586 | walk | `let accountInspectorController = accountInspectorNavController.topViewController as! AccountInspectorViewController` |
| iOS/SceneCoordinator.swift:1591 | present | `rootSplitViewController.present(accountInspectorNavController, animated: true)` |
| iOS/SceneCoordinator.swift:1597 | present | `rootSplitViewController.present(hostingController, animated: true)` |
| iOS/SceneCoordinator.swift:1609 | walk | `UIStoryboard.inspector.instantiateViewController(identifier: "FeedInspectorNavigationViewController") as! UINavigationController` |
| iOS/SceneCoordinator.swift:1610 | walk | `let feedInspectorController = feedInspectorNavController.topViewController as! FeedInspectorViewController` |
| iOS/SceneCoordinator.swift:1614 | present | `rootSplitViewController.present(feedInspectorNavController, animated: true)` |
| iOS/SceneCoordinator.swift:1629 | present | `rootSplitViewController.present(hostingController, animated: true)` |
| iOS/SceneCoordinator.swift:1633 | present | `self.rootSplitViewController.present(hostingController, animated: true)` |
| iOS/SceneCoordinator.swift:1641 | present | `mainFeedCollectionViewController.present(hostingController, animated: true)` |
| iOS/SceneCoordinator.swift:1652 | present | `rootSplitViewController.present(navController, animated: true)` |
| iOS/SceneCoordinator.swift:1757 | walk | `guard let presentedController = mainFeedCollectionViewController.presentedViewController else {` |
| iOS/SceneCoordinator.swift:1895 | walk | `if navController.topViewController === targetViewController {` |
| iOS/SceneCoordinator.swift:1900 | walk | `} else if navController.viewControllers.contains(targetViewController) {` |
| iOS/SceneCoordinator.swift:1903 | pop | `navController.popToViewController(targetViewController, animated: true)` |
| iOS/SceneDelegate.swift:45 | shortcut | `connectionOptions.shortcutItem != nil \|\|` |
| iOS/SceneDelegate.swift:48 | restore | `coordinator.restoreWindowState(activity: session.stateRestorationActivity, restoreSelection: !hasPendingExternalAction)` |
| iOS/SceneDelegate.swift:55 | url | `self.scene(scene, openURLContexts: connectionOptions.urlContexts)` |
| iOS/SceneDelegate.swift:59 | shortcut | `if let shortcutItem = connectionOptions.shortcutItem {` |
| iOS/SceneDelegate.swift:60 | shortcut | `handleShortcutItem(shortcutItem)` |
| iOS/SceneDelegate.swift:76 | shortcut | `func windowScene(_ windowScene: UIWindowScene, performActionFor shortcutItem: UIApplicationShortcutItem, completionHandler: @escaping (Bool) -> Void) {` |
| iOS/SceneDelegate.swift:78 | shortcut | `handleShortcutItem(shortcutItem)` |
| iOS/SceneDelegate.swift:82 | restore | `func scene(_ scene: UIScene, continue userActivity: NSUserActivity) {` |
| iOS/SceneDelegate.swift:98 | restore | `func stateRestorationActivity(for scene: UIScene) -> NSUserActivity? {` |
| iOS/SceneDelegate.swift:99 | restore | `return coordinator.stateRestorationActivity` |
| iOS/SceneDelegate.swift:104 | notification | `func handle(_ response: UNNotificationResponse) {` |
| iOS/SceneDelegate.swift:119 | url | `func scene(_ scene: UIScene, openURLContexts urlContexts: Set<UIOpenURLContext>) {` |
| iOS/SceneDelegate.swift:220 | shortcut | `func handleShortcutItem(_ shortcutItem: UIApplicationShortcutItem) {` |
| iOS/SceneDelegate.swift:221 | shortcut | `switch shortcutItem.type {` |
| iOS/Settings/SettingsViewController.swift:239 | pop | `self?.navigationController?.popViewController(animated: false)` |
| iOS/Settings/SettingsViewController.swift:241 | push | `self.navigationController?.pushViewController(UIHostingController(rootView: addAccountView), animated: true)` |
| iOS/Settings/SettingsViewController.swift:245 | push | `self.navigationController?.pushViewController(controller, animated: true)` |
| iOS/Settings/SettingsViewController.swift:271 | push | `self.navigationController?.pushViewController(timeline, animated: true)` |
| iOS/Settings/SettingsViewController.swift:279 | push | `self.navigationController?.pushViewController(articleThemes, animated: true)` |
| iOS/Settings/SettingsViewController.swift:310 | push | `self.navigationController?.pushViewController(viewController, animated: true)` |
| iOS/Settings/SettingsViewController.swift:328 | push | `self.navigationController?.pushViewController(hosting, animated: true)` |
| iOS/Settings/SettingsViewController.swift:523 | present | `presentingParentController?.present(hostingController, animated: true)` |
| iOS/Settings/SettingsViewController.swift:558 | present | `self.present(alert, animated: true)` |
| iOS/Settings/SettingsViewController.swift:582 | present | `self.present(documentPicker, animated: true)` |
| iOS/Settings/SettingsViewController.swift:614 | present | `self.present(alert, animated: true)` |
| iOS/Settings/SettingsViewController.swift:634 | present | `self.present(docPicker, animated: true)` |
| iOS/Settings/SettingsViewController.swift:658 | present | `present(vc, animated: true)` |

104 lines: set 3 · push 6 · pop 2 · present 30 · swiftui 1 · restore 13 · url 4 · notification 2 · shortcut 7 · walk 36
