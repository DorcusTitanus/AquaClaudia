# ChroniCal — Product Line Paradigms

**Status:** Visual model verified 2026-05-15 against 2027 Totoro Wall, 2027
Totoro 12-Month Weekly Planner, 2027 Disney Daily, 2027 WCS Daily.
**Sibling memos:** [project_chronical.md](project_chronical.md),
[project_chronical_corpus.md](project_chronical_corpus.md)

## Context: ChroniCal in Happenstance

Happenstance (Jefe's 2009 brand) does typesetting pre-production for
publisher clients. The longstanding solution is **per-client perl + xsl**
pipelines that emit InDesign-importable XML — one stack per publisher.

**ChroniCal is the first PoC** of a different approach: a native macOS GUI
(SwiftUI + Python backend) that wraps a single configurable pipeline,
drag-and-drop input, point-and-click toggles, syntax-highlighted XML editor.
**Chronicle Books is the first client.** After ChroniCal proves the model,
the plan is to port the remaining 5–6 publisher comps to the same GUI
pattern, each with its own configuration. Hand-coding XSL per job goes away.

## The four Chronicle product lines

All four use the same `<Root id="Calendar">` schema (v2 — see corpus memo
for v1 vs v2). The element vocabulary differs per line.

### 1. Wall

**Visual:** Single page per month. 7-col grid (Sun–Sat). Holidays render as
small text under the date numeral. Moon phases as glyphs to the right of
the date. Two minical thumbnails (prev + next month) at the bottom.
**Status in ChroniCal:** shipped v0.3.

**Per-month element order** (from `plannerBuilderv002.py`):

```
Month                                  # header, e.g. "January 2027"
MiniCalMonth (prev month label)        # "December 2026"
MiniCalWeekdays                        # "\ts\tm\tt\tw\tt\tf\ts"
MiniCalDate                            # contains tab-delimited days with inline <MiniCalGhost> runs
MiniCalMonth (next month label)        # "February 2027"
MiniCalWeekdays
MiniCalDate

# Leading ghost dates (filling cells before day 1 of month)
GhostDate "27" date="12/27/26"
  SpacerMoon "M"                       # placeholder for moon slot
  SpacerHoliday "H"                    # placeholder for holiday slot
GhostDate "28" date="12/28/26"
  SpacerMoon "M"
  SpacerHoliday "H"
...

# Real dates — each Date is followed by Moon-slot then Holiday-slot:
Date "1" date="1/1/27"
  MoonPhase | SpacerMoon              # MoonPhase if there is one this date, else SpacerMoon
  Holiday | SpacerHoliday             # Holiday(s) if any, else SpacerHoliday
Date "2" date="1/2/27"
  SpacerMoon
  Holiday "World Braille Day" date="1/2/27"
...

# Trailing ghosts to complete 35- or 42-cell grid
GhostDate "1" date="2/1/27"
  SpacerMoon
  SpacerHoliday
...
```

**Holiday continuation:** when multiple holidays land on the same date, the
second+ get `aid:pstyle="HolidayCont"` instead of `Holiday`. The
`--holiday-cont-after-moon` flag promotes the first holiday to `HolidayCont`
when a MoonPhase precedes it in the same cell.

**Grid fill:** `--six-week-grids` fills every month to 35 or 42 cells
(5 or 6 complete weeks). Without it, only the last partial row is filled,
which is the planner default.

**Bookend:** the script emits Jan of the *following* year as a bookend
month after December.

### 2. Planner

**Visual:** Three interleaving section types across ~174 pages:
- **Monthly spread** — facing pages split: left page has Sun–Wed columns,
  right page has Thu–Sat columns + a sidebar with both minicals stacked
  + a NOTES lined area.
- **Weekly spread** — one per week. Top half: prev/next minical thumb in
  corner, big "FRIDAY 15" header with moon-phase note, ruled lines.
  Bottom half: split into Saturday | Sunday with their own ruled zones.
- **Themed photo pages, holiday lists, blank notes pages** interleaved.

**Status in ChroniCal:** the underlying generator was originally written
for this paradigm (`plannerBuilderv002.py` — name reveals the origin).
GUI support: surfaced in product selector but not fully wired.

**Per-month element order:** same as Wall through the trailing ghosts,
then for each week within the month:

```
MonthWeekly "January" (or "December–January" for straddle weeks)
MiniCalMonth (current month) MiniCalWeekdays MiniCalDate

# 7 days of the week:
DateWeekly "31" date="12/31/26"        # ghost-styled if outside month
  SpacerMoon | MoonPhase
  SpacerHoliday | Holiday
DateWeekly "1" date="1/1/27"
  MoonPhase | SpacerMoon
  Holiday "New Year's Day" | SpacerHoliday
... (5 more)
```

**Straddle weeks** (a week split between two months) are attributed to the
later month in the doc order — they appear after that month's monthly
spread. Exception: December keeps its Dec–Jan straddle because the next
Jan is a bookend month.

### 3. Daily

**Visual:** 313-page book, 1 page per day, weekends merged (Sat+Sun → one
page). Skeleton is consistent across titles; body is polymorphic:
- **Header region:** Month, Day-of-week abbrev (MON/TUE/...), big Date
  numeral, optional Holiday(s) below.
- **Body region:** any of —
  - Image + title + paragraph (Disney: full-bleed Pixar still + film title +
    blurb)
  - Image only as a placed PDF (WCS: each day's content is pre-typeset
    upstream, then `<image>` pulls it in)
  - Image + numbered list / annotated diagram + paragraph (WCS in some days)
  - All-text editorial (Liz Climo, presumably — not yet sampled)

**Status in ChroniCal:** not built. Daily is the next major paradigm to
wire after Wall stabilizes.

**Element vocabulary** (from the 2027 Disney Daily IDML `Tags.xml`):

```
Date          DateWeekend     # weekday vs merged weekend cell
Day                           # day-of-week abbrev: MON, TUE, ...
Month         MonthWeekend
Holiday       HolidayWeekend
HolidaySpacer
Graphic
image                         # placed image / PDF reference
ProdDirective                 # production notes, ignored at render time
Root
```

Plus client-defined body tags (H1, para, title, etc.) carried in via the
docx → XML conversion.

**Order per day-entry:** not yet read from a Daily input XML — inferred
from PDF + Tags.xml only. Confirm against a real Daily input XML before
implementing.

**Weekend merging math:** 261 weekday pages + 52 weekend pages = 313.
Cover/back pages bring the printed book to ~316.

### 4. Engagement and Family Wall

Not yet inspected. Filenames suggest:
- **Engagement** — week-per-spread, similar to the planner's weekly section
  but standalone (no monthly spreads).
- **Family Wall** — Wall with multiple columns per day (one per family
  member). Different grid math.

Sample these before coding their paradigms.

## Pipeline shape (current — Wall)

```
xlsx (holidays + moon phases)
  → xlsx_to_events.py → events.xml (flat list, one per date)
  → plannerBuilderv002.py (with toggles) → InDesign-ready XML
  → InDesign places XML into tagged frames → printed calendar
```

## Pipeline shape (Daily — target)

```
client docx (Word Styles map to body tags) + xlsx (holidays + moons)
  → docx_to_xml + xlsx_to_events → events.xml + body.xml
  → dailyBuilder (TBD) → InDesign-ready XML with header skeleton + body
  → InDesign places XML
```

The docx → XML step is the part that turns ChroniCal into drag-and-drop
for Daily titles. Clients write content in Word with the assigned Styles;
the conversion script lifts that into the tagged body region.

## Open questions for coding

- Read a real Daily input XML (not the IDML's internal Stories) and verify
  the per-day element order matches the Tags.xml vocabulary.
- Confirm whether Daily ever carries `MoonPhase` (some titles may, others
  may not).
- Inspect an Engagement and a Family Wall to fill out their rows above.
- Decide whether Daily/Engagement/Family get their own builder scripts or
  flags on `plannerBuilderv002.py`. Lean toward separate builders — the
  element vocabularies diverge enough that flag-soup would be worse than
  three smaller scripts sharing a common helper module.
