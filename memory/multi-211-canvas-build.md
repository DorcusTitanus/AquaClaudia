# MULTI 211 Canvas Build — Work Log

Course: Adobe Photoshop II at Crafton Hills College. 13 weeks, fully online,
gamified Level structure over Horton's Absorb → Do → Connect pedagogy.

Last updated: 2026-04-30 — Level 11 shipped as a draft. Course content build is complete.

## Status snapshot

**11 of 11 levels built.** All draft, ready for Jeff to publish at Crafton. Walkthrough video recordings planned for summer 2026.

| # | Level                    | Topic                                                          | Status |
|---|--------------------------|----------------------------------------------------------------|--------|
| 1 | Mask Architect           | Non-destructive masking, layer masks, blending modes           | Built  |
| 2 | Experimental Designer    | Glass sphere compositing — layer styles, Spherize, refraction  | Built  |
| 3 | Visual Storyteller       | Elements of art / design principles / Sprite Fright key art    | Built  |
| 4 | Precision Retoucher      | Frequency separation + recording an Action                     | Built  |
| 5 | Motion Beginner          | Frame animation, GIF loops, Sprite Fright                      | Built  |
| 6 | Motion Designer          | Video timeline, keyframing, Render Video, Sprite Fright        | Built  |
| 7 | Generative Designer      | Seven ML tools — Select Subject, Remove, CAF, Gen Fill, etc.   | Built  |
| 8 | Digital Painter          | Mixer Brush, custom brushes, painting over a photograph        | Built  |
| 9 | Automation Engineer      | Variables + Actions + Batch — generate a 30-card trading set   | Built  |
| 10| Systems Designer         | Layer comps + attention economy + YouTube thumbnail design     | Built  |
| 11| Advanced Practitioner    | Capstone — three-product cohesive campaign with data-driven set| Built  |

## Per-level structure (what every level looks like)

Seven module items, in order. Total per level: 40 pts.

1. **Overview** (Page) — `must_view`. LLOs, time estimate, badge, XP.
2. **Tutorial** (Assignment) — `must_submit`, 5 pts. Step-by-step walkthrough; submits a PSD.
3. **Checkpoint Quiz** — `min_score 4.0`, 5 pts. Five questions, unlimited attempts, keep highest, shuffled.
4. **Project** (Assignment) — `must_submit`, 25 pts. Most levels enable peer review (count = 2) with a level-specific 5×5 binary rubric. A few use a Discussion-style "Screening Room" instead.
5. **Journal** (Assignment) — `must_submit`, 5 pts. Private reflection. Reusable Weekly Journal rubric.
6. **Level N+1 Unlocked** (Page) — no completion requirement.
7. **Level N: Badge Collection** (Page) — no completion requirement. Shows the cumulative XP bar and the full badge grid (earned in color, locked greyed out).

## Hard-won decisions worth remembering

### Journal gating — context, not time

Journals are *context-sensitive* (tied to the project the student just finished), not *time-sensitive*. So journals are gated by `must_submit`, not by a 2-week availability window. A student who reaches Level 4 in Week 8 should still be able to reflect on Level 4. Discussions and peer reviews keep their windows because they need the cohort present.

This was a midstream change — the original course design used 2-week windows, which blocked a behind-schedule student from submitting backlogged journals. All 9 already-built levels were back-patched to the new gating rule on 2026-04-22.

### IMSCC build — Canvas exports its own cartridges cleanly

The first IMSCC built without bugs was Level 10. The recipe: build the level live in Canvas, then call the Canvas content_exports API with `select[modules][]` set to just the level's module ID. Canvas generates the cartridge and every previously-error-prone detail (manifest entries, `unpublished` states, rubric encoding, peer-review wiring) comes out correct.

Hand-rolled Python builders (`scripts/canvas/build_levelN_imscc.py`) for Levels 3–9 are deprecated.

### IMSCC must import unpublished

Every wiki page meta tag and every module item in `course_settings/module_meta.xml` must read `workflow_state="unpublished"`. Cartridges that auto-publish on import are a hard violation — students see drafts.

### Layer comps gotcha (Level 10)

