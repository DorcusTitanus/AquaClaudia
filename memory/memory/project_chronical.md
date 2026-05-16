# ChroniCal — Chronicle Books Calendar Pipeline App

**Status:** v0.5 shipped 2026-05-16. End-to-end docx → body XML pipeline for Wall and Daily, with Holiday cross-validation against xlsx and lint pass for InDesign-import hygiene. Comp-facing workflow now complete for Chronicle's two comp-handled paradigms.
**Repo:** `github.com/DorcusTitanus/happenstance` (private)
**Local:** `~/sync/Documents/ClaudeCode/happenstance/` (via per-machine symlink — see `feedback_sync_symlink.md`)

## What it is
macOS SwiftUI app that wraps a Python calendar pipeline for Chronicle Books. Comps drop an xlsx (holidays + moon phases) and an editor-supplied docx, pick a year, toggle design options, get InDesign-ready XML in a syntax-highlighted editor. Lint and validation surface comp/editor mistakes BEFORE InDesign sees them. **Self-contained .app** — scripts bundled inside, no filesystem dependencies on sync drives, repos, or specific machines.

## Stack
- **Frontend:** Swift + SwiftUI (macOS 14+, Xcode 26+)
- **Backend:** Python 3.14 (via `/opt/homebrew` on arm64) subprocess through `PythonRunner.swift`
- **Venv:** `~/Library/Application Support/ChroniCal/venv/` with **openpyxl + python-docx**
- **No sandbox** — needs filesystem access for user-dropped xlsx/docx and `~/Documents/ChroniCal/` writes

## Architecture

### Comp workflow (v0.5)
1. Comp drops the year's xlsx (holidays + moon phases — Jefe-maintained, authoritative)
2. Comp drops the editor's docx (per-title editorial content — Word styles already match InDesign element vocabulary)
3. Sets year, picks product (Wall / Planner / Daily), toggles design options
4. Clicks Build → pipeline runs:
   - `xlsx_to_events.py` → `/tmp/{year}_events.xml`
   - `docx_to_body.py --product wall|daily` (Wall + Daily only; Planner skips this) → `/tmp/{year}_body.xml` — extracts body, validates Holidays against xlsx, lints for hygiene
   - Per-product builder (`wallBuilder`/`plannerBuilder`/`dailyBuilder`) → `~/Documents/ChroniCal/Calendar_{year}_{product}.xml`
5. Either: ✓ output loaded into XML editor, OR ✗ lint/validation report loaded into editor with line numbers and rule names. Comp fixes docx or xlsx, re-runs.

### Scripts: bundled inside the .app
Canonical location: `gui/ChronCal/ChronCal/Scripts/`. Xcode 15+ PBXFileSystemSynchronizedRootGroup auto-bundles into `ChroniCal.app/Contents/Resources/*.py` — no manual project.pbxproj edits needed when adding new scripts.

