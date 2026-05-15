# ChroniCal — Corpus & Regression Test Material

**Status:** Cataloged 2026-05-15
**Sibling memo:** [project_chronical.md](project_chronical.md)

## What's here
Eight years of production Chronicle Books calendars sitting on disk —
the dev gold mine for testing ChroniCal's pipeline.

**Location:** `~/sync/Buttercup/Chronicle/Calendars/`
(per-machine symlink; resolves to `/Volumes/Stilgar/...` on shai-hulud,
`/Volumes/Ghola/...` on alia, so the dev path is stable.)

**Scale:** ~280GB, 409 indexable files
- **185 loose .xml** files (calendar inputs, intermediates, masters, templates)
- **143 .idml** packages (Adobe interchange format, zip-of-xml, parseable)
- 362 .indd files exist but are redundant — IDML is the open equivalent

## Schema versions

The InDesign import-XML schema evolved. ChroniCal currently emits v2.

**v1 (2020–2021)** — `<Root xmlns:aid="..." aid:type="4.0">` with content
nested under `<year2020>`, `<year2021>` blocks. Older Anne Taintor / Bad
Girls / Year of the Dogs imports use this. Parseable but ChroniCal does not
emit this shape today.

**v2 (2022–2028)** — flat `<Root id="Calendar">`, paragraph-styled cell
elements directly under root: `PreviousYear_Year`, `PreviousYear_Month`,
`PreviousYear_Ghost`, `PreviousYear_Date date="M/D/YY"`, plus the
current-year date/moon/holiday elements. This is what ChroniCal emits.
**70 cal_xml v2 files in the corpus.**

## Catalog script

`scripts/catalog_corpus.py` walks the corpus, classifies every .xml and
.idml, writes a manifest.

Run from happenstance repo:
```
python3 scripts/catalog_corpus.py
```
Outputs:
- `scripts/output/corpus_manifest.csv`
- `scripts/output/corpus_manifest.json`

Per-file columns: `year, title, format (Wall|Daily|Engagement|Planner|
Family|Unknown), kind (xml|idml), role (input|intermediate|output|template|
master), is_calendar_xml, path, rel_path, size`.

Current totals (2026-05-15 scan):
| year | xml | idml | v2 cal_xml |
|------|----:|-----:|----------:|
| 2019 |   0 |   16 |  0 |
| 2020 |   3 |    0 |  0 |
| 2021 |  87 |   56 |  0 (v1 schema) |
| 2022 |  62 |   16 | 20 |
| 2023 |  23 |   13 | 15 |
| 2024 |  16 |   15 |  9 |
| 2025 |  30 |   12 | 11 |
| 2026 |   8 |   12 |  4 |
| 2027 |  11 |   18 |  8 |
| 2028 |   2 |    2 |  2 |

## Use cases

**Regression test bed.** Feed each input XML through ChroniCal's
plannerBuilder, diff against the stored output. Catches schema drift
before it ships.

**Format coverage.** Wall is what ChroniCal does today. Daily, Engagement,
Planner, Family Wall are all represented — when we wire those paradigms,
we have real examples to mirror.

**IDML fallback.** When loose XML isn't on disk, IDML preserves the imported
XML structure in `designmap.xml` + tagged Story elements. Reconstructable
via Python `zipfile` + `lxml`. Not built yet — write it when we need an
older year that lacks loose XML.

## Mess to navigate

- Inconsistent folder naming across years
  (`2022 Wall Cal - Anne Taintor` vs `2022_WallCal_AnneT-INT_MS to DES.xml`)
- Paired variants: `_FLOW`, `_ex`, `_FINAL`, `_output01` — needs disambiguation
- Older years have `zDeprecated/` and `MasterXML2021/` precursors
- 2022 has a `missingHolidays.txt` — known data gaps logged in-tree
- v1 → v2 schema break means a regression suite needs version-aware fixtures
- "None" year bucket in the manifest catches templates and the top-level
  `IndesignTemplates/`, `FSC LOGOS/`, `examples/` dirs — usually fine to skip

## What's not built yet

- v1 schema parser / converter (mostly only useful if we want to backfill
  2020–2021 regression coverage; deferable)
- IDML → input-XML extractor
- Regression harness that consumes the manifest, runs the pipeline, diffs
  against stored outputs
