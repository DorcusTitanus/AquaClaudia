# ChroniCal — Chronicle Books Calendar Pipeline App

**Status:** v1.1 shipped 2026-05-16 (same day as v1.0). Adds in-app Help menu with FAQ window, Holiday-mismatch reclassification (advisories vs lint vs data-error), HolidaySpecial suggestion for docx-only Holiday text, Daily "Allow art checks to pass" + "Weekday full names" toggles, lenient DateSlug parsing for comp-annotated WCS-style docx ("New:1/1/27", "1/4/27m"), text-zoom redraw fix, Save-As-aware Reveal Output. Comp-facing workflow complete for Wall AND Daily; WCS 2027 verified structurally identical to production-pre-hand-curation reference.
**Repo:** `github.com/DorcusTitanus/happenstance` (private)
**Local:** `~/sync/Documents/ClaudeCode/happenstance/` (via per-machine symlink — see `feedback_sync_symlink.md`)

## What it is
macOS SwiftUI app that wraps a Python calendar pipeline for Chronicle Books. Comps drop an xlsx (holidays + moon phases) and an editor-supplied docx, pick a year, toggle design options, get InDesign-ready XML. Lint and validation surface comp/editor mistakes BEFORE InDesign sees them, and an in-app lint review pane lets the comp re-tag flagged paragraphs without round-tripping through Word. **Self-contained .app** — scripts bundled inside, no filesystem dependencies on sync drives, repos, or specific machines.

## Stack
- **Frontend:** Swift + SwiftUI (macOS 14+, Xcode 26+)
- **Backend:** Python 3.14 (arm64, `/opt/homebrew`) subprocess via `PythonRunner.swift`
- **Venv:** `~/Library/Application Support/ChroniCal/venv/` with **openpyxl + python-docx**
- **No sandbox** — needs filesystem access for user-dropped xlsx/docx and `~/Documents/ChroniCal/` writes

## Comp workflow (the killer loop)
1. Drop the year's xlsx (Jefe-maintained, authoritative)
2. Drop the editor's docx (per-title editorial content)
3. Set year, pick product (Wall / Planner / Daily), toggle design options
4. Click **Build {Product}**. Pipeline runs:
   - filename-year pre-check (cheapest gate; halts before any Python if year stepper disagrees with leading-4-digit-year in either filename)
   - `xlsx_to_events.py` → `/tmp/{year}_events.xml`
   - `docx_to_body.py` (Wall/Daily/Planner) → `/tmp/{year}_body.xml` + `/tmp/{year}_findings.json`
   - Per-product builder (`wallBuilder`/`plannerBuilder`/`dailyBuilder`)
5. The right pane swaps to whichever mode is most actionable:
   - **`.errorReport`** — saffron-tinted stderr (year mismatch, xlsx year mismatch, product mismatch, Holiday text mismatch). NOT fixable via style overrides; comp fixes stepper / xlsx / docx text.
   - **`.lintReview`** — actionable rows with style pickers pre-filled with suggested fixes. Bulk "Show all suggestions" populates every row; "Build with fixes (N)" stages overrides as `/tmp/{year}_overrides.json` and re-runs.
   - **`.xmlOutput`** — syntax-highlighted Calendar XML.
6. ⌘S to overwrite, ⇧⌘S for Save As. Output defaults to `~/Documents/ChroniCal/{docxStemNoWhitespace}_input.xml`.

## Right-pane viewport modes (single context-switching pane)
```
.empty           initial placeholder
.errorReport     saffron-tinted lines (data errors)
.lintReview      style-picker rows with bulk apply
.xmlOutput       syntax-highlighted calendar XML
```
Status bar above the pane stays consistent; the pane content swaps. Per Jefe (2026-05-16): "like running claude in terminal, everything is in the terminal."

## Pre-checks (priority order)
| Check | Where | Halts before |
|---|---|---|
| Filename year (4-digit leading year in xlsx/docx filename) | Swift | Python |
| Product mismatch (Daily docx with Wall selected, etc.) | Python (earliest gate after doc load) | Lint + extraction |
| Year mismatch — docx side | Python | per-date Holiday validator |
| Year mismatch — xlsx side (threshold 10 entries) | Python | per-date Holiday validator |
| Holiday text mismatch (xlsx is canonical, weekend-aware union) | Python | — |
| Docx lint (bracket-not-graphic, unstyled-content, graphic-no-bracket, …) | Python | per-product builder |

