# Late-Publish Recovery — Compress Pedagogy, Not Content

**Logged**: 2026-05-08 (Fri evening, panic mode)

## What happened

Level 11 of MULTI 211 was built and exported as an IMSCC on 2026-04-30. The cartridge was imported into the live Crafton course (sbccd.instructure.com, course 63462) but the module was never published. By Fri May 8 the original schedule (Sun May 4 → Thu May 14) was already 4 days into a 10-day plan and the course end is May 22. Students still had not seen the project brief.

The fix: compress the 2-week capstone to a 6-day Sun May 10 → Fri May 15 module without erasing the integrity of the level.

## The compression decision tree (use this in any future late-publish)

When a multi-deliverable module has to fit a window that's smaller than its original timeline, drop in this order:

1. **Drop the cohort-dependent phases first** — discussion-led pitch, replies, peer review. These need cohort presence to function. Time-shifted, they don't work.
2. **Drop multi-stage submissions next** — Roughs/Finals splits that depend on the peer review they just lost. Collapse to one submission.
3. **Drop deliverable count last** — narrow from N products to 1, but keep the technical depth of that 1.
4. **Never drop the journal.** It's the gating completion item per `feedback_journal_gating.md`. Always preserves the "context-sensitive" reflection.
5. **Never drop the rubric.** Rescale criteria/points if needed; do not assess on vibes.

## The cleanest "single-week capstone" shape

For a course-end capstone with no lead time:

- **Sun:** Module opens, brief becomes visible
- **Fri:** Single submission + course-reflection journal, both due same day
- **No discussion, no peer review, no multi-stage submissions.**
- **The brief asks the student to pick one technique from prior coursework and produce one polished portfolio piece.** This is integrative without requiring new conceptual work in the compressed window.
- **Points consolidated to two graded items:** Project (35) + Journal (5) = 40, matching standard per-level total. The 10 points that would have gone to the discussion fold into Project.

## Preserve the source of truth

The pre-compression IMSCC is the authoritative version of the level. After compressing live, leave that cartridge untouched at `/exports/Level-N-{Badge-Name}.imscc`. Next time the level is taught with proper lead time, re-import from the cartridge into a fresh course shell. Do **not** re-export the compressed Crafton version back into the cartridge — that would overwrite the real plan with the emergency version.

For Level 11 specifically: `/exports/Level-11-Advanced-Practitioner.imscc` (34.6 MB) is the full Sun May 4 → Thu May 14 version with three deliverables, peer review, Comps/Roughs/Finals gates. The live Crafton module is the compressed Sun May 10 → Fri May 15 version. They will diverge until the next semester teaches it properly.

## Secops note (temporary)

During this incident, the `.env` was repointed at Crafton (sbccd.instructure.com), violating Jefe's usual "empty house" rule. Tokens at institutions with real student data should not stay in the .env after the crisis is resolved. Default state is the sandbox/local-host; Crafton tokens come in only for documented emergencies and out again immediately after.

## Tone

Jefe was in panic mode. Don't try to solve the strategic question of why the module wasn't published — he already knows. Focus on shipping. Three crisp options, fast execution, secops boundary held even under pressure. Memory note after the dust settles, not during.
