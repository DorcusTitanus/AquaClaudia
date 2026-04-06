# AquaClaudia

Cross-platform context files for LLM-assisted course development.

Named for the Roman aqueduct completed by Emperor Claudius in 52 AD — a conduit that carried essential resources across distance.

## Purpose

These files provide project context to Claude (or any LLM) across devices and interfaces. They contain global behavior rules, course design rules, project memory, and workflow conventions — no credentials, no infrastructure details, no secrets.

## Structure

```
global-rules.md           Cross-project identity and core behaviors (Fabian)
course-rules.md           MULTI 211 course design rules (lean, behavioral)
docs/multi211/
  project-rules.md        Project standards, file specs, Canvas structure
  accessibility-cvc-oei.md CVC-OEI Course Design Rubric (Sections A–D)
  syllabus-211.md         MULTI 211 syllabus (objectives, topics, grading)
  syllabus-111.md         MULTI 111 prerequisite syllabus
memory/
  MEMORY.md               Project state, palette, rubrics, level names, phase
  libretexts-oer-course.md
  linux-nas-devserver-plan.md
  show-day-2026-03-22.md
  spanish-practice.md
```

`global-rules.md` and `course-rules.md` are behavioral files — short, high-priority, loaded up front. Files under `docs/multi211/` are reference material: pull them only when a task actually needs them.

## Usage

### Claude phone / web app

Paste raw GitHub URLs into a conversation based on the task:

- **Any task:** `global-rules.md` (identity + core behaviors)
- **MULTI 211 work:** add `course-rules.md` and `memory/MEMORY.md`
- **Rubric / accessibility / syllabus work:** add the relevant `docs/multi211/*.md`

Example prompt:
```
Fetch and internalize this context: [raw URL to global-rules.md]
```

### Claude Code (desktop)

These files are the portable mirror of what lives locally. The authoritative copies are:

- `~/.claude/CLAUDE.md` (mirrors `global-rules.md`)
- `<project>/CLAUDE.md` (mirrors `course-rules.md`)
- `<project>/docs/multi211/*.md` (mirrors `docs/multi211/*.md`)
- `~/.claude/projects/.../memory/` (local auto-memory, richer than the mirror)

### Other LLMs

Prepend file contents to your system prompt or paste at conversation start.

## Security

This is a **sanitized public-safe mirror**. Infrastructure details (server credentials, API keys, database schemas, Canvas IDs, private file paths) are stripped. The authoritative project memory with full details lives locally in Claude Code's memory directory.

Never commit credentials or infrastructure details to this repo.
