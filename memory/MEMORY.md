# WilsonIDT Project Memory (Sanitized)

> This is the portable, public-safe version. Infrastructure details (credentials, IDs, paths) are in the local Claude Code memory only.

## First Principles
- **WCAG 2.1 AA is mandatory** — all content (Canvas, web, plugin) must meet WCAG 2.1 Level AA. This is a first principle, not optional.
- **Canvas Accessibility Checker must pass with 0 errors** — every assignment/page must pass all 12 Canvas Accessibility Checker rules before deployment
- **Contrast minimums**: small text >= 4.5:1, large text (18pt+ or bold 14pt+) >= 3:1 — verified against actual background colors
- **Tables**: always include `<caption>`, `<thead>`, `<th scope="col">` (or `scope="row"`)
- **Images**: descriptive `alt` text (< 120 chars), never use filename as alt text
- **Headings**: sequential (h2 -> h3 -> h4), never skip levels, < 120 chars
- **Color swatches**: use `role="img"` + `aria-label` for decorative color indicators
- **No opacity tricks**: use explicit hex colors, never rely on `opacity` for text contrast

## Project Overview
- **WilsonIDT Tutorial Plugin**: 3-tier system (FastAPI backend + Canvas LMS + UXP Photoshop plugin)
- **Goal**: Interactive Photoshop tutorials inside UXP plugin, with progress tracked to Canvas LMS
- **Owner**: Jefe (professor)

## Architecture (High Level)
- **Backend**: Python/FastAPI, SQLite (dev)
- **Plugin**: Vanilla JS + Spectrum UXP Widgets, loaded via Adobe UXP Developer Tools
- **Auth**: JWT (backend-minted), OAuth2 Developer Key for Canvas
- **Content pipeline**: Source PDF -> Claude authors tutorial JSON -> Author Mode generates reference PNGs -> record video -> build Canvas Assignment

## Asset System
- Assets stored per tutorial (PSDs, images, textures)
- Served via authenticated API endpoint
- Plugin downloads and caches via AssetManager class
- Action types: `OPEN_DOCUMENT` (opens a starter PSD), `PLACE_IMAGE` (places image as smart object)
- Steps can have `reference_image` field to display a guide image in the step card
- Steps have `annotations` field (future use) for visual highlight overlays on reference images
- UXP does NOT support multiple panels — visual guides must be inline in the single panel

## Known Bugs (remaining)
1. **Buttons disappear after clicking Hint**: Likely `display: none !important` on `.hidden` class conflicting with Spectrum `sp-button` internals.
2. **No document-open guard**: Tutorial steps fail silently if no PSD is open. Need pre-check.

## Testing Status
- Plugin needs an open PSD document to work
- Mask Architect tutorial: 9/9 steps pass
- Glass Sphere tutorial: 13/13 steps pass (beta-ready)
- Author Mode: 4/4 captures pass

## Badge Collection System
- Badge grid page shows earned vs locked badges
- Plugin has "Badge Collection" buttons in tutorial list + completion views
- State rules (MVP): earned (completion exists) or unlocked (no prerequisites yet)
- Tutorials can declare a badge asset; fallback to default earned badge image

## UDL & Accessibility
- **Triple-representation model**: Each tutorial = one Canvas Assignment with 3 parallel formats
  1. Written tutorial text (in Canvas Assignment description)
  2. Walkthrough video (instructor screen recording with teleprompter narration)
  3. Interactive UXP plugin (guided steps, checks, hints, progress)
- Plugin is one **means of engagement** — not the only path; no learning outcome depends solely on it
- Satisfies UDL 2.2 Guidelines (Representation, Action/Expression, Engagement)
- WCAG 2.1 AA compliance: text equivalents, semantic HTML, Spectrum widgets handle ARIA

## Photoshop Naming Conventions
- PS duplicate layer appends lowercase " copy" (e.g., "Glass Ball copy" NOT "Glass Ball Copy")
- All LAYER_EXISTS checks and instruction_md text must use PS default casing

