# NetNewsWire, migrated hands-free (2026-10-27)

The first run of the hands-free skill (`skills/foldsmith-duo/SKILL.md`) on a corpus app: one prompt, no questions, run
by a Claude Code agent with the skill and the `iphone-duo-simulator` driver, on the project's iPhone Duo simulator (iOS
27.1, Xcode 27.1). The app was NetNewsWire at d9ec7df (`raw/corpus/NetNewsWire`, a clone made by `raw/corpus/clone.sh`,
not committed), copied outside this repository, with a `duo-baseline` commit; the skill committed its work on
`duo-migration` there.

| File | What it is |
|---|---|
| `REPORT.md` | the skill's report, corrected on 2026-10-31 (its "Corrections" lists what and why): status at a glance, per screen the pattern and the state of each pose, what changed, what runs on devices without a fold, the decisions left to NetNewsWire's maintainers, what was not checked, the known misses and regressions an independent review confirmed (2026-10-31), and what is left |
| `RUN-NOTES.md` | for Foldsmith's team, moved out of `REPORT.md` on 2026-10-31: timing, the skill's references against the SDK and the app, what the corrections and the independent review say about the skill |
| `audit.md` | the audit of the unmodified app and the classification of each screen |
| `migration.json` | the progress file at the end of the run (what the Foldsmith pane reads) |
| `NetNewsWire.flow` | the screens the skill found and the taps that reach them |
| `detectors-before.md`, `detectors-after.md` | `duo-detect` on the 35 snapshots before and after, rerun with the detectors of Phase 4 step 5 (below); the run's own detectors gave 3 findings before and 0 after |
| `NetNewsWire.patch` | the code change, `duo-baseline..duo-migration` without `.duo/` (NetNewsWire is MIT) |
| `LICENSE-MIT-NetNewsWire` | the license of the patch: NetNewsWire's `LICENSE` at d9ec7df, unchanged |
| `commits.txt` | the branch's seven commits, newest first: the feeds follow-up, the report, one per screen (add-feed, settings, timeline, article), then the audit |

