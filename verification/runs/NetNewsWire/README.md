# NetNewsWire, migrated hands-free (2026-10-27)

The first run of the hands-free skill (`skills/foldsmith-duo/SKILL.md`) on a corpus app: one prompt, no questions, run
by a Claude Code agent with the skill and the `iphone-duo-simulator` driver, on the project's iPhone Duo simulator (iOS
27.1, Xcode 27.1). The app was NetNewsWire at d9ec7df (`raw/corpus/NetNewsWire`, a clone made by `raw/corpus/clone.sh`,
not committed), copied outside this repository, with a `duo-baseline` commit; the skill committed its work on
`duo-migration` there.

| File | What it is |
|---|---|
| `REPORT.md` | the skill's report, corrected on 2026-10-31 (its "Corrections" lists what and why): status at a glance, per screen the pattern and the state of each pose, what changed, what runs on devices without a fold, the decisions left to NetNewsWire's maintainers, what was not checked and what is left |
| `RUN-NOTES.md` | for Foldsmith's team, moved out of `REPORT.md` on 2026-10-31: timing, the skill's references against the SDK and the app, what the corrections say about the skill |
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
`duo-detect` 0, every shot checked. All five screens are done: of the 35 cells, 19 verified and 16 fine as is, none
left, and no detector finding after with the run's detectors (G3, Foldsmith's gate of zero detector findings in every
pose, met with those detectors; not with the later T10 detector, below).

`detectors-before.md` and `detectors-after.md` are rerun with the detectors of Phase 4 step 5, which close the run's
misses: the before captures now show 26 findings (T14 on all three split panes in book, T1 on "No selection" in laptop
and tent, T7 on feeds in every inner pose and on the timeline upright, T10 on Add Feed, T15 on both sheets): everything
the run saw by eye, and T10 on Add Feed in book and flat-sideways, which its audit missed. The after captures show
none. `detectors-before.md` marks 21 classes in 15 cells; the list behind the figure of 26 is not published.

Phase 4 step 9 (2026-10-28, after the Wikipedia run) taught T10 custom-view toolbar items. On these captures it finds
one the run and its review missed (Foldsmith's
[DEVIATIONS.md](https://github.com/3nchiridion/foldsmith/blob/main/docs/history/DEVIATIONS.md) mentions that review
without saying what it covered; the run's log records none), and the step-5 detectors with it, before and after alike:
the article's Reader View button, `UIBarButtonItem(customView:)` in `ArticleViewController.swift`, stays beside the
vertical bar on the cover and in the detail pane (8 cells after: article, timeline and feeds in book and flat-sideways,
the article closed and closed-sideways). Not fixed here; `detectors-before.md` and `detectors-after.md` are left as they
were run.

Also not in the run's report until 2026-10-31: upright (laptop, tent, flat-upright) the two panes leave the article a
328 pt pane, and Previous Article, Next Article and Share are no longer in its bars; its navigation bar shows a More
button instead (9 cells, from the after snapshots; the run did not open More). `REPORT.md` now opens with both; its
"Devices without a fold" lists what runs off the fold, and its "Corrections" lists every change to it.

The run's own shots and snapshots stay outside the repository, as for every run.