The first failing pre-check wins routing. Data errors → `.errorReport`. Lint findings → `.lintReview`. Clean → builder runs → `.xmlOutput`.

## Scripts (canonical = bundled inside `ChroniCal.app/Contents/Resources/`)

Located at `gui/ChronCal/ChronCal/Scripts/`. Xcode 15+ `PBXFileSystemSynchronizedRootGroup` auto-bundles them.

- **`chronicalLib.py`** — shared helpers: AID namespace, `create_element`, `parse_events`, `build_minical`, `emit_date_events`, `build_previous_year(skip_year_label=…)`, `emit_month_block(month_section_children=…, day_body=…)`, output postprocessing.
- **`xlsx_to_events.py`** — extracts holidays + moon phases from xlsx to events XML.
- **`docx_to_body.py`** (v0.5 → v1.0) — universal Wall + Daily + Planner extractor. Reads docx via python-docx. Skips template style-demo preamble (stops at first `Month` or DateSlug that parses as a real date — `CreditArt`-style demo DateSlugs are ignored). Groups paragraphs under DateSlug anchors. Emits two body XML schemas (Wall: `<Cover>` + `<MonthSection>` + `<Body date>`; Daily: `<Body date>` only, weekend pairs as `"M/D/YY and M/D/YY"`). Inline character styles preserved as nested elements with `aid:cstyle`. Bracketed filenames in `Graphic` paragraphs → `<image href="file:///Links/…"/>`. v1.0 adds: `--overrides` flag, `--findings-json` sidecar, structured `dataErrors` for year/xlsx-year/product mismatches, per-Normal-paragraph `unstyled-content` warning, `classify_bracket` heuristic (filename / maybe-filename / label).
- **`wallBuilder.py`** — accepts `--body` and `--prev-year-graphic`. Splices Cover before PreviousYear, MonthSection after each Month, Body[date] after each date's Moon/Holiday/Spacer.
- **`plannerBuilder.py`** — Jefe-owned, comp-untouched. Does not consume body XML by design.
- **`dailyBuilder.py`** — `--body` consumer; 313-page skeleton with weekday/weekend merge. Verified end-to-end against Disappointing Affirmations 2027.
- **`holidays_to_docx.py`** — blank Word template generator (not yet wired into the UI).

## Holiday validator (v0.5, weekend-aware)
**Rule:** xlsx wins for Holiday text. docx must match exactly. Any divergence is a hard error.

Three failure modes:
1. Wording mismatch — e.g. `'Indigenous People'` (xlsx) vs `'Indigenous Peoples'` (docx) on 8/9/27. (Real xlsx bug found across three 2027 titles — UN day is "Peoples" plural.)
2. xlsx has a Holiday, docx omits it.
3. docx adds a Holiday xlsx doesn't have.

**Weekend-aware:** Daily docx groups Sat+Sun under one `'M/D/YY and M/D/YY'` DateSlug because the page body is shared. Validator compares the docx weekend set against the UNION of xlsx Sat+Sun.

