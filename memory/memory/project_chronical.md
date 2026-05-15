# ChroniCal — Chronicle Books Calendar Pipeline App

**Status:** v0.3 (v0.4 pending — significant arch change 2026-05-15 not yet versioned in UI)
**Repo:** `github.com/DorcusTitanus/happenstance` (private)
**Local:** `~/sync/Documents/ClaudeCode/happenstance/` (via per-machine symlink — see `feedback_sync_symlink.md`)

## What it is
macOS SwiftUI app that wraps a Python calendar pipeline for Chronicle Books. Comps drop an xlsx of holidays/moon phases, pick a year, toggle design options, get InDesign-ready XML in a syntax-highlighted editor. **Self-contained .app** — scripts bundled inside, no filesystem dependencies on sync drives, repos, or specific machines.

## Stack
- **Frontend:** Swift + SwiftUI (macOS 14+, Xcode 26+)
- **Backend:** Python 3.9+ subprocess via `PythonRunner.swift`
- **Venv:** `~/Library/Application Support/ChroniCal/venv/` with **openpyxl + python-docx**
- **No sandbox** — needs filesystem access for user-dropped xlsx and `~/Documents/ChroniCal/` writes

## Architecture (post-2026-05-15)

### Scripts: bundled inside the .app
Canonical location: `gui/ChronCal/ChronCal/Scripts/` (also where Xcode pulls them from for Copy Bundle Resources).

Bundled into: `ChroniCal.app/Contents/Resources/{plannerBuilderv002,xlsx_to_events,holidays_to_docx}.py`

Swift finds them via `Bundle.main.url(forResource: "<name>", withExtension: "py")`. No `repoRoot` constant, no `/Volumes/...` hardcodes.

**Legacy duplicates at `~/sync/Documents/ClaudeCode/happenstance/scripts/`** are stale post-2026-05-15. If Jefe asks to "edit the script," confirm which copy he means; canonical is the in-app one.

### Output: `~/Documents/ChroniCal/`
Filename pattern: `Calendar_{year}_{product}.xml` (e.g., `Calendar_2028_Wall.xml`). Product is one of `Planner`, `Wall`, `Daily`. Folder is created lazily via `ensureOutputDirectory()` on first Build click. Reveal in Finder button targets this folder.

### Events intermediate
`/tmp/{year}_events.xml`. Year-stamped to avoid year-to-year collisions during the same session. Survives until macOS tmp cleanup.

## Brand
Happenstance aesthetic (Jefe's 2009 brand). Mustard/saffron/cream/charcoal palette, Futura typography, adaptive light/dark mode. Icon: swirl motif with "ChroniCal" type.

## What works
- Sidebar with product selector (Planner/Wall/Daily), year stepper, design toggles
- Dual drop zones: xlsx (holidays/moons) + docx (Word template — not yet consumed by pipeline)
- Syntax-highlighted XML editor with find bar and save
- **Design toggles** (all wired to CLI flags):
  - Six-week grids → `--six-week-grids`
  - HolidayCont after MoonPhase → `--holiday-cont-after-moon`
  - MoonPhase icon only → `--moon-icon-only`
  - Year after month → `--no-year-after-month` (inverted)
  - PrevYear date graphic → visible only when Wall selected; **still not consumed by Python** (TODO)
- **Wall mode** (2026-05-15): `--wall` flag added; gates the `if y == year:` weekly section in `plannerBuilderv002.py`. Wall XML matches production 2027 Totoro reference (zero MonthWeekly/DateWeekly, identical Month/Date/Moon/Holiday/Spacer/GhostDate/MiniCal census).
- Pipeline: xlsx → events XML → InDesign Calendar XML
- PreviousYear block (Sept–Dec, 5-week grid cap, no parity with main calendar)
- Correct element order: Date > Moon > Holiday with spacer placeholders
- Verified against 2027 Totoro Wall Calendar production XML
- Light/dark mode, full icon set (16–1024px)

## Pipeline scripts (canonical = bundled)

- **`xlsx_to_events.py`** — extracts holidays + moon phases from xlsx to events XML. CLI: `--input` `--output`.
- **`plannerBuilderv002.py`** — generates InDesign calendar XML from events. CLI: `--year` `--events` `--output`, plus toggle flags `--six-week-grids` `--holiday-cont-after-moon` `--no-year-after-month` `--moon-icon-only` `--wall`. `--output` is now **required** (2026-05-15) — no more cwd-relative `output/Calendar_{year}.xml`.
- **`holidays_to_docx.py`** — generates blank Word doc templates for the Word-template path (not yet wired into ChroniCal UI). CLI: `--input` `--output` `--year`.

`build_holidays_docx.js` at the legacy `scripts/` location is a Node companion, unrelated to the Python pipeline.

## Key Swift files
- `gui/ChronCal/ChronCal/ContentView.swift` — sidebar + editor + pipeline wiring. Holds `outputDirectory`, `scriptURL(_:)`, `ensureOutputDirectory()`.
- `gui/ChronCal/ChronCal/XMLEditorView.swift` — NSTextView syntax highlighting
- `gui/ChronCal/ChronCal/Theme.swift` — Hap palette, fonts, button style
- `gui/ChronCal/ChronCal/PythonRunner.swift` — async Process wrapper; venv at `~/Library/Application Support/ChroniCal/venv/bin/python3`

## Repo history
- 2026-05-14: v0.3 shipped. Extracted from `chronicleBooks/calendar-builder/` on this date. That repo retains event data and year-specific XSL builds but no longer has gui/, scripts/, or art/.
- **2026-05-15: Major arch change** — bundled scripts, removed `repoRoot`, moved output to `~/Documents/ChroniCal/`, added `--wall` flag, filename includes product. Per `feedback_apps_ship_self_contained.md` global default.

## Known TODOs
- Wire `prevYearGraphic` toggle to a Python flag (UI exists, flag doesn't)
- Wall/Daily Word-doc input paradigm (docx drop zone wired in UI, no pipeline consumer)
- Error styling (stderr in saffron color in editor on script failure)
- Decide fate of legacy `scripts/` directory at repo root (delete vs rename vs leave)
- Bump visible version label from "v0.3" → "v0.4" to reflect arch change
- Python deps not bundled — venv is created per-machine. For a true ship-to-Happenstance .app, need to either bundle Python (py2app/PyInstaller pattern) or pivot to native Swift.

## Verification
The Wall XML output is verifiable against the production 2027 Totoro wall via element census:
```bash
diff <(grep -oE '<[A-Za-z][A-Za-z_:]*' ~/Documents/ChroniCal/Calendar_2028_Wall.xml | sort | uniq -c | sort -rn) \
     <(grep -oE '<[A-Za-z][A-Za-z_:]*' "/Volumes/tabr/HappenstanceWork/My Neighbor Totoro 2028 Wall Calendar/Archive/2027WallCal_MyNeighborTotoro_INT_mechs-V7.xml" | sort | uniq -c | sort -rn)
```
Element *types* should match (MonthWeekly/DateWeekly absent from both); counts differ year-over-year.

## Mounted paths Jefe uses for ChroniCal context (Shai-Hulud)
- `~/sync/Documents/ClaudeCode/happenstance/` — repo
- `~/sync/Documents/ClaudeCode/calendars/2028/` — 2028 deliverables (xlsx, docx, build_wall_2028.py one-off)
- `/Volumes/tabr/HappenstanceWork/Chronicle/calendars/{year}/` — canonical NAS archive of past years
- `~/Documents/ChroniCal/` — ChroniCal's own output folder (new 2026-05-15)
