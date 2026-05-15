# ChroniCal — Chronicle Books Calendar Pipeline App

**Status:** v0.2 shipped 2026-05-14
**Repo:** `github.com/DorcusTitanus/happenstance` (private)
**Local:** `/Volumes/Ghola/Sync/Documents/ClaudeCode/happenstance/`

## What it is
macOS SwiftUI app that wraps the Python calendar pipeline for Chronicle Books. Comps drop an xlsx of holidays/moon phases, pick a year, click two buttons, get InDesign-ready XML.

## Stack
- **Frontend:** Swift + SwiftUI (macOS 14+, Xcode 15+)
- **Backend:** Python 3.12+ subprocess via `PythonRunner.swift`
- **Venv:** `~/Library/Application Support/ChronCal/venv/` with `openpyxl`
- **No sandbox** — needs filesystem access to Ghola sync drive

## Brand
Happenstance aesthetic (Jefe's 2009 brand). Mustard/saffron/cream/charcoal palette, Futura typography, adaptive light/dark mode. Icon: swirl motif with "ChroniCal" type.

## What works (v0.2)
- Drag-drop xlsx input (auto-detects 2028 xlsx on Ghola)
- Year stepper (2024–2032)
- Test Bridge (verifies Python + openpyxl)
- xlsx → events XML extraction
- events → InDesign Calendar XML generation
- Reveal Output in Finder
- Monospaced console output
- Full icon set (16–1024px)
- Light/dark mode via xcassets color sets

## Pipeline scripts
- `xlsx_to_events.py` — extract holidays + moon phases from xlsx to XML
- `plannerBuilderv002.py` — generate InDesign calendar XML from events
- `holidays_to_docx.py` — generate blank Word doc templates

## Repo history
Extracted from `chronicleBooks/calendar-builder/` on 2026-05-14. That repo retains event data and year-specific XSL builds but no longer has gui/, scripts/, or art/ (deleted to prevent drift).

## Next
- Wall/Daily paradigm (Word doc input + design toggles)
- Authoring mode (xlsx → generate blank Word templates)
- Progress feedback, error styling, Run All button
