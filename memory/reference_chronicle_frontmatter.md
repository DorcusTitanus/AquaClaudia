---
name: Chronicle calendar front-matter structure (per product)
description: What sits at the front of each Chronicle calendar product's interior (INT) file. The branded cover is ALWAYS a separate CVR file; Wall interiors open with a page-1 art image BEFORE the previous-year block. Empirical from ~7 years of production PDFs (2021–2028).
type: reference
---

Scanned the production archive at
`~/sync/Buttercup/Chronicle/Calendars` (2021–2028; PDFs are ground truth — the
archived XML is often comp-altered post-import, so trust the PDFs). Companion to
[[project_chronical_paradigms]].

## Universal rule

The **branded printed cover is always a separate `_CVR` file** (front-cover art +
title/branding). No product's **interior (`_INT`) file** contains that branded
cover. "We don't do the covers for them" is true *about the CVR* — but the
interior's page 1 is a different thing and varies by product.

## Per-product interior front matter

| Product | Interior page 1 | Prev-year (Sept–Dec) block | Image before prev-year content |
|---|---|---|---|
| **Wall** | full-page theme **ART** | yes — p2, inside front cover | **YES, standard 2021–2027** |
| **Family Wall** | prior-year **August** grid (17-mo school-year, Aug→Dec) | yes, as month grids p1–4 | **No** — starts on the grid |
| **Daily** | title / disclaimer / illustrated-title / or straight to Jan 1 | **none** | title-dependent, not standard |
| **Planner** | typographic **title page** | year-at-a-glance grids (not Sept–Dec mini) | no |
| **Engagement** | **title page** (sometimes illustrated) | only licensed-IP (Totoro) has year grids | no |

## Wall detail (the one that bit us)

Every finalized Wall INT opens: **p1 = full-page theme art → p2 = prior-year
Sept–Dec mini grid (inside front cover) → p3 = art → p4 = January grid**, then
art/grid per month. Confirmed across Grumpy Cat (2021, 2027), Stargazing,
Astrology, Catffirmations, Disney Villains, Classic Horror (2025), Museum Bums,
Disney Animation (2027). Archived Wall XML corroborates: a leading `<Graphic>`
sits before `PreviousYear_Year` (seen in 2022 Anne Taintor, 2025 Museum Bums,
2027 Anne Taintor; ~41 of 102 wall-path XML files — the rest are comp-altered).
Only counter-example: 2026 Disney Animation had the grid on p1, but that file was
a rough with "Caption TK"/"FPO" placeholders — not a finished layout.

**Implication for ChroniCal:** the leading `Graphic`+`Caption` before the first
`[Month]` in a Wall docx is legitimate **page-1 interior art** and must be emitted
BEFORE the PreviousYear block — it is NOT the branded cover, and dropping it is a
Wall bug. ChroniCal's `DocxBodyBuilder.preambleStart()` currently discards it,
violating [[feedback_content_is_king]]. Family Wall / Daily / Planner / Engagement
each need their own front-matter model when those builders are added.
