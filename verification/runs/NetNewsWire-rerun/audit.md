# NetNewsWire on iPhone Duo: audit

Unmodified source (`duo-baseline`, 20a90a6), built against the iOS 27.1 SDK (Xcode 27.1) and captured on
the iPhone Duo simulator, every screen in every pose:
`.duo/captures/NetNewsWire/before/` (shots, accessibility snapshots, `detectors.md`). The flow is
`.duo/flows/NetNewsWire.flow`; its launch arguments pin selection restoration, so every screen starts from the
feeds list, and keep the split's saved display mode at its default (`oneBesideSecondary`). Content is the app's own
first-run download of its ten default feeds.

## Static scans (`references/detectors-static.md`)

| Class | Hit | Read |
|---|---|---|
| T13 | `iOS/Resources/Info.plist` has `UIApplicationSceneManifest`; `SceneDelegate` | scene lifecycle present: the app launches on the 27.1 SDK |
| T14 | `SceneCoordinator.init`: minimum, maximum and preferred widths of both the primary (feeds, 300–500 pt) and the supplementary (timeline, 280–440 pt, 320 by default) column | confirmed by the book captures: the timeline ends at 320.5 pt |
| T10 | text-only `ToolbarItem`s in Add Feed (Cancel, Add), Add Folder, Account Setup, Current Activity (Done), Activity Log and Error Log (Copy Contents) | Add Feed confirmed (captured); the others are not on the capture's screens |
| T10 | custom-view items: the article's Reader View (`ArticleExtractorButton`), the feeds and timeline refresh progress | Reader View confirmed; both refresh items are built only before iOS 26 (`#available(iOS 26, *)` returns first), so never on the 27.1 SDK |
| T9, T3 | none | |

## Screens × poses

`·` = no finding. Classes from `references/taxonomy.md`; every shot was looked at, and every detector line is borne
out by its shot.

| Screen | closed | closed-sideways | book | laptop | tent | flat-sideways | flat-upright |
|---|---|---|---|---|---|---|---|
| feeds | · | · | T10 T14 T7 | T1 T7 | T1 T7 | T10 T7 | T7 |
| timeline | · | · | T10 T14 | T1 T7 | T1 T7 | T10 | T7 |
| article | T10 | T10 | T10 T14 | · | · | T10 | · |
| settings | · | · | T15 | · | · | · | · |
| add-feed | T10 | T10 | T10 T15 | · | · | T10 | · |

What each class is, as seen:
- **T14** (book): the split's timeline column ends at 320.5 pt, so the detail pane (an article, or the empty detail)
  runs from 320.5 to 867 pt across the fold; the article's text column measures 340–827 pt over the 455.5–495.5 pt
  division. Cause: the six column widths set in `SceneCoordinator.init`.
- **T7** (feeds, every inner pose): opened from the feeds list, the inner display shows an empty timeline beside
  "No selection" (book, flat sideways) or "No selection" alone (laptop, tent, flat upright); none of the cover's feeds,
  Add, Current Activity or filter is there. (timeline, upright): opened from a timeline, laptop, tent and flat upright
  show only the empty detail; the timeline is gone. Cause: the three-column split shows its supplementary and
  secondary columns when expanded (`oneBesideSecondary`), and upright the detail's system minimum leaves no room for
  the timeline beside it.
- **T1** (feeds, timeline; laptop, tent): the empty detail's "No selection" sits at y 444–469 pt, on the horizontal
  fold (455.5–495.5). It is HTML in the web view, placed at 45 % of the page by `stylesheet.css`.
- **T10**: the article's Reader View, a custom view (`UIBarButtonItem(customView:)`), stays in a horizontal toolbar
  along the bottom while the other items are in the vertical bar: on the cover both ways, and in the detail pane in
  book and flat sideways (also the empty detail's, hence feeds and timeline). Add Feed's Cancel and Add are text-only
  and stay in a horizontal bar wherever the bar is vertical.
- **T15** (book): Settings and Add Feed, both form sheets, sit on the leading half (8–467.5 pt), over the list.

Not findings: the black disc at the top of the article's cover shot is the outer camera; the laptop and tent sheets
span the horizontal fold, but their forms scroll (exempt, `tt-111466 9:28`); flat sideways the split's 320 pt
leading pane ignores the inactive division, which the pattern accepts there (Settings does the same).

## Classification (`references/decision-table.md`)

| Screen | Relationship / mode | Why | Pattern |
|---|---|---|---|
| feeds | hierarchical / viewing | a list whose rows each open a timeline: the first of three levels (feeds, timeline, article) | Fold-aligned split (P1); its local fixes (widths, placeholder, bar item) and, for T7, "Three levels" (structural) |
| timeline | hierarchical / viewing | uniform article rows in one order, each opening its own page (the I1 feed rule) | Fold-aligned split (P1): two panes in every inner pose |
| article | continuous / viewing | one article, read in order, in a `WKWebView`; it is the detail of the timeline | Fold-aligned split's detail pane (Reflowing columns, "When the text is a web page": open it in the detail pane of a P1 split, move its bar items into the vertical bar) |
| settings | hierarchical / editing | a sheet of rows that open pages and toggles | Fold-aligned split's sheet rule (T15: trailing while the book fold is active) |
| add-feed | hierarchical / editing | a sheet: a two-field form and a folder picker it pushes | Fold-aligned split's sheet rule, and the bar rule (items need a symbol and a title) |

Cells: P1 in every inner pose for hierarchical content (book: boundary on the division; laptop, tent, flat upright:
two panes; flat sideways: leading narrower); the rules every pattern carries for T1, T10 and T15.

NetNewsWire's split is a `UISplitViewController` with three columns, so the changes are local (column widths, its
tiling upright, the sheets' placement, a placeholder's position, bar items), gated on the fold or the vertical bar,
except T7 on the feeds screen: the two panes there would be the feeds list and the detail, which needs "Three levels
in two panes" (`references/patterns/fold-aligned-split.md`, UIKit), a structural change. It is proposed in
`.duo/PLAN.md`; its cells stay `finding`.