## Lint pass (v1.0)
Surfaces docx hygiene issues that either break InDesign import or signal editor/comp style misuse. Each finding cites paragraph index (1-based, matches Word's line numbering) and a snippet so the comp can jump straight to the row in Word — or fix in-app via the lint review pane.

| Severity | Rule | Catches |
|---|---|---|
| Blocker | `bracket-not-graphic` | `[foo.jpg]` in non-Graphic-styled paragraph (InDesign import fails) |
| Blocker | `graphic-no-bracket` | Graphic paragraph with no bracketed filename |
| Warning | `unstyled-content` | Any non-empty Normal-styled content paragraph (per Jefe: Normal is never right) |
| Warning | `bracket-maybe-art-ref` | Non-Graphic style, single-token bracket, no extension |
| Warning | `bracket-missing-extension` | Graphic with `[name]` lacking extension |

`classify_bracket()` heuristic separates art refs from decorative labels:
- has `.` → filename (high confidence)
- ALL CAPS or contains spaces → decorative label (suppress)
- single mixed-case token → maybe filename (warn)

## Lint review pane (v1.0 anchor feature)
`LintReviewPane.swift`. Renders when findings are present and there are no data errors blocking.

- Header bar: blocker/warning count badges, "Show all suggestions" + "Clear" bulk buttons, "N of M fixes staged" counter
- One row per flagged paragraph: severity icon + line number + rule kind + current style + snippet + style picker
- Picker pre-filled with the script's `suggested` style (Graphic for bracket-not-graphic; CaptionPara for unstyled-content; etc.). Picker selection of "— no fix —" clears that paragraph's override.
- Picker options come from a fixed `chronicleStyleOptions` list of 17 Chronicle paragraph styles (Graphic, CaptionPara, Caption, Holiday, HolidaySpecial, Header1, Header1cont, Heading1, RunningText, IndentText, ListBulleted, ListNumbered, Copyright, CreditProperty, ProdDirective, Wildcard1, Wildcard2). Future v1.1 candidate: pull this list from the docx's defined paragraph styles via python-docx so the picker only offers styles the comp's template actually carries.
- "Build with fixes (N)" footer button writes `/tmp/{year}_overrides.json` and re-runs the pipeline. Overrides are applied at extraction time so the output XML carries the corrected pstyle on each element.

Comp workflow on Catffirmations 2027 Wall: 1 blocker + 15 warnings → click "Show all suggestions" → 16 fixes staged with the right defaults (1 Graphic + 15 CaptionPara) → click "Build with fixes (16)" → clean XML output. Three clicks.

## Body XML schemas (consumer side)

### Wall body (v0.5+, consumed by wallBuilder.py)
```xml
<Bodies xmlns:aid="http://ns.adobe.com/AdobeInDesign/4.0/">
  <Cover>
    <Caption aid:pstyle="Caption">…tagline…</Caption>
    <Graphic aid:pstyle="Graphic"><image href="…"/></Graphic>
  </Cover>
  <MonthSection name="January">
    <Graphic aid:pstyle="Graphic"><image href="file:///Links/foo.jpg"/></Graphic>
    <Caption aid:pstyle="Caption">Live fully.</Caption>
  </MonthSection>
  <Body date="1/1/27"><HolidaySpecial>…</HolidaySpecial></Body>
</Bodies>
```

### Daily body (v0.4 schema, v0.5 emitter, v1.0 overrides)
```xml
<Bodies xmlns:aid="http://ns.adobe.com/AdobeInDesign/4.0/">
  <Body date="1/1/27">
    <Graphic aid:pstyle="Graphic"><image href="file:///Links/154.jpg"/></Graphic>
    <Header1 aid:pstyle="Header1"><Italic aid:cstyle="Italic">Title</Italic>, 1937</Header1>
    <CaptionPara aid:pstyle="CaptionPara">…</CaptionPara>
    <Copyright aid:pstyle="Copyright">© DISNEY</Copyright>
  </Body>
  <Body date="1/2/27 and 1/3/27">…weekend Sat+Sun merged…</Body>
</Bodies>
```

## Editor docx convention (Chronicle Books)
Word styleIds map directly to InDesign element names. Same template style set covers Wall + Daily; Daily adds body-editorial styles. **DateSlug = anchor.** Editorial content under a DateSlug belongs to that date. Weekend pairs use `'M/D/YY and M/D/YY'`. Skeleton paragraphs (Weekday/Weekend/Month/Date) are present in the docx but get programmatically re-emitted by the per-product builder.

## Output naming convention (v1.0)
Docx stem with all whitespace removed + `_input.xml`. Example: `2027 Catfirmations Wall MS.docx` → `2027CatfirmationsWallMS_input.xml`. Used as the build output path AND the Save / Save As default. Fallback when no docx is dropped (Planner; empty slate): `Calendar_{year}_{product}.xml`.

## Text size (v1.0)
- Universal **+2pt baseline** bump (HapFont.baseBoost) so default text is more readable.
- **⌘+ / ⌘− / ⌘0** zoom commands in the View menu adjust `HapFont.scale` (0.7×–1.6×, 0.1 steps). Persisted via `@AppStorage("textScale")`. Cascades to every HapFont call site + the XML editor's monospace font.

## File menu commands (v1.0)
- **File → Save** (⌘S) — overwrites the canonical output path; disabled when no XML is in the viewport
- **File → Save As…** (⇧⌘S) — NSSavePanel defaulted to the output path, allowedContentTypes=[.xml]
- **View → Make Text Bigger / Smaller / Actual Size** (⌘+/−/0)

Wired via `@FocusedValue(\.saveActions)` published by ContentView and consumed by a `SaveCommands` struct in ChronCalApp.

## Repo history
- 2026-05-14: v0.3 shipped — sidebar, XML editor, design toggles
- 2026-05-15: v0.4 — major arch change; bundled scripts, removed `repoRoot`, moved output to `~/Documents/ChroniCal/`, split into per-product builders, added Daily skeleton
- 2026-05-16: v0.5 — docx → body XML pipeline for Wall and Daily; Holiday validator; lint pass; ContentView auto-runs docx_to_body
- 2026-05-16: v1.0 — Cycle 4 lint review pane; xlsx-year-mismatch detection; product-mismatch detection; filename-year fast-fail; per-Normal warning; classify_bracket heuristic; cover-preamble fix; auto-rerun xlsx_to_events on Build; error-report styling; Save / Save As; ⌘+/− text zoom; docx-derived output naming
- **2026-05-16: v1.1 (same day, third push)** — Cycle 5 in-app Help menu + FAQ window (⌘?); text-zoom dependency-tracking fix; Reveal Output uses lastSavedURL; Holiday-mismatch reclassification (advisories don't halt build, docx-only-with-text becomes HolidaySpecial suggestion); Daily Options restructured (Allow art checks to pass, Weekday full names); bracket-missing-extension warning removed; lenient DateSlug + skip_preamble parsing (handles WCS-style "New:1/1/27" / "1/4/27m"); WCS 2027 Daily verified structurally identical to production pre-hand-curation.

## Hard rules captured this session
- **xlsx is canonical for Holiday text.** Build halts on **wording** mismatch. xlsx-only entries are advisories that don't halt (xlsx wins; holiday emits anyway).
- **docx-only Holiday text** (xlsx doesn't have it) → editor probably meant **HolidaySpecial**. Surface as a lint warning with paragraph index + suggested style. v1.1 makes "Walt Disney born December 5, 1901"-style entries actually land in the output.
- **Normal is never a valid Chronicle paragraph style.** Per-paragraph warning fires for every non-empty Normal in the post-preamble content area.
- **`aid:pstyle` is the editor↔comp seam.** Editor-side style names stay uniform across all titles; per-title InDesign customization (Weekday-Wend, Month-Wend etc.) happens via comp-side remap post-import (automated or s/r). ChroniCal extracts editor-consistent names — do NOT add per-title pstyle toggles.
- **DateSlug parsing must be lenient.** WCS-style docx uses comp annotations like "New:1/1/27" or "1/4/27m". Strict regex drops the first day's content. Same lenient extraction applies in `skip_preamble`.
- **Dumb Birds is poisoned reference data** (Jefe 2026-05-16). Chronicle took the workflow back in-house. Do NOT use it to design pipeline behavior.
- **Don't pre-stage with `git add` when a single `git commit -o <files>` would do** — except for untracked files, which `git commit -o` does NOT auto-stage despite the docs. Pre-stage with `git add <file>` only when explicitly named.
- **@AppStorage subscribes on READ, not declaration.** SwiftUI views must access the property in body (e.g. `HapFont.scale = textScale`) for the subscription to register. Just declaring `@AppStorage("foo")` won't trigger re-render when the value changes.

## Verification (v1.0)
End-to-end in the actual .app against:
- **Catffirmations 2027 Wall** — Build (year 2027, Wall, both files dropped) → lint review with 1 blocker + 15 warnings → Show all suggestions populates 1 Graphic + 15 CaptionPara → Build with fixes → 105,645-byte clean XML written to `2027CatfirmationsWallMS_input.xml`. Save As pre-fills the same name.
- **Disappointing Affirmations 2027 Daily** — clean except the same `Indigenous People`/`Peoples` xlsx issue surfacing as data error.
- **Year mismatch matrix** — confirmed: 2027 docx + 2028 xlsx + year 2027 fires xlsx-year-mismatch; year 2028 fires docx year-mismatch; filename-year pre-check catches both before Python runs.
- **Product mismatch matrix** — Wall+Daily docx, Daily+Wall docx, Planner+anything all surface with title-aware fix suggestions.

## Open TODOs / v1.1 candidates
- **Pull picker style list from the docx** (currently hardcoded `chronicleStyleOptions`). Read paragraph-styles from the docx's `styles.xml` via python-docx, ship in findings JSON as `availableStyles`, Swift uses that for the picker. Lets templates drop or rename styles without code changes.
- **Suggestion priority lists** — `SUGGESTED_BY_KIND` becomes a priority list per kind so suggestions fall through against the docx's actual style set.
- **Engagement + Family Wall paradigms** — not yet inspected; sample IDMLs first.
- **Regression harness** — walk `corpus_manifest.csv`, run builders, diff against stored outputs.
- **Python deps bundling** — venv is per-machine. For ship-to-Happenstance, bundle Python (py2app/PyInstaller) or pivot to native Swift parser.
- **Legacy `scripts/` directory at repo root** — stale post-v0.4. Decide delete vs leave.
- **Inline run-level text edits in lint review** — currently overrides only swap paragraph style; text edits aren't supported in the pane. Comps fix typos in Word.

## Key Swift files
- `gui/ChronCal/ChronCal/ContentView.swift` — sidebar + drop zones + status bar + viewport mode dispatch + build chain (filename pre-check → xlsx_to_events → docx_to_body w/ overrides → wallBuilder/plannerBuilder/dailyBuilder)
- `gui/ChronCal/ChronCal/LintReviewPane.swift` — flagged-only list with style pickers, bulk apply, Build with fixes
- `gui/ChronCal/ChronCal/XMLEditorView.swift` — NSTextView with XML syntax highlighting and error-report mode (`isErrorReport: Bool`)
- `gui/ChronCal/ChronCal/Theme.swift` — Hap palette, HapFont (scale + baseBoost), HapButtonStyle
- `gui/ChronCal/ChronCal/ChronCalApp.swift` — App entry, SaveCommands, ⌘+/−/0 zoom commands
- `gui/ChronCal/ChronCal/PythonRunner.swift` — async Process wrapper; venv at `~/Library/Application Support/ChroniCal/venv/bin/python3`

## Distribution (path locked 2026-05-16)
**Apple Developer Program enrollment confirmed 2026-05-16.** ChroniCal ships as a
**Developer ID-signed and notarized .dmg**, not via the Mac App Store.

Why this path:
- Comps at Chronicle Books (and the other 5–6 publisher clients Happenstance will
  port off perl/xsl) install by double-clicking a .dmg. No App Store account, no
  IT review, no sandbox restrictions on filesystem access (ChroniCal needs broad
  access for user-dropped xlsx/docx and `~/Documents/ChroniCal/` writes).
- Notarization passes Apple's automated malware scan → no Gatekeeper warning on
  first launch.
- TestFlight available for beta distribution to comps without sending raw .app
  bundles around.

What this unblocks:
- Signed builds the comps can run without "unidentified developer" friction.
- Per-client distribution is just rebranded DMGs of the same signed binary.
- iCloud / push entitlements available later if the product needs them.

Status: dev account live, signing identity not yet pulled into the Xcode project.
Cut the first signed/notarized build when v1.x stabilizes for external comps.

## Mounted paths Jefe uses for ChroniCal context
- `~/sync/Documents/ClaudeCode/happenstance/` — repo
- `~/sync/Documents/ClaudeCode/calendars/{year}/` — Jefe's per-year working files (xlsx etc.)
- `/Volumes/tabr/HappenstanceWork/Chronicle/calendars/{year}/` — canonical NAS archive of past years (production docs, IDMLs, INTs)
- `~/Documents/ChroniCal/` — ChroniCal's own output folder
