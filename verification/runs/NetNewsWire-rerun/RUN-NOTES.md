# NetNewsWire rerun — notes for Foldsmith

For Foldsmith's own team, not for NetNewsWire's maintainers: when things happened in the second hands-free run and how
long they took, the run agent's 12 frictions and what plugin 0.8.1 made of each, what the run found wrong or missing in
the skill's references, and what the independent review of 2026-11-01 and the corrections of 2026-11-02 to
[REPORT.md](REPORT.md) say about the skill. The run, its result and the review are in [README.md](README.md). Checked
against [migration.json](migration.json)'s log, the branch's commits ([commits.txt](commits.txt)) and reflog, the run's
working copy, its own build and capture logs (kept outside the app's folder, not published) and the run agent's
closing account (its last message, not published), which is where the frictions and a few times come from; each figure
says which.

## When things happened
Local time (UTC−4 that night): to the minute from migration.json's log; to the second from the commits, the reflog, the
`captured:` lines of the run's `capture.txt` files and the times of its files.
- **Start, 23:10–23:23.** The copy was cloned from the first run's folder at 23:09:05 (reflog). The run agent's first
  command printed 23:10:27, and it read the skill until 23:11 (closing account). Preflight at 23:11 (8.1 GB free, a
  warning; the tap runner rebuilt, its source being newer), `.duo/env` written at 23:11:34, `duo-migration` created
  from `duo-baseline` at 23:11:51. The unmodified build failed at 23:13:31 (`SecretKey.swift` not yet generated) and
  built at 23:14:38; a fresh install downloaded the app's default feeds at 23:15 (closing account). The flow file is
  dated 23:16:13; the five screens were captured on the cover 23:16:19–23:22:56.
- **Audit, 23:23–23:43.** Static scans, the screens and `phase audit` logged at 23:23; audit capture
  23:23:51–23:40:32 (35 cells, 34 detector lines), logged at 23:41 as "every shot looked at, each detector line borne
  out"; `audit.md` at 23:42:40; classification and `phase migrate` at 23:43; `56ade81` (audit and plan) at 23:43:48.
- **Migration, 23:43–00:07.** Five local changes, built and probed (six probe captures, 23:46:02–00:06:43): `0dd7f93`
  (column widths, tiling) at 23:48:54; `e241cb7` (placeholder) and `c091a32` (Reader View) at 23:53:21; `4f6de2c`
  (sheet rule) and `3201df1` (Add Feed's bar items) at 00:07:38; `1c28ea1` (the plan's `made:` lines) at 00:07:53. At
  00:07 the log moves the article's three upright cells from unchanged to migrated (change 1 tiles the timeline beside
  them) and sets `phase verify`.
