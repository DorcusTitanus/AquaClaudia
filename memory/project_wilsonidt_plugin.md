---
name: WilsonIDT Tutorial Plugin — main software project
description: 3-tier system (FastAPI + Canvas LMS + UXP Photoshop plugin) for interactive Photoshop tutorials with Canvas-tracked progress. Jefe's primary software project as of 2026-05.
type: project
originSessionId: c277d2fb-213a-4cdf-9509-14fde273a074
---
## What it is

Jefe's main software project: an interactive Photoshop tutorial system
with three tiers:

- **Backend**: Python/FastAPI, SQLite (dev). JWT-auth.
- **Plugin**: Vanilla JS + Spectrum UXP Widgets, loaded via Adobe UXP
  Developer Tools. Runs inside Photoshop.
- **LMS integration**: Canvas Assignments. OAuth2 Developer Key.

## Goal

Students complete guided Photoshop tutorials inside the UXP plugin;
progress and completion sync to Canvas as graded assignments. Plugin
is one of three parallel formats per tutorial (UDL triple representation):
written text, walkthrough video, interactive plugin.

## Non-negotiables (first principles)

- **WCAG 2.1 AA mandatory** for all content (Canvas, web, plugin)
- **Canvas Accessibility Checker: 0 errors** before any deployment
- **Never fabricate Photoshop features** — Easter Egg incident, April 2026
- Contrast minimums: small text >= 4.5:1, large text >= 3:1
- Tables: caption + thead + th scope. Images: descriptive alt < 120 chars.
- Sequential headings (h2 -> h3 -> h4), never skip levels.

Full accessibility checklist + contrast-safe palette + 12 Canvas Checker
rules in `~/claudeCode/projects/AquaClaudia/memory/MEMORY.md`.

## Curriculum status (April 2026)

11 levels all built and deployed:
1. Mask Architect — non-destructive masking
2. Experimental Designer — glass sphere compositing
3. Visual Storyteller — key art / movie poster
4. Precision Retoucher — frequency separation
5. Motion Beginner — frame animation, GIF
6. Motion Designer — video timeline, keyframing
7. Generative Designer — ML tools (Generative Fill, Neural Filters, etc.)
8. Digital Painter — Mixer Brush, custom brushes
9. Automation Engineer — Variables + Actions + Batch
10. Systems Designer — layer comps + YouTube thumbnail design
11. Advanced Practitioner — capstone, two-week module May 4–14

Each level: 40 pts (Tutorial 5 + Checkpoint 5 + Project 25 + Journal 5).

## Phase status (snapshot from MEMORY.md, may have drifted)

- Phase 6 (Canvas Delivery) ~85%. Remaining: teleprompter scripts,
  walkthrough videos, backend public deploy, screenshots for L3-L5.
- Phase 7 (Visual Guides / annotations) — not started
- Phase 8 (Production) — future

## Known constraints

- **OAuth Developer Key denied by institution** — Jefe can't get a token
  from his school's Canvas. **This is the ROOT CAUSE driving the
  self-hosted Canvas effort on Thufir.** Connect the dots: Canvas
  self-host project = unblock LTI/OAuth dev for this plugin.
- **LTI access also denied** by institution
- **UXP plugins must live on the macOS boot volume** — Photoshop silently
  refuses to load from external drives (confirmed April 2026)

## Open bugs (from MEMORY.md, may be stale)

1. Buttons disappear after clicking Hint — `display: none !important`
   on `.hidden` class likely conflicting with Spectrum `sp-button`
2. No document-open guard — tutorial steps fail silently if no PSD open

## How to apply

When Jefe asks for help on Photoshop tutorial authoring, Canvas assignment
markup, plugin code, or accessibility validation, this is the project
he's working in. Default assumptions:
- Accessibility is non-negotiable, never optional
- Use the contrast-safe palette in `~/claudeCode/projects/AquaClaudia/memory/MEMORY.md`
- Photoshop feature claims must be verified; never fabricate
- The Canvas self-host work on Thufir directly serves this project's
  blocked-by-institution dev needs
