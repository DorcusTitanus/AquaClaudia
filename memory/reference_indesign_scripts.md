# Reference — InDesign / ExtendScript automation

**Status:** Collection started 2026-05-22 with BridgeToNextPage.jsx. Anticipated growth: Jefe noted these scripts save substantial comp time, so expect more as workflow pain points surface.

## Why this memo
ExtendScript (.jsx) tools live in Adobe InDesign's Scripts Panel and handle InDesign-side manipulation that would otherwise be click-heavy: relinking text chains, restoring master overrides, batch-applying paragraph styles, etc. They sit downstream of ChroniCal — ChroniCal produces the import XML; these scripts run after import to fix the things automated import can't. Capturing them here so future instances know what exists and can extend or fix as needed.

## Conventions
- **Location:** `~/Library/Preferences/Adobe InDesign/Version 21.0/en_US/Scripts/Scripts Panel/`
- **Run with:** Double-click in InDesign's Scripts panel (`Window → Utilities → Scripts`).
- **Wrapper:** Each script wraps its body in `app.doScript(fn, ScriptLanguage.JAVASCRIPT, undefined, UndoModes.ENTIRE_SCRIPT, "<name>")` so a single run is a single undo step.
- **Style:** Small, focused, one-job-each. Loud alerts on edge cases. Bail rather than guess. No CLI-style flags; behavior auto-detects from the document state when possible.

## Scripts

### BridgeToNextPage.jsx
**Path:** `~/Library/Preferences/Adobe InDesign/Version 21.0/en_US/Scripts/Scripts Panel/BridgeToNextPage.jsx`
**Purpose:** Thread the active page's text chain into the next document page's chain head. Saves the comp from manually linking after inserting or replacing a master page on a page that sits between two already-threaded pages.
**Three modes (auto-detected, alert reports which fired):**
- **APPEND** — active page's chain ends here AND story overflows → bridge to next page's head.
- **EXTEND** — chain ends here, story fits (no overflow yet) → same DOM op; text flows forward if it ever grows.
- **SPLICE** — active page's last frame threads to a page *beyond* the next page (Replace Master scenario where a new page now sits between two already-threaded pages). Saves the downstream frame, re-aims source at the new page's head, walks the new page's chain to its tail, re-attaches the saved downstream. Net: `N → new-N+1 chain → original-N+2`.
**Known issue:** Throws a modal error at the end of the run. **The script still works as intended** — text flows into the next page's chain without manual relinking. Specific error text not yet captured; Jefe to surface in a future session, then patch.
**Created:** 2026-05-22 (replaced an earlier overflow-only version that required the source frame's story to overflow — that filter blocked the SPLICE use case).

## Adding a new script
1. Save the .jsx file into `~/Library/Preferences/Adobe InDesign/Version 21.0/en_US/Scripts/Scripts Panel/`.
2. It appears immediately in InDesign's Scripts panel — no restart needed.
3. Follow the conventions above (doScript wrap, focused scope, loud alerts).
4. Add an entry to this memo with: path, purpose, modes/behavior, known issues, creation context.

## Version control
Scripts Panel folder is a macOS user-prefs dir — not currently tracked in git. If a script grows complex enough to need history, consider moving it into a tracked repo and symlinking from the prefs dir back to the tracked source.