Layer comps capture *visibility*, *position*, and *layer style appearance*. They do NOT capture adjustment layer values. So if you want each comp to show a different color, use a Color Overlay layer style on the layer (works), not a Solid Color adjustment layer clipped to the layer (does not work — all comps share whichever color was set last). This is the central conceptual lesson in the Level 10 tutorial.

### PSD optimization recipe

When a starter PSD ends up oversized (the Level 10 file was 130 MB, the Level 8 file was 128 MB):

1. **Maximize Compatibility OFF** in Settings → File Handling. Saves 30–50% with zero quality loss.
2. **Delete leftover Generated Image layers** from any Generative Fill experiments.
3. If still too large: downsize Smart Object source rasters. Open the SO, Image Size to ~2× canvas size, save.

Level 10 went from 123 MB to 14 MB with steps 1 and 2 alone.

## Pedagogical through-line

Each level pairs a technical skill with a *critical context* aside:

- Level 5 includes the **GIF Economy** — who owns GIPHY/Tenor, how attention search ad markets work.
- Level 7 includes the **ML Map** — which Photoshop tools are actually machine learning vs traditional algorithms (Content-Aware Fill is NOT ML).
- Level 9 includes a hands-on use case discussion to surface real workplaces where automation matters.
- Level 10 includes the **Attention Economy** — CTR as the single number that drives every YouTube thumbnail design choice; clickbait conventions named so students can use, resist, or invent on purpose.

The pattern: technical skill + critical context, neither subordinate to the other. This is Jeff's brand alignment with the polymathery audience.

## Writing rules (student-facing copy)

- 8th-grade reading level. Short sentences. No jargon.
- Calm, clear, supportive, confident, human. Not apologetic or over-explaining.
- No emojis in body copy. Section heading emojis are fine and follow the established level template pattern.
- No meta commentary, no breaking the game narrative, no instructor-facing asides in student-facing content.
- Journals explicitly prohibit AI generation, framed as purpose-driven.

## Level 11 — Capstone (the exception to the standard structure)

Level 11 has a custom 7-item module shape because it has no tutorial assignment and no checkpoint quiz. The Project Brief page IS the Absorb. Two-week timeline, May 4 to May 14.

| # | Item | Type | Completion | Pts |
|---|---|---|---|---|
| 1 | Overview | Page | must_view | — |
| 2 | Project Brief | Page (serves as Absorb) | must_view | — |
| 3 | Pitch + Comps: Your Capstone Campaign | Discussion (graded) | must_contribute | 10 |
| 4 | Project: Three-Product Campaign | Assignment + peer review (count = 2) | must_submit | 25 |
| 5 | Journal: Course Reflection | Assignment | must_submit | 5 |
| 6 | Course Complete! | Page | — | — |
| 7 | Level 11: Badge Collection | Page (all 11 earned) | — | — |

Total 40 pts (10 + 25 + 5). Discussion takes the slot Tutorial+Checkpoint usually fill.

**Three submission deadlines + two peer feedback deadlines = five deadlines:**
- Tue May 5 — COMPS (pitch + layer comp options)
- Thu May 7 — Discussion replies
- Sun May 10 — ROUGHS (3 master files + 12 data-driven exports)
- Tue May 12 — Peer reviews
- Thu May 14 — FINALS (revised after peer feedback)

The Project Brief page contains a curated Absorb of two real-world Behance campaigns (Lollapalooza Brazil 2026 by LUKTHIS®; The Legend of Ochi—A24 by Clément Brichon, Watson Design Group, Christian Korpi) presented as three lessons: system across formats, identity across motion, and one template producing many variants.

## Plans for summer 2026

- Walkthrough video recordings for every level's tutorial.
- Eventually re-export master cartridges from the live Canvas course (use the Canvas-native recipe in `project_imscc_builds.md`, never re-touch the old Python builders).

## Where things live (sanitized — no IDs)

- Course design rules: `CLAUDE.md` at project root
- Gold-standard level template: `LEVEL-TEMPLATE.md`
- Level walkthrough teleprompter scripts: `video/teleprompter/scripts/`
- Cartridge exports: `exports/`
- Badge artwork: `assets/source/*Designer.png` and `*Practitioner.png`
- Canvas API helper: `scripts/canvas/canvas_manager.py` (uses `.env` credentials, never opened directly)