Result of the run: five screens, all Fold-aligned split. Timeline, article, settings and Add Feed were done in all seven
poses (each cell verified, that is changed and re-checked, or fine as is); feeds was left in `todo` (T7 in the five
inner poses: unfolding from the feeds list showed an empty timeline beside "No selection"; NetNewsWire has three levels
and P1 described two). The detectors went from 3 findings to 0; the rest (T14 on the feeds and timeline panes in book,
T1 on "No selection" in laptop and tent, T7, T10 on Add Feed's text-only buttons) were seen in the shots only. Captures
took about 76 of the run's minutes (about 65 s a cell: the audit and the proof capture; `RUN-NOTES.md` has the rest).

Follow-up, the same day, after the Foldsmith project's owner decided the three-level question (the sidebar beside the
article): the last commit makes the split two panes on the inner display, the list pane (feeds, then the timeline pushed
on it) and the article (`REPORT.md`, "What changed", feeds). Recaptured with one test run per screen: 35 cells in 915 s,
`duo-detect` 0, every shot checked. All five screens are `done` in migration.json: of the 35 cells, 19 verified and 16
fine as is, none left, and no detector finding after with the run's detectors (G3, Foldsmith's gate of zero detector
findings in every pose, met with those detectors; not with the later T10 detector, below). An independent review on
2026-10-31 ([below](#independent-review-2026-10-31)) then confirmed 15 items, 9 of them regressions (2 high), none
fixed; 6 of the 9 come from this follow-up.

`detectors-before.md` and `detectors-after.md` are rerun with the detectors of Phase 4 step 5, which close the run's
misses: the before captures now show 26 findings (T14 on all three split panes in book, T1 on "No selection" in laptop
and tent, T7 on feeds in every inner pose and on the timeline upright, T10 on Add Feed, T15 on both sheets): everything
the run saw by eye, and T10 on Add Feed in book and flat-sideways, which its audit missed. The after captures show
none. `detectors-before.md` marks 21 classes in 15 cells; the list behind the figure of 26 is not published.

Phase 4 step 9 (2026-10-28, after the Wikipedia run) taught T10 custom-view toolbar items. On these captures it finds
one that the run missed, and the review Foldsmith's
[DEVIATIONS.md](https://github.com/3nchiridion/foldsmith/blob/main/docs/history/DEVIATIONS.md) mentions too (without
saying what that review covered; the run's log records none), and the step-5 detectors with it, before and after alike:
the article's Reader View button, `UIBarButtonItem(customView:)` in `ArticleViewController.swift`, stays beside the
vertical bar on the cover and in the detail pane (8 cells after: article, timeline and feeds in book and flat-sideways,
the article closed and closed-sideways). Not fixed here; `detectors-before.md` and `detectors-after.md` are left as they
were run. The independent review of 2026-10-31 found it again on its own (L-L3, below).

Also not in the run's report until 2026-10-31: upright (laptop, tent, flat-upright) the two panes leave the article a
328 pt pane, and Previous Article, Next Article and Share are no longer in its bars; its navigation bar shows a More
button instead (9 cells, from the after snapshots; the run did not open More). The independent review confirmed both
(L-L1, L-L2, below). `REPORT.md` now opens with them; its "Devices without a fold" lists what runs off the fold, and its
"Corrections" lists every change to it.

The run's own shots and snapshots stay outside the repository, as for every run.

## Independent review (2026-10-31)

After the run, three Claude Code agents with no part in it reviewed the branch from the evidence and the source. Two
worked blind to what the run claimed, reading neither its report, its audit nor its progress file: a layout reviewer
looked at every before and after shot (35 + 35, side by side with the fold drawn) and measured every accessibility
snapshot; a code reviewer read the diff (`20a90a6..duo-migration` without `.duo/`, 5 files, +331 −26; `20a90a6` is
`duo-baseline`), the source around it at both revisions and the 121 lines `tools/routes.sh` lists for the touched
types, and ran SwiftLint 0.63 `--strict` on the changed files. A skeptic then checked each of their items against the
frames, the lines, the shots, the iOS 27.1 SDK headers and Foldsmith's rules. The skeptic saw one line of the run's
audit in a search result and did not use it. Their notes and scripts are not published.

They raised 24 items (layout 10, code 14). The skeptic confirmed 15, 9 of them regressions: 2 high (both regressions),
6 medium (5 regressions), 7 low (2 regressions). Four are marked plausible, not shown (C-R4, C-R8, L-L5, L-L6); C-R1 is
read from the CI file and the SDK headers, not from a build; C-R2, C-R3, C-R5 and L-L4 are read from the code, which no
capture runs. L-L1 and L-L3 were lowered to low. The other 9 were not kept: 5 repeated a confirmed item and were merged
into it (C-R6 into L-L7, C-R7 into L-L5, C-R9 into L-L3, C-R10 into L-L1 and L-L2, C-R11 into L-L6), and 4 were refuted
on the evidence (L-L8, the trailing sheet in book: not a defect, since it carries its own vertical bar, nothing is
clipped and the rows keep their height; L-L9, a coverage note: the article's CSS scales wide content with `max-width:
100%`; L-L10, a process detail: the after shots plainly show `2b2e1dc`'s three-level layout; C-R14: the capture shut
the phone from four inner poses and lost nothing). The part of C-R3 that repeats L-L4 is left in L-L4. Confirmed, most
severe first, with their evidence in `REPORT.md` (Known misses and regressions; lines in the source at `duo-migration`,
snapshots in the run's working copy):
- High, regression (C-R1). The change does not build with the Xcode 26.6 the project's CI selects: it calls iOS 27.0
  and 27.1 SDK symbols behind the runtime `#available` only (`.github/workflows/ci.yml:75-110`;
  `iOS/FoldAlignedSplit.swift:27-57`; `iOS/RootSplitViewController.swift:38`).
- High, regression (C-R2). In every inner pose Mark All as Read still asks for the sidebar, which now brings up an empty
  column that stays until the phone is closed, and it closes the open article (`iOS/SceneCoordinator.swift:1536-1541`,
  `:886`, `:907-910`, `:1969-1971`).
- Medium, regression (L-L2). Upright, the article's Share button has left the detail pane (no node in
  `after/article-{laptop,tent,flat-upright}.json`); whether it is in the top More is unverified.
- Medium, regression (L-L4). In every inner pose the timeline's Show Sidebar became a Back button to the feeds, and
  going Back deselects the feed and closes the open article (`iOS/SceneCoordinator.swift:1969-1971`, `:1248-1250`,
  `:1321-1324`).
- Medium, regression (C-R3). With an article open, that Back pushes the timeline and pops it again in the same run-loop
  turn (`iOS/SceneCoordinator.swift:940-946`, `:1271`, `:2674-2676`).
- Medium, regression, plausible (C-R4). Handoff, Spotlight, Siri, notification and search routes pop and push the list
  pane with no delay, and the flag meant to stop a late deselection is never set (`iOS/SceneCoordinator.swift:97`,
  `:492`, `:528-535`, `:1669-1673`, `:1969`).
- Medium, regression (C-R5). Unfolding drops a Search All started with ⌘F or the Search quick action
  (`iOS/SceneCoordinator.swift:528-535`, `:908`).
- Medium, not a regression, plausible (L-L5). On a launch or scene reconnection directly upright, the 320 pt detail
  minimum is set after the split's first layout pass, which kept the catalog's split an overlay
  (`iOS/RootSplitViewController.swift:45-48`; `iOS/SceneCoordinator.swift:847`). No capture launched upright.
- Low, regression (L-L1). Upright, Previous Article and Next Article left the article's top bar for a system More, one
  tap further, with no `visibilityPriority` to choose what stays (`after/article-laptop.json` nodes 195-197).
- Low, regression, plausible (C-R8). The split's edge swipe is still on and can bring up the empty column of C-R2
  (`iOS/SceneDelegate.swift:25`; `iOS/SceneCoordinator.swift:909`).
- Low, not a regression (L-L3). Reader View still sits outside the vertical bar in 8 cells, as before
  (`iOS/Article/ArticleViewController.swift:143-147`).
- Low, not a regression, plausible (L-L6). In book, Add Folder and the sheets Settings presents itself skip the sheet
  rule (`iOS/SceneCoordinator.swift:1764-1768`; `iOS/Settings/SettingsViewController.swift:582, 634, 658`).
- Low, not a regression (L-L7). Add Feed's symbol buttons are gated on iOS 27.1 only, not on a vertical bar, so they
  reach every iOS 27.1 iPhone and iPad (`iOS/Add/AddFeedView.swift:87-111`).
- Low, not a regression (C-R12). The "Loading…" page shown while Reader View extracts an article still lands on the
  fold in laptop and tent (`iOS/Article/WebViewController.swift:688-689`).
- Low, not a regression (C-R13). Contribution-rule nits: commit subjects, where properties sit, access levels, the new
  file's header, one commit without an LLM note (`Technotes/CodingGuidelines.md:43, 49, 167`; `CONTRIBUTING.md:25`).

What they found right, without the run's claims to go on: T14 fixed in book (the panes meet at 474.7 | 475.7, inside
455.5–495.5; the article's text runs x 514–819, where it ran 340–827 across the fold); T15 fixed for Settings and Add
Feed (from x 8–467.5 to 483.5–943); T7 fixed (the feeds list beside the detail in all five inner poses, where before an
empty timeline or the detail alone showed); two panes upright; "No selection" off the laptop and tent fold (y 444–469 →
286–311); Add Feed's Cancel and Add in the cover's vertical bar, Add still disabled while it should be; the cover poses
and Settings upright unchanged; no detector finding after; no text clipped at a pane's edge, no fixed control on an
active division, no screen lost when the phone shut. In the code: the gating (reserved regions plus `#available`, no
branch on idiom or orientation); devices without a fold as before but for Add Feed's symbols and a harmless
`UIHingeInteraction`; only the NetNewsWire-iOS target compiles the changed files, the Mac app is untouched, and Catalyst
and visionOS are off; every API in the installed iOS 27.1 SDK; everything on the main actor; SwiftLint clean at both
revisions; state restoration (the list pane gets the feeds and the timeline), widget deep links, notifications through
`restoreFeedSelection`, and the sheets presented through the root split. Not verifiable from the captures: what the
upright More holds, Back to the feeds (read from the code only), the sheets presented outside the root split, and a
launch directly upright.
