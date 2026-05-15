# ChroniCal — Chronicle Books Calendar Pipeline App

**Status:** v0.3 shipped 2026-05-14
**Repo:** `github.com/DorcusTitanus/happenstance` (private)
**Local:** `/Volumes/Ghola/Sync/Documents/ClaudeCode/happenstance/`

## What it is
macOS SwiftUI app that wraps the Python calendar pipeline for Chronicle Books. Comps drop an xlsx of holidays/moon phases, pick a year, toggle design options, get InDesign-ready XML in a syntax-highlighted editor.

## Stack
- **Frontend:** Swift + SwiftUI (macOS 14+, Xcode 15+)
- **Backend:** Python 3.12+ subprocess via `PythonRunner.swift`
- **Venv:** `~/Library/Application Support/ChroniCal/venv/` with `openpyxl`
- **No sandbox** — needs filesystem access to Ghola sync drive

## Brand
Happenstance aesthetic (Jefe's 2009 brand). Mustard/saffron/cream/charcoal palette, Futura typography, adaptive light/dark mode. Icon: swirl motif with "ChroniCal" type.

## What works (v0.3)
- Sidebar with product selector (Planner/Wall/Daily), year stepper, design toggles
- Dual drop zones: xlsx (holidays/moons) + docx (Word template)
- Syntax-highlighted XML editor with find bar and save
- **Design toggles** (all wired to CLI flags):
  - Six-week grids, HolidayCont after MoonPhase, MoonPhase icon only, Year after month
- Pipeline: xlsx → events XML → InDesign Calendar XML
- PreviousYear block (Sept–Dec, 5-week grid cap)
- Correct element order: Date > Moon > Holiday with spacer placeholders
- Verified against 2027 Totoro Wall Calendar production XML
- Light/dark mode, full icon set (16–1024px)

## Pipeline scripts
- `xlsx_to_events.py` — extract holidays + moon phases from xlsx to XML
- `plannerBuilderv002.py` — generate InDesign calendar XML from events (accepts toggle flags)
- `holidays_to_docx.py` — generate blank Word doc templates

## Key files
- `gui/ChronCal/ChronCal/ContentView.swift` — sidebar + editor + pipeline wiring
- `gui/ChronCal/ChronCal/XMLEditorView.swift` — NSTextView syntax highlighting
- `gui/ChronCal/ChronCal/Theme.swift` — Hap palette, fonts, button style

## Repo history
Extracted from `chronicleBooks/calendar-builder/` on 2026-05-14. That repo retains event data and year-specific XSL builds but no longer has gui/, scripts/, or art/.

## Next
- Wall/Daily paradigm (Word doc input pipeline)
- Wire prevYearGraphic toggle
- Error styling (stderr in saffron)