- **Proof and two late fixes, 00:08–00:45.** Proof capture 00:08:03–00:24:38 (35 cells, 7 detector lines). At 00:27
  the log records I1 (a regression the run's own review found: Reader View in the turned cover's More, with no title),
  I2 (found by eye: Previous, Next and Share no longer in the upright article pane's bars, said to be in its More) and
  the T10 waiver for Add Feed flat-sideways; I2 `wontfix` at 00:28. First fix, `523be11` (Reader View's overflow
  item), committed at 00:27:48; feeds, timeline and article recaptured 00:27:51–00:37:16. I3 at 00:38 (the placeholder
  suspected at 45 % in book and flat sideways). Second fix, `87584b9` (the fold helpers read the window), committed at
  00:38:56; feeds and timeline recaptured 00:38:59–00:45:00.
- **Close, 00:46–00:48.** At 00:46 the log closes I3 as not a defect, citing `87584b9`, marks I1 fixed, waives feeds'
  two cover cells as unchanged and writes the four done lines and the proof line; at 00:46:02 `87584b9` was amended to
  `8e7c888` (the same tree, a new message), so that note cites a commit that is on no branch. `e71d74b` (proof) at
  00:46:30; the App Store set (18 JPEGs) written 00:46:37–00:46:39; the report and `phase done` logged at 00:47;
  `d5cd9a2` (report) at 00:47:56. The run ended at 00:48 with the agent's closing message.

The proof line at 00:46 says feeds, timeline and article were "recaptured twice"; article was recaptured once
(`after/capture.txt`). What each after shot was taken from: settings and Add Feed (00:19:00–00:24:33) from the build
before both late fixes, article (00:35:11–00:37:10) from the build before `87584b9`, feeds and timeline
(00:39:51–00:44:55) from the code the branch ends with (`e71d74b` and `d5cd9a2` change only `.duo/`). The independent
review followed on 2026-11-01, and Foldsmith 0.8.1
([`851b0cf`](https://github.com/3nchiridion/foldsmith/commit/851b0cf)) was committed at 13:19 that day.

## Timing
| Step | Time | Source |
|---|---|---|
| Whole run | 1 h 37 min 29 s, 23:10:27–00:47:56 | the time the agent's first command printed; the report's commit `d5cd9a2` |
| Tap runner rebuild (the preflight's note) | ≈ 10 s | the closing account; the log says only "rebuilt (source newer)" |
| Unmodified build, first try (failed) | 31 s, 23:13:00–23:13:31 | the closing account; its build log is dated 23:13:31 |
| Unmodified build, second try | 36 s, 23:14:02–23:14:38 | the closing account; its build log is dated 23:14:38 |
| Detectors' first `swift build` | 10.6 s | the cover capture's log ("Build complete! (10.58 sec)") |
| Cover capture, 5 screens, closed only | 397 s (6.6 min), 23:16:19–23:22:56 | `reach/capture.txt`'s start; its log and detector table are dated 23:22:56 |
| Audit capture, 35 cells | 1001 s (16.7 min, ≈ 28.6 s a cell), 23:23:51–23:40:32 | log, 23:41 ("16.7 min"); `before/capture.txt`'s start; its log's date |
| Builds during the migration | ≈ 35 s each | the closing account ("six builds"); eight build logs end between 23:45:32 and 00:07:26, with no duration in them |
| Probes, 6 captures, 18 cells | 800 s (13 min 20 s) | each probe's `capture.txt` start to its log's date; the closing account's estimates (3, 3.5, 6, 3, 1.5 and 2 min) add up to 19 min |
| Proof capture, 35 cells | 995 s (16.6 min, ≈ 28.4 s a cell), 00:08:03–00:24:38 | `after/capture.txt`'s start; the capture's log |
| Recapture after `523be11`: feeds, timeline, article, 21 cells | 565 s (9.4 min, ≈ 26.9 s a cell), 00:27:51–00:37:16 | `after/capture.txt`'s first `recaptured:` line; the capture's log |
| Recapture after `87584b9` (now `8e7c888`): feeds, timeline, 14 cells | 361 s (6.0 min, ≈ 25.8 s a cell), 00:38:59–00:45:00 | the second `recaptured:` line; the capture's log and `after/detectors.md` |

Each capture time runs from its `captured:` or `recaptured:` line to the date of the capture's output log, whose last
line is the detectors' count; the run's own "16.7 min" for the audit matches. The captures took 55 min 19 s of the 97
min 29 s (57 %): cover, audit, proof and the two recaptures (397 + 1001 + 995 + 565 + 361 s); with the probes' 800 s,
68 min 39 s (70 %). The two late fixes cost 15 min 26 s of recapture. The agent's own estimates for the probes add up
to 19 min, against the 13 min 20 s their captures took; what the estimates include besides is not
recorded. The audit's ≈ 28.6 s a cell is the rate of the first run's follow-up recapture (≈ 26 s, one test run per
screen), not of its audit (≈ 65 s; [../NetNewsWire/RUN-NOTES.md](../NetNewsWire/RUN-NOTES.md)). Disk: 8.1 GB free at
the preflight (log), 5.5–8 GB throughout and 5.6 GB at the end (closing account); the disk guard never stopped the run.
The log records no review step as such: I1 and I3 carry `found_by: review`, and the run's report says the run reviewed
its own diff, having no way to start a subagent.

## The run agent's frictions, and what 0.8.1 made of them
The 12 the agent listed in its closing account, in its order and shortened, each with what plugin 0.8.1 did, quoted
from its [CHANGELOG](https://github.com/3nchiridion/foldsmith/blob/851b0cf/CHANGELOG.md) ("Plugin 0.8.1").
1. **Launch-argument quoting.** `references/simulator.md` wrote a boolean as `-Key '<false/>'`, but `capture-app.sh`
   sources the flow file and the tap runner splits `LAUNCH_ARGS` at spaces, so the quotes would reach the app as
   characters; the run used none inside the value ([NetNewsWire.flow](NetNewsWire.flow)). 0.8.1: "`LAUNCH_ARGS`
   quoting corrected: no quotes inside the value".
2. **State restoration.** NetNewsWire restores the selected feed and article at launch, so each screen's relaunch
   could start where the last ended; the skill covered only apps that remember onboarding. The run found it in the code
   and pinned it in `LAUNCH_ARGS`. 0.8.1: "New advice on apps that restore their last selection", with this run's keys
   as the example.
3. **A first build that fails.** A fresh checkout's first build fails because the gitignored `SecretKey.swift` is
   generated during the build (failed at 23:13:31, built at 23:14:38). 0.8.1: advice on "a first build that fails on a
   generated source": build once more before diagnosing, and read the target's script phases for generators.
4. **`rg` and zsh.** `rg` was a shell function and calling it through the agent's own executable failed; zsh does not
   split a variable holding `python3 …/progress.py` into words, so the run wrote a wrapper script. 0.8.1: advice on
   "zsh, and how `rg` is found".
5. **T10 on a centred sheet.** Open flat, Add Feed's sheet is centred and has a horizontal bar of its own
   (`toolbarVerticalEdge` is nil inside it, and the window root's `verticalBarEdge` reads unspecified while it is up),
   yet the detector marked its text items T10, and the taxonomy cited this very case as T10 evidence. The agent spent a
   probe and two builds proving the window-level read cannot see a vertical bar there, then waived the line. 0.8.1:
   "T10 checks an item only in a bar that reaches the vertical bar's band", and "A sheet away from that edge keeps a
   horizontal bar of its own"; the taxonomy's T10 evidence changed with it. Under 0.8.1 Add Feed flat-sideways has no
   class before or after, and with the same snapshot before and after it would be fine as is, not verified.
6. **Overflows cannot be opened by tap.** Taps reach only the upright cover, and a menu closes on the next fold;
   nothing said what a custom-view item shows in an overflow, where UIKit gives it no title. The run added a
   `menuRepresentation` by reasoning (`523be11`, I1). 0.8.1: the Fold-aligned split page names `menuRepresentation`
   ("SDK API, not yet measured on a run"), and the gate takes as proof of a moved control "a line of a file git tracks
   that puts it there (an inner pose's overflow cannot be opened by the tap runner)". The tap runner still cannot open
   an overflow.
7. **No subagent, so no fresh review.** 0.8.0's step 6 asked for one "If you can start a subagent"; this session could
   not, and the run reviewed its own diff. 0.8.1: "The independent review is a step with a record":
   `references/review.md`, `progress.py review`, and `/foldsmith-review`, "which runs the review in a new session";
   `phase done` refuses with no review on record, as it does on this run
   ([foldsmith-0.8.1-check.txt](foldsmith-0.8.1-check.txt)).
8. **Long waits.** About 17 min for a full capture and 6–10 min for each recapture of 2–3 screens; two late fixes meant
   two recaptures, and 10-minute shell timeouts meant re-arming the wait loops. Not addressed in 0.8.1: no capture is
   faster. A late fix costs more under it: each after shot records the code it was taken from, and the gate refuses a
   cell captured from "other code than the code now". Here that is settings' and Add Feed's 14 cells and article's 7,
   to recapture (21 cells, about 10 min at the proof's rate) or to waive `stale` with why.
9. **The copy's history.** The copy's `origin/duo-migration` was the first run's branch, which a plain `git checkout
   duo-migration` would have tracked; the run used `git checkout -b` (the reflog: created from `HEAD` at 23:11:51). The
   agent also names a pre-existing `.duo/env`; the copy was cloned at 23:09:05 and the run's preflight wrote the file at
   23:11:34, so what was there before is not recorded. 0.8.1: the branch is made "with `git switch -c duo-migration
   --no-track`", and the preflight adds "a note when a remote `duo-migration` exists and no local one does, a warning
   when the local one tracks it"; its warning about tracked `.duo/` files replaces the one about a tracked `.duo/env`.
10. **References that already knew this app.** The references held the first run's measured NetNewsWire answers (the
    320 pt detail minimum, the Add Feed gate, the Reader View finding, three levels), which shaped this rerun. Not
    addressed in 0.8.1, which adds more of them ([below](#the-caveat)).
11. **What worked.** `progress.py`'s done-line and waive grammar was fine; `appstore.py` and `check` worked first time.
    Nothing to change; 0.8.1's `check` is stricter, and refuses this run's progress file.
12. **Disk.** Tight throughout (5.5–8 GB free); the disk guard never fired. Not addressed in 0.8.1; nothing was asked.

## The skill's references against the SDK and the app
What the run found wrong or missing in the skill it had (0.8.0, `da6b941`), from its report ("Where the references and
the SDK differ"), its log and its frictions, with what 0.8.1 says.
- **Launch arguments.** The quoting in `references/simulator.md` (friction 1). Corrected in 0.8.1.
- **What the app restores, and generated sources.** Nothing on an app that restores its last selection at launch
  (NetNewsWire restores the selected feed and article, and keeps the split's display mode; the flow pins all three
  keys) or on a first build that fails on a source generated during the build (frictions 2 and 3). Both are in 0.8.1's
  `references/simulator.md`.
- **T10 and a centred sheet.** The run's report: on the open inner display `UITraitCollection.verticalBarEdge` on the
  window's root reads unspecified while a centred sheet is up, though the split's vertical bar is still drawn behind
  it; the T10 detector read that bar. The audit's log line (23:41) says each detector line was borne out; at 00:07 the
  run found this one was not. 0.8.1 reads T10 only in a bar that reaches the vertical bar's band: Add Feed flat-sideways
  loses its T10 before and after, and Add Feed in book before keeps only T15. The published tables
  ([detectors-before.md](detectors-before.md), [detectors-after.md](detectors-after.md)) are the run's own, from 0.8.0.
- **A custom view in an overflow.** 0.8.0's step 5 named `axisBehavior = .verticalPreferred`, `visibilityPriority`
  and `additionalOverflowItems`, and nothing on what a custom-view item shows once it lands in an overflow: no title
  (I1, a regression the run found and fixed in `523be11`). 0.8.1's Fold-aligned split page names `menuRepresentation`.
- **API names.** The run's report found those it used as the 27.1 SDK declares them: `UIView.reservedRegions(kind:
  options:)` with `.includeInactive`, `UISheetPresentationController.Placement` and `preferredPlacement` (27.0),
  `UIBarButtonItem.axisBehavior` (27.1), `UITraitCollection.systemTraitsAffectingVerticalBarEdge`,
  `UIHingeInteraction`, `minimumSecondaryColumnWidth` (26.0) and SwiftUI's `toolbarVerticalEdge`. It did not ask
  which SDK the project's CI builds with (C-R1, below).
- **The book boundary (not in the run's report).** The Fold-aligned split page's UIKit section says that in book "the
  system then puts the boundary on the fold: timeline 0–455, article from 495 pt". Those are the fold's own edges, as
  the first run's corrections say ([../NetNewsWire/REPORT.md](../NetNewsWire/REPORT.md)); this run's after snapshots
  measure the panes at 0–474.7 and 475.7–951 pt (`after/timeline-book.json`, not published), as the first run's
  corrected report does. Unchanged at `851b0cf`.
- **Found by the review, not by the run:** no toolchain rule (C-R1), the sheet rule's reach (C-R4) and a placeholder
  rule that names only "No selection" (C-R6); [below](#what-the-independent-review-of-2026-11-01-says-about-the-skill).

## What the independent review of 2026-11-01 says about the skill
The review ([README.md](README.md), Independent review; [REPORT.md](REPORT.md), Known misses and regressions) confirmed
9 items, 3 of them regressions, none fixed on this branch. None of the three shows in a detector table: two are in the
after snapshots, the third in the code and the CI's configuration. Traced to the skill the run had (0.8.0), with what
0.8.1 changed; [foldsmith-0.8.1-check.txt](foldsmith-0.8.1-check.txt) has 0.8.1's gate and toolchain check on this run.
- **C-R1 (high, regression): no toolchain rule.** 0.8.0's step 5 (Migrate) says "Gate iOS 27.1 APIs behind `#available`
  with the pre-Duo layout as fallback", and the Fold-aligned split page the same: a run-time check. Nothing in its
  `SKILL.md` or references names the project's CI or a compile-time guard (a search at `da6b941` finds neither), and
  the run built only with Xcode 27.1. The first run's notes, published at 17:43 on 2026-10-31, called this "not yet
  changed in Foldsmith"; 0.8.0, committed at 23:08 that day, did not change it, and the rerun repeated the defect.
  0.8.1: `tools/sdkguard.py` (the CI's Xcode, the installed Xcodes, and each iOS 27 use added since the base outside
  `#if compiler(>=6.4)`), reference implementations "wrapped whole in `#if compiler(>=6.4)`", a Toolchain section in
  `references/apple.md` and a "Builds with the project's toolchain" line in the report. On this branch sdkguard exits
  1: 11 lines Xcode 26.6 does not compile, 8 availability checks outside the guard. Not shown: that the branch, once
  guarded, builds with Xcode 26.6, which is not installed here; sdkguard reads the source and does not compile it (its
  reading "was tested against swiftc with the guard forced false").
- **L-L1 (medium) and L-L2 (low), regressions: two panes upright.** The decision table prescribes "P1 two panes" for
  hierarchical screens in laptop, tent and flat-upright, which the page's UIKit section gets with `.tile` and
  `minimumSecondaryColumnWidth = 320`: the detail is 328 pt wide, Share leaves its bottom bar, and Previous and Next
  leave its top bar, where a More appears. The first run's review found the same two (its L-L2 and L-L1). 0.8.0's step
  6 already asked the run to check each moved control, "opening the overflow to see it", and to count each extra tap;
  the run could not open an inner pose's overflow, wrote as fact that the three were in More, and filed I2 as a cost,
  not a regression, which `issue wontfix` accepts (it refuses only a regression). As in the first run, a `verified`
  cell held a regression: the article's three upright cells are `verified` with Share gone. 0.8.1: the gate refuses a
  control gone from a cell with no `moved` record backed by a snapshot of that cell, an `--as` label or a tracked line
  of code (on this run, 37 controls); the page adds `visibilityPriority` and `menuRepresentation` for the 328 pt
  detail, not yet measured on a run; the review's first item checks each record and looks for actions that lived only
  on a moved control (Find in Article). Unchanged: the decision table, which still prescribes two panes upright. Two
  limits: 9 of the 37 are the display-mode button, relabelled from "split view horizontally left" to "enter full
  screen" (L-L2), which `moved --as` clears; and for a line of code the gate checks only that its file is tracked and
  the line is not blank, so whether the line puts the control there is the review's to check (`scripts/progress.py` at
  `851b0cf`).
- **L-L3 (medium): feeds T7 left.** The local-changes-only default (plugin 0.7.0) and the decision table make three
  levels in two panes structural, and the page's only recipe for it is the stack swap behind six of the first run's
  regressions; the run proposed it in [PLAN.md](PLAN.md) and did not make it. 0.8.1 adds "six checks a
  `--structural` UIKit three-level change must pass"; no local fix.
- **L-L6 (low): Reader View in the turned cover's More.** The cost of the T10 fix step 5 prescribes
  (`.verticalPreferred`); its missing title (I1) the run found and fixed itself, and no capture opened that More.
  0.8.1's gate lists Reader View on the turned cover among the 37; a `moved` record citing the `menuRepresentation`
  line would clear it. The tap runner still cannot open the overflow.
- **C-R4 (low): the sheet rule's reach.** 0.8.0's UIKit section says to set `preferredPlacement` "before `present`"
  and its Consequences that every sheet's content needs the SwiftUI modifier, without saying how to find every sheet;
  the run set it on the sheets it named and wrote "before every sheet the app presents", while about a dozen sheets
  presented from inside a sheet never get it. 0.8.1: "the sheet rule covers every sheet route, nested sheets
  included" (the `present` lines `tools/routes.sh` lists), and the review's fourth item checks it.
- **C-R6 (low): "Loading…" on the fold.** 0.8.0's rule moves "an empty detail's placeholder" (NetNewsWire: 25 %) and
  names no other message; the run did exactly that. 0.8.1: "every transient message (loading, empty, error) moves off
  the fold", and the review's fifth item.
- **C-R9 (low): the empty page loads twice.** No skill rule is involved; not changed by 0.8.1.
- **C-R10 (low): the project's contribution rules.** The tracked `.duo/` files and the "Duo:" subjects are what 0.8.0's
  `SKILL.md` told the run: "Commit `.duo/migration.json`, `.duo/flows/`, `.duo/PLAN.md` and the reports with it",
  with subjects `Duo: <screen> → <pattern>`; nothing asked it to read the project's guidelines, which put properties
  first. 0.8.1: "The migration never commits `.duo/`", "It reads the project's contribution rules first and follows
  them", the default subject is a present-tense verb, and a cited commit is never amended.
- **The run's own report (what REPORT.md's Corrections correct).** Three done lines say "verified in every pose" over
  cells left unchanged (timeline 5 verified and 2 unchanged, settings 1 and 6, Add Feed 4 and 3), and the proof
  commit's subject says it of four screens: the wording of 0.8.0's own example done line (`"Inbox: Fold-aligned split,
  verified in every pose"`), which the first run's corrections had already flagged. 0.8.1's example reads "4 verified,
  3 unchanged", and the gate refuses such a line "with the counts to write instead" (here, three); the skill's
  description at `851b0cf` still promises "every screen verified in every pose", as a goal. The report also states as
  fact what no capture opened (Previous, Next and Share in More), overstates the sheet rule (C-R4) and says nothing of
  the CI (C-R1): 0.8.1 has the run draft the report before the review, "so the review's last item checks its claims".
  Its trail: I3's note cites `87584b9`, amended away; the settings and Add Feed shots predate both late fixes and
  article's the second, while `8e7c888`'s "No capture changed" rests on feeds and timeline. 0.8.1's gate refuses a
  cited commit not on the branch, "naming the commit with the same tree or subject", and a shot captured from other
  code; the run never amends a cited commit.
- **The gate itself.** 0.8.0's `check` passed a `verified` cell that kept its before class (found after the run, on a
  copy: feeds' five T7 cells marked `verified` passed `check`, `done feeds` and `phase done`). 0.8.1 refuses it.

## The caveat
The references were written from the first run on this same app. At `da6b941`, 26 lines of `SKILL.md` and its
references name NetNewsWire and carry that run's answers (the 320 pt detail minimum, "No selection" at 25 %, the Add
Feed gate, Reader View with `.verticalPreferred`, the three-level recipe), and the copy's `origin` was the first run's
folder, its branch one `git show` away; whether the agent read it cannot be shown. The rerun shows that the first run's
lessons held on the app they came from, not that a first run on an app the references do not name would go as well.
0.8.1 adds more of this app: 49 lines of `SKILL.md` and its references name NetNewsWire at `851b0cf` (both counts by
`git grep` in the plugin's copy of the skill).
