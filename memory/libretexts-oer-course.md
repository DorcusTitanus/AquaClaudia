# LibreTexts OER Course Project

## Project Overview
- **Course**: Personal AI Workflows (next semester, date TBD)
- **Concept**: Students create OER in LibreTexts from recorded/transcribed classroom sessions processed by Jefe's personal AI workflow
- **Recursive design**: Students learn AI workflows BY participating in OER production — the course builds its own textbook
- First cohort creates foundation, future cohorts improve/remix it
- **Target library**: Workforce (student decision, but Jefe's best guess — AI workflows as professional practice, not model-building)
- **License**: CC BY (confirmed — maximizes remixability, LibreTexts default)

## LibreTexts Account Status
- Jefe has 2 LibreTexts accounts (already registered)
- Instructor-level access confirmed

## Content Pipeline (designed, not yet built)
```
Recording -> Transcription -> Claude ingestion -> Structured content
    |
Claude generates LibreTexts-formatted pages (HTML/Markdown)
    |
Students review, edit, add media, build H5P activities
    |
Students paste/upload into LibreTexts CKEditor
    |
Accessibility check -> Peer review -> Publish
```

## Automation Boundaries
**Claude CAN automate:**
- Transcription -> LibreTexts-formatted content (H2-H4 hierarchy, box templates, figure placeholders, alt-text stubs, page titles in `XX.YY: Title` format, CC BY attribution blocks)
- Accessibility pre-checking before content hits LibreTexts
- Remixing map / book skeleton drafting
- H5P content specs (students build them)
- LaTeX equation formatting (minimal for this course)

**Cannot automate (requires browser session):**
- OER Remixer drag-and-drop (no API)
- Page publishing (authenticated browser)
- H5P creation on WordPress server (requires login)
- File uploads to pages (editor session)
- Compiling/batching PDFs

## LibreTexts Formatting Conventions
- **Page titles**: `XX.YY: Title` (colons in titles become dashes)
- **Heading hierarchy**: H2-H4 for main content; H5+ for container elements (Learning Objectives, Examples)
- **Box templates**: Definition, Example, Exercise (hidden answers), Note, Skills, Theorem, Caution
- **Figure captions**: `Figure \(\PageIndex{i}\): [Caption text]` — center justified
- **Alt text**: < 144 chars, decorative images get blank alt, never use filenames
- **Images**: PNG/JPG/GIF only (no BMP/TIFF), no hotlinking, upload originals
- **Tables**: Always mark headers, add captions, enable responsive formatting
- **Links**: Avoid external link farming; use anchors for internal navigation
- **Single-digit URLs**: Use leading zeros (01-09) for proper sorting

## LibreTexts Platform Notes
- Powered by MindTouch CKEditor (WYSIWYG, no HTML needed)
- Autosaves every 25 seconds
- Wiki infrastructure — all edits create revisions, fully revertible
- **Transcluding** = mirroring content (auto-updates from source) — preferred for initial remixes
- **Forking** = independent copy (no auto-updates) — use when substantially modifying
- Once a book is created in a library, it CANNOT be moved — permanent choice
- Every campus can publish 5 course shells without joining LibreTexts Network

## Key LibreTexts URLs
- Registration: one.libretexts.org / register.libretexts.org
- Commons: commons.libretexts.org
- Conductor: (project management — accessed after login)
- LibreStudio (H5P): studio.libretexts.org
- H5P server: h5p.libretexts.org
- ADAPT homework: adapt.libretexts.org
- Support: commons.libretexts.org/support

## LMS Integration
- Export via Common Cartridge v1.1 (.imscc file)
- Canvas import: Settings -> Import -> Common Cartridge 1.x Package -> All Content
- ADAPT connects to LMS via LTI

## Accessibility (LibreTexts built-in)
- Accessibility Checker: Vitruvian Man icon in editor toolbar
- Checks: headings, alt tags, table headers/captions, link text, abbreviations
- Color contrast and labels: currently inactive in checker
- Color contrast ratios documented (all box types meet WCAG)

## Institutional Access Issues (context)
- OAuth Developer Key access denied for Canvas integration (MULTI 211 plugin project)
- LTI access also denied by both institutions
- This influenced exploring LibreTexts/ADAPT as alternative pathways
- ADAPT still needs LTI to talk to Canvas — doesn't solve the access problem
- ADAPT paradigm (Q&A assessment) doesn't match UXP plugin (Photoshop state checking)

## Next Steps (as of 2026-03-02)
1. Jefe needs to decide/finalize course topic arc and syllabus
2. Claude to build transcription-to-LibreTexts formatting pipeline
3. Claude to draft remixing map template once topic sequence is known
4. Library choice (Workforce likely) is a student exercise — early semester
5. Course start date TBD — determines lead time for pipeline development
