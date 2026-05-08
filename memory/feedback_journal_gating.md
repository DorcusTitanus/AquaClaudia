# Journal Gating — Context, not Time

**Decided**: 2026-04-22 (Wed, Redlands office; prompted by a student 4 weeks behind who couldn't submit journals because the 2-week window had closed)

## Rule

Weekly journals use `must_submit` completion requirement, same as Tutorial and Project. No 2-week availability window. The journal stays open until the student reaches and completes that level.

Effect: journals become part of the chain that unlocks the next level.

## Why

**Context-sensitive ≠ time-sensitive.** Jefe was conflating the two.

- **Context-sensitive** (tied to the project the student just did): journals, tutorials, checkpoints, projects. These are meaningful whenever the student reaches them. Gate them by completion, not time.
- **Time-sensitive** (need the cohort present): discussions, peer reviews. These genuinely expire — the rest of the class has moved on and the context is gone.

A behind-schedule student who does the journal 4 weeks late, in context with the level they just completed, gets the reflection benefit. A behind-schedule student who does the journal when the window happens to be open — even if they haven't done the project yet — gets nothing.

## How to apply

When building or editing a level:

- Journal completion requirement: `must_submit` (not `none`)
- Do **not** set an availability window (`unlock_at` / `lock_at`) on journals
- Keep `unlock_at` / `lock_at` for discussions and peer reviews where cohort presence matters

Back-populate existing levels (1–9) when time allows. LEVEL-TEMPLATE.md is already updated.

## Related

- `LEVEL-TEMPLATE.md` §Module Completion Requirements (table + rationale paragraph)
- `LEVEL-TEMPLATE.md` §Build Checklist (must_submit Journal line)