## Tutorial Authoring Lessons (from live testing)
- Cmd-click loads selection from hidden layers (no need to show/hide guide layers)
- Specify gradient presets by full name (e.g., "Basics > Foreground to Background")
- Gradient fill layers must be rasterized before pixel filters (Gaussian Blur, etc.)
- Spherize filter drops the active selection — add reload instruction after
- Use Cmd+J (Layer via Copy) for non-destructive background editing
- On-canvas gradient controls = "aspect ratio handles" (not "ellipse handles")
- Layer Style screenshots serve as student reference assets
- Claude replaces GPT pipeline for tutorial authoring (no more hallucinations)
- Single-pass: upload source PDF -> Claude produces complete JSON with AMP captures baked in

## Contrast-Safe Color Palette (reuse for all Canvas assignments)
- `#14501a` on white (dark green headings, 8.6:1)
- `#333` on white (body text, 12.6:1)
- `#595959` on white (captions/figcaptions, 5.9:1)
- `#5c4813` on `#fdf6e3` (hint summary, 7.0:1)
- `#4a3a10` on `#fdf6e3` (hint list items, 9.0:1)
- `#cccccc` on `#2c2c2c` (layer stack notes, 8.5:1)
- `#999999` on `#2c2c2c` (hidden layers note, 5.3:1)
- `#ffffff` on `#2c2c2c` (layer names, 13.3:1)

## Canvas Accessibility Checker Rules (all 12)
1. Adjacent Links — no duplicate adjacent links to same href
2. Heading Paragraphs — headings < 120 chars
3. Image Alt Text — all `<img>` have `alt`
4. Image Alt Filename — `alt` is not a filename
5. Image Alt Length — `alt` < 120 chars
6. Large Text Contrast — >= 3:1 for 18pt+ or bold 14pt+
7. Small Text Contrast — >= 4.5:1 for all other text
8. Lists — use `<ul>`/`<ol>`, not manual numbering
9. Sequential Headings — no skipped levels
10. Table Captions — every `<table>` has `<caption>`
11. Table Header Scope — `<th>` has `scope="col"` or `scope="row"`
12. Table Headers — data tables have `<thead>` with `<th>`

## Canvas Assignment Template Pattern
- h2 for title -> h3 for section -> h4 for each step (sequential headings)
- Inline styles only (Canvas strips external CSS)
- All images hosted externally with descriptive alt text

## Level Names (current)
- Level 1: Mask Architect
- Level 2: Experimental Designer (Glass Sphere tutorial)
- Level 3: (TBD)
- Level 4: Precision Retoucher (Frequency Separation tutorial)
- Level 11: Advanced Practitioner (always last)
- Badge names follow level names

## Rubrics & Points
- **Level total**: 40 pts (Tutorial 5 + Checkpoint 5 + Project 25 + Journal 5)
- **Tutorial Completion rubric** (reusable): 5 pts, 2 criteria (File Submission 3pts / Tutorial Steps Completed 2pts)
- **Weekly Journal rubric** (reusable): 5 pts, 2 criteria (Reflection Depth 3pts 3-tier / Completeness 2pts binary)
- **Peer Review rubric** (level-specific): 25 pts, 5 criteria x 5pts binary (Meets/Not Yet), hide_points
- Peer reviews enabled on all Project assignments (automatic, count: 2)
- Assignment Groups: Tutorials, Projects, Journals

## Phase Status
- **Phase 6 (Canvas Delivery)**: ~60% complete
  - Done: Canvas Assignment template, asset hosting, module placement, UDL framework, checkpoint quiz
  - Remaining: teleprompter scripts, walkthrough video, backend public deploy
  - OAuth Developer Key denied by institution; LTI access also denied
- **Phase 7**: Visual Guides (annotations)
- **Phase 8**: Production
- **Future**: hint button bug, document-open guard, badge prerequisites, WCAG audit

## LibreTexts OER Course
- See `memory/libretexts-oer-course.md` for full details
- Course on Personal AI Workflows — students create OER from transcribed class sessions
- Target: Workforce library, CC BY license
