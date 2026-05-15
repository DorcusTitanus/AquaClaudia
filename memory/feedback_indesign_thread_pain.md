---
name: InDesign text-threading pain on ChroniCal layouts
description: Open question — why master-page text threads don't survive XML Place re-imports for week-grid changes
type: feedback
status: unverified
---

# InDesign Text-Threading After XML Place

## The pain
On ChroniCal-style calendar layouts, switching between 5-week and 6-week grids (or toggling other XML structural changes) forces Jefe to relink text boxes manually, even though they are threaded on the master page. This is a real production cost — every iteration on a wall template means re-doing thread connections.

## Tentative answer (UNVERIFIED — investigate before relying)
The canonical InDesign pattern for auto-flowing text is:

1. **Primary Text Frame** — a master-page property (since InDesign CS6) that designates a frame as the document's main story carrier. Enable on each master page that hosts the calendar grid.
2. **Smart Text Reflow** — Preferences → Type. When enabled, InDesign auto-adds or removes pages to match content overflow/emptiness in the primary thread.

When both are on, the thread persists across master page changes and content re-imports. *In theory.*

## Why this might not actually solve it
- **XML Place vs Place Text File** behave differently. XML Place loads tagged content into named frames, which may break the primary thread.
- Calendar layouts use **multi-frame structured imports** (Date frames, Holiday frames, MiniCal frames per cell), not a single story — Primary Text Frame is designed for single-story flow.
- The cells may be intentionally unlinked because each cell is its own structured frame, and threading them defeats the per-cell tag mapping.

## What to actually do
1. Verify on a *copy* of one wall template — never the production one.
2. Possibly consult the InDesign Scripting forum or Mike Rankin's InDesignSecrets articles on XML + threading.
3. If structured XML Place fundamentally can't preserve threading, the workaround is a script (ExtendScript/UXP) that re-threads cells in document order after each XML Place.

## How this affects ChroniCal architecture
If we ever build a "Re-import XML" button in ChroniCal, it should probably also trigger a thread-fix script. That's a UXP plugin scope item, not a Python pipeline scope item.

## Established
2026-05-15 wrap of long ChroniCal session. Jefe flagged the pain after the per-product builder refactor shipped. Open until verified.