- **`chronicalLib.py`** — shared helpers: AID namespace, `create_element`, `parse_events`, `build_minical`, `emit_date_events`, `build_previous_year`, `emit_month_block` (now accepts opt-in `month_section_children` + `day_body` kwargs for the docx body splice — planner unaffected since it passes None), output postprocessing.
- **`xlsx_to_events.py`** — extracts holidays + moon phases from xlsx to events XML.
- **`docx_to_body.py` (v0.5, new)** — universal Wall+Daily extractor. Reads docx via python-docx, skips template style-demo preamble (stops at first `Month` or `DateSlug`), groups paragraphs under DateSlug anchors. Emits one of two schemas:
  - **Wall:** `<Bodies><Cover>…</Cover><MonthSection name="…">…</MonthSection><Body date="M/D/YY">…</Body></Bodies>`
  - **Daily:** `<Bodies><Body date="M/D/YY">…</Body></Bodies>` (matches dailyBuilder's pre-existing `--body` consumer)
  Inline character styles preserved as nested elements with aid:cstyle. Bracketed filenames in Graphic paragraphs become `<image href="file:///Links/…"/>` children.
- **`wallBuilder.py`** — accepts `--body` (v0.5). Splices Cover at top of output (before PreviousYear), MonthSection right after each `Month` element, Body[date] children right after each date's Moon/Holiday/Spacer block.
- **`plannerBuilder.py`** — Jefe-owned, comp-untouched. Does not consume body XML by design — Jefe builds Planners himself.
- **`dailyBuilder.py`** — `--body` consumer was already wired in v0.4. v0.5 verified end-to-end against Disappointing Affirmations 2027 (313 Body blocks, weekend Sat+Sun merge correct).
- **`holidays_to_docx.py`** — generates blank Word doc templates (not yet wired into the UI). Unchanged.

### Holiday validator (v0.5)
**Rule:** xlsx wins for Holiday text. docx must match exactly. Any mismatch is a hard error, build halts.

Three failure modes caught:
1. Wording mismatch — e.g. `'Indigenous People'` (xlsx) vs `'Indigenous Peoples'` (docx) on 8/9/27. (Real production issue surfaced across three 2027 titles — the xlsx is wrong; the UN day is "Peoples" plural.)
2. xlsx has a Holiday, docx omits it — editor missed one.
3. docx adds a Holiday xlsx doesn't have — editor invented one.

**Weekend-aware:** docx Daily docs group weekend Sat+Sun under a single `'M/D/YY and M/D/YY'` DateSlug because the two days share the page's body region. xlsx is canonical per-day. The validator compares the docx weekend set against the **union** of xlsx Sat+Sun holidays for that pair — not per-day. This avoids spurious "docx only" mismatches on weekends.

### Lint pass (v0.5)
Surfaces docx hygiene issues that either break InDesign import or signal editor/comp style misuse. Each finding cites paragraph index (1-based, matches Word's line numbering) and a snippet so the comp can jump directly to it.

| Severity | Rule | Catches |
|---|---|---|
| Blocker | `bracket-not-graphic` | `[foo.jpg]` in a non-Graphic-styled paragraph (InDesign import will fail) |
| Blocker | `graphic-no-bracket` | Graphic paragraph with no bracketed filename |
| Warning | `bracket-maybe-art-ref` | Bracket text in non-Graphic style, single-token, no extension — could be art ref missing extension+style |
| Warning | `bracket-missing-extension` | Graphic paragraph with `[name]` lacking extension |
| Warning | `excessive-normal` | >5% of content paragraphs styled Normal and >10 absolute |

`classify_bracket()` heuristic distinguishes art references from decorative `[SECTION LABEL]` patterns:
- has `.` → filename (high confidence)
- ALL CAPS or contains spaces → decorative label (suppress)
- single mixed-case token → maybe art ref missing extension (warn)

The decorative-label suppression was added after Dumb Birds-style titles fired 157 false-positive blockers on legit `[BIRD PARTS]`-type section headers. Per Jefe (2026-05-16): Dumb Birds is poisoned reference data; do NOT program based on that title.

### Output: `~/Documents/ChroniCal/`
Filename pattern: `Calendar_{year}_{product}.xml`. Folder created lazily on first Build click. Reveal in Finder button targets this folder.

### Intermediates
`/tmp/{year}_events.xml` and `/tmp/{year}_body.xml`. Year-stamped to avoid same-session collisions. Survive until macOS tmp cleanup.

## Brand
Happenstance aesthetic (Jefe's 2009 brand). Mustard/saffron/cream/charcoal palette, Futura typography, adaptive light/dark mode. Icon: swirl motif with "ChroniCal" type.

## Body XML schemas

### Wall body (v0.5)
```xml
<Bodies xmlns:aid="http://ns.adobe.com/AdobeInDesign/4.0/">
  <Cover>
    <Caption aid:pstyle="Caption">…month-tagline…</Caption>
    <Graphic aid:pstyle="Graphic"><image href="…"/></Graphic>
  </Cover>
  <MonthSection name="January">
    <Graphic aid:pstyle="Graphic"><image href="file:///Links/foo.jpg"/></Graphic>
    <Caption aid:pstyle="Caption">Live fully.</Caption>
    <Caption aid:pstyle="Caption">Leap fearlessly.</Caption>
  </MonthSection>
  <Body date="1/1/27">
    <HolidaySpecial aid:pstyle="HolidaySpecial">…</HolidaySpecial>
  </Body>
</Bodies>
```

### Daily body (v0.4 schema, v0.5 emitter)
```xml
<Bodies xmlns:aid="http://ns.adobe.com/AdobeInDesign/4.0/">
  <Body date="1/1/27">
    <Graphic aid:pstyle="Graphic"><image href="file:///Links/154.jpg"/></Graphic>
    <Header1 aid:pstyle="Header1"><Italic aid:cstyle="Italic">Title</Italic>, 1937</Header1>
    <CaptionPara aid:pstyle="CaptionPara">…body text…</CaptionPara>
    <Copyright aid:pstyle="Copyright">© DISNEY</Copyright>
  </Body>
  <Body date="1/2/27 and 1/3/27">
    …weekend Sat+Sun merged content…
  </Body>
</Bodies>
```

## Editor docx convention (Chronicle Books)
Word styleIds map directly to InDesign element names. The same vocabulary covers Wall + Daily templates with Daily-specific additions for body editorial content.

**Shared structural styles:** Month, Date, DateSlug, Weekday, Weekend, Holiday, HolidaySpecial, Graphic, CaptionPara, Header1, Wildcard1, Wildcard2

**Daily-extra:** Heading1, RunningText, IndentText, ListBulleted, ListNumbered, Copyright, CreditProperty, ProdDirective, TableBody, TableHeader

**Inline character styles:** Bold, Italic, BoldItalic, CAPS, Caps0, SmallCaps, Strong, Subscript, Superscript, Highlight, Mono, MonoBold, MonoBoldItalic, MonoItalic

**DateSlug as anchor:** Editorial content under a DateSlug paragraph belongs to that date. Weekend pairs use `'M/D/YY and M/D/YY'` form. Skeleton paragraphs (Weekday/Weekend/Month/Date) ARE present in the docx but get programmatically re-emitted by the per-product builder — `docx_to_body` discards them.

## Repo history
- 2026-05-14: v0.3 shipped — sidebar, XML editor, design toggles. Extracted from `chronicleBooks/calendar-builder/`.
- 2026-05-15: v0.4 — major arch change. Bundled scripts, removed `repoRoot`, moved output to `~/Documents/ChroniCal/`, split into per-product builders, added Daily skeleton.
- **2026-05-16: v0.5** — docx → body XML pipeline for Wall and Daily. Holiday validator. Lint pass. ContentView auto-runs `docx_to_body` when a docx is dropped + product is Wall/Daily. Stale `v0.3` UI label → `v0.4` (drift fix); the project itself is at v0.5.

## Verification (v0.5)
Tested end-to-end in the actual .app against:
- **Catffirmations 2027 Wall** (Wall product) — lint catches cover image filename styled Normal; validator catches `Indigenous People`/`Peoples` mismatch. Both real findings.
- **Disappointing Affirmations 2027 Daily** (Daily product) — clean except for the same `Indigenous People` issue. 313 Body blocks, weekend Sat+Sun merge confirmed.
- Year-mismatch failure mode confirmed: when xlsx-year and dropped-year disagree, validator returns ~94 spurious mismatches. Pre-check planned (one clear "Year mismatch: docx is X, build year is Y" message).

## Known TODOs / open items
- **Year-mismatch pre-check** — collapse the wall of per-date mismatches to one clear message when docx year ≠ build year. 10-line script addition.
- **Error styling** — render stderr report in saffron in the XML editor on script failure; distinguishes the report from the calendar XML output.
- **`prevYearGraphic` toggle** — UI exists since v0.3, flag still not consumed by Python. Wall-only.
- **Input editor pane (Cycle 4)** — parsed-docx review pane on left, lint findings highlighted inline, mirrors the XML output editor on the right. Editor pane would let comps fix lint blockers without round-tripping through Word. Significant Swift work (~2-4 hr). v1.1+ candidate.
- **Engagement + Family Wall paradigms** — not yet inspected; sample IDMLs first per the paradigms memo.
- **Regression harness** — walk `corpus_manifest.csv`, run builders, diff against stored outputs. Catches schema drift.
- **Python deps bundling** — venv is per-machine. For ship-to-Happenstance, either bundle Python (py2app/PyInstaller) or pivot to native Swift parser.
- **Legacy `scripts/` directory at repo root** — stale post-v0.4 (everything canonical lives in `gui/ChronCal/ChronCal/Scripts/`). Decide delete vs rename vs leave.
- **Dumb Birds is poisoned reference data** (Jefe 2026-05-16) — workflow didn't work, taken back in-house. Do NOT use it to validate or design pipeline behavior.

## Key Swift files
- `gui/ChronCal/ChronCal/ContentView.swift` — sidebar + editor + pipeline wiring. v0.5 adds `runDocxToBodyIfNeeded()` invoked from `runPlannerBuilder()` before the per-product builder.
- `gui/ChronCal/ChronCal/XMLEditorView.swift` — NSTextView syntax highlighting
- `gui/ChronCal/ChronCal/Theme.swift` — Hap palette, fonts, button style
- `gui/ChronCal/ChronCal/PythonRunner.swift` — async Process wrapper; venv at `~/Library/Application Support/ChroniCal/venv/bin/python3`

## Mounted paths Jefe uses for ChroniCal context
- `~/sync/Documents/ClaudeCode/happenstance/` — repo
- `~/sync/Documents/ClaudeCode/calendars/{year}/` — Jefe's per-year working files (xlsx etc.)
- `/Volumes/tabr/HappenstanceWork/Chronicle/calendars/{year}/` — canonical NAS archive of past years (production docs, IDMLs, INTs)
- `~/Documents/ChroniCal/` — ChroniCal's own output folder
