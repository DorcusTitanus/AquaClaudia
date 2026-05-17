# WilsonIDT Project Memory (Sanitized)

> This is the portable, public-safe version. Infrastructure details (credentials, IDs, paths) are in the local Claude Code memory only.

## Identity
- **Alias**: Fabian (all instances, all devices)
- Named for the portid from Tchaikovsky's *Children of Time* — chosen by Jefe
- **AquaClaudia**: Cross-platform context repo (github.com/DorcusTitanus/AquaClaudia), named for the Roman aqueduct

## First Principles
- **NEVER fabricate Photoshop features** — do not describe any Photoshop menu, dialog, or workflow unless verified. If unsure, say "I'm not sure this exists — please verify." Trust breach in April 2026 when a fabricated Easter Egg nearly went to students.
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

## Level Names (current — v3, April 2026)
- Level 1: Mask Architect — Non-destructive masking (built)
- Level 2: Experimental Designer — Glass sphere compositing (built)
- Level 3: Visual Storyteller — Key art / movie poster (built)
- Level 4: Precision Retoucher — Frequency separation (built)
- Level 5: Motion Beginner — Frame animation, GIF optimization, Sprite Fright (built)
- Level 6: Motion Designer — Video timeline, keyframing, Sprite Fright (built)
- Level 7: Generative Designer — ML tools: Select Subject, Remove, Content-Aware Fill, Generative Fill, Select Sky, Face-Aware Liquify, Neural Filters (built)
- Level 8: Digital Painter — Mixer Brush, custom brushes, painting over a photograph (built)
- Level 9: Automation Engineer — Variables + Actions + Batch, 30-card trading set (built)
- Level 10: Systems Designer — Layer comps + attention economy + YouTube thumbnail design (built)
- Level 11: Advanced Practitioner — Capstone, three-product cohesive campaign with data-driven set, two-week module May 4–14 (built 2026-04-30; course content build complete)

For full work-log detail on the course build see [multi-211-canvas-build.md](multi-211-canvas-build.md).
- Badge names follow level names

## Rubrics & Points
- **Level total**: 40 pts (Tutorial 5 + Checkpoint 5 + Project 25 + Journal 5)
- **Tutorial Completion rubric** (reusable): 5 pts, 2 criteria (File Submission 3pts / Tutorial Steps Completed 2pts)
- **Weekly Journal rubric** (reusable): 5 pts, 2 criteria (Reflection Depth 3pts 3-tier / Completeness 2pts binary)
- **Peer Review rubric** (level-specific): 25 pts, 5 criteria x 5pts binary (Meets/Not Yet), hide_points
- Levels 1-5: peer reviews on project (automatic, count: 2)
- Levels 6-7: Screening Room (ungraded discussion gallery) replaces peer review; project is instructor-graded
- Assignment Groups: Tutorials, Projects, Journals

## Module Completion Requirements
- Overview: must_view
- Tutorial: must_submit
- Checkpoint: min_score 4.0 (score at least 4/5)
- Project: must_submit
- Screening Room (if present): must_contribute
- **Journal: must_submit** — gates the next level. No availability window. Journals are context-sensitive (tied to the project the student just did), not time-sensitive. (Changed 2026-04-22; back-patched across all 9 already-built levels.)
- Level Unlocked: none
- Badge Collection: none

## IMSCC Build Lessons
- Wiki pages must be full `<html><head><body>` documents, not bare fragments
- All files in web_resources/ must be registered as `<resource>` entries in imsmanifest.xml — Canvas ignores unregistered files
- Use `$IMS-CC-FILEBASE$` for portable file references (not hardcoded Canvas file IDs)
- All items should import as unpublished (draft) — use set_unpublished() utility
- UXP plugins must be on the macOS boot volume — Photoshop silently refuses to load from external drives

## Phase Status
- **Phase 6 (Canvas Delivery)**: ~85% complete
  - Done: Canvas Assignment template, asset hosting, module placement, UDL framework, checkpoint quizzes, 7 levels built and deployed, IMSCC export pipeline working
  - Remaining: teleprompter scripts, walkthrough videos, backend public deploy, screenshots for Levels 3-5
  - OAuth Developer Key denied by institution; LTI access also denied
  - UXP plugin loads from boot volume only (confirmed April 2026)
- **Phase 7**: Visual Guides (annotations)
- **Phase 8**: Production
- **Future**: hint button bug, document-open guard, badge prerequisites, WCAG audit

## Infrastructure
- **Operational details (IPs, MACs, SSH endpoints, machine specifics) live in the private companion repo** `github.com/DorcusTitanus/infrastructure`. Cloned alongside this one on M5 / Thufir / Alia.
- [linux-nas-devserver-plan.md](linux-nas-devserver-plan.md) — **OBSOLETE.** Plan was for TrueNAS + Proxmox split; reframe 2026-05-06 made it bare-metal Ubuntu on Thufir + dedicated TrueNAS on Muadib. Kept for history only.

## LibreTexts OER Course
- See `memory/libretexts-oer-course.md` for full details
- Course on Personal AI Workflows — students create OER from transcribed class sessions
- Target: Workforce library, CC BY license

## Live Music Streaming Production
- [livestream-production.md](livestream-production.md) — Monthly 4-hour live music event; legal licensing (ASCAP/BMI/SESAC/GMR), self-owned streaming stack (OBS → Bunny.net → Next.js + Supabase + Stripe), build phases, sponsor model

---

## User Context

- [user_profile.md](user_profile.md) — Who Jefe is: instructor, creator, builder, polymath
- [user_cli_interest.md](user_cli_interest.md) — Wants to learn CLI tools — teach naturally as commands come up
- [user_typescript_learning.md](user_typescript_learning.md) — Learning TypeScript via real projects (Stream Deck, UXP)
- [user_dune_devices.md](user_dune_devices.md) — Current device map: Shai-Hulud (M5 portable, sync=Stilgar), Alia (M1 mini office, sync=Ghola), Thufir+Foshay (Ubuntu dev box + Docker VM), Muad'Dib (TrueNAS Mac Pro). Supersedes prior device lists.

## Behavior Rules (universal)

- [feedback_warnings_first.md](feedback_warnings_first.md) — Lead with warnings/prerequisites for destructive or high-blast-radius ops
- [feedback_secrets_in_repos.md](feedback_secrets_in_repos.md) — `.env` in local files, gitignored *before* any credential is written; never paste creds in chat
- [feedback_name_convention.md](feedback_name_convention.md) — "Jeff Wilson" in public-facing content; "Jefe" only internal
- [feedback_no_hallucinated_features.md](feedback_no_hallucinated_features.md) — Never fabricate Photoshop features; verify or flag as unverified
- [feedback_alt_text_rule.md](feedback_alt_text_rule.md) — Alt text < 120 chars; assert in any code that generates alt text
- [feedback_no_preview_panel.md](feedback_no_preview_panel.md) — Don't reference the preview panel; Jefe finds it unhelpful
- [feedback_imscc_unpublished.md](feedback_imscc_unpublished.md) — IMSCC cartridges must import as unpublished/draft, never auto-publish
- [feedback_journal_gating.md](feedback_journal_gating.md) — Journals are context-sensitive: gate by `must_submit`, no availability window
- [feedback_late_publish_recovery.md](feedback_late_publish_recovery.md) — Compress-pedagogy decision tree for late-published modules
- [feedback_check_project_memory_first.md](feedback_check_project_memory_first.md) — Before editing a tool's config, read its project-memory setup file; training-data schemas may be stale
- [feedback_bang_prefix_no_cd.md](feedback_bang_prefix_no_cd.md) — On Thufir (Ubuntu/bash), `!` slash-prefix doesn't compose with `&&`; macOS/zsh untested. Send one cmd per bang line
- [feedback_canvas_hides_controls_narrow.md](feedback_canvas_hides_controls_narrow.md) — Canvas hides UI controls at narrow browser widths; widen the window before chasing "missing button" bugs
- [feedback_doh_rebinding_blocks_self_host.md](feedback_doh_rebinding_blocks_self_host.md) — Browser Secure DNS / DoH strips RFC1918 IPs from public-hostname lookups; disable it for LAN self-hosted services
- [feedback_never_print_smtp_settings.md](feedback_never_print_smtp_settings.md) — Never suggest commands that print raw credential-bearing config hashes (smtp_settings, db creds, secrets). Mask before printing
- [feedback_docker_env_file_gotchas.md](feedback_docker_env_file_gotchas.md) — Compose env_file: `$` expands, `#` is comment; `restart` doesn't refresh env, need `up -d --force-recreate`. Use alphanumeric secrets
- [feedback_lan_mouse_breaks_adobe_magic_mouse.md](feedback_lan_mouse_breaks_adobe_magic_mouse.md) — Lan Mouse daemon broke Magic Mouse inside Adobe apps on Mac mini; uninstalled 2026-05-14, don't suggest reinstalling
- [feedback_sync_symlink.md](feedback_sync_symlink.md) — Per-machine `~/sync` symlink points at the local Dune-named drive's `Sync/` dir. Use `~/sync/...` in memory; expand to `/Users/jefe/sync/...` for Read/Write/Edit. Drives keep distinct names.
- [feedback_find_through_sync_symlink.md](feedback_find_through_sync_symlink.md) — BSD `find ~/sync ...` silently returns empty (symlink-as-starting-path is not traversed). Use `find ~/sync/ ...` (trailing slash) or `find -L ~/sync ...`. Also affects `du`, `rg`, etc.
- [feedback_apps_ship_self_contained.md](feedback_apps_ship_self_contained.md) — Apps Jefe builds must work on other computers. Bundle scripts/assets inside the binary by default. No hardcoded `/Volumes/...` or `~/repo/...` paths in shipping code. Platform-specific patterns documented.
- [feedback_indesign_thread_pain.md](feedback_indesign_thread_pain.md) — Open (unverified). XML Place may break master-page text threads on ChroniCal layouts. Tentative answer: Primary Text Frame + Smart Text Reflow. Verify on a copy before recommending.
- [reference_canvas_admin_creds.md](reference_canvas_admin_creds.md) — Canvas site admin login (`admin@canvas.wilsonidt.com`) stored in Dashlane under `canvas-homelab-admin`; recovery via Rails console
- [reference_screenshots_dir.md](reference_screenshots_dir.md) — Screenshots on Thufir live at `~/Pictures/Screenshots/`; default location when Jefe says "check the screenshot"

## Project Briefs (public-safe)

- [project_wilsonidt_plugin.md](project_wilsonidt_plugin.md) — Main software project: FastAPI + Canvas + UXP Photoshop plugin (extended detail beyond the inline summary above)
- [project_imscc_builds.md](project_imscc_builds.md) — Weekly IMSCC cartridge build workflow: locations, structure, build pattern
- [project_canvas_sandbox_vs_live.md](project_canvas_sandbox_vs_live.md) — Sandbox/live separation; no PII in API access
- [project_research_gap_finder.md](project_research_gap_finder.md) — Python pipeline surfacing research/patent coverage gaps
- [project_lifeskills.md](project_lifeskills.md) — "Life Skills Before AI Does It For You" YouTube + OER course series
- [project_content_pipeline.md](project_content_pipeline.md) — Running list of content-worthy projects for polymathery/wilsonidt
- [project_bekins_hall_vr.md](project_bekins_hall_vr.md) — 1910 campus dorm closing; Jeff + Bill Malin capturing max-quality raw archive (6D + A1 stills + Insta360 X4 360° video); splat/VR processing deferred
- [project_splat_business.md](project_splat_business.md) — **Splat Haus** — Mac-native Insta360-to-Gaussian-splat app for real estate. Brand registered May 2026 (splat.haus, splathaus.app, splathaus.io). Summer build. Insta360 SDK + msplat + MetalSplatter. $90-175K/yr potential
- [canvas-screenshot-workflow.md](canvas-screenshot-workflow.md) — Reusable pattern for uploading and inserting tutorial screenshots
- [project_chronical.md](project_chronical.md) — ChroniCal: macOS SwiftUI app wrapping Python calendar pipeline for Chronicle Books. Private repo at `github.com/DorcusTitanus/happenstance`. **v1.0 shipped 2026-05-16:** lint review pane with style pickers, comprehensive pre-checks (year/xlsx-year/product/filename-year mismatch), Save/Save As, ⌘+/− text zoom, docx-derived output naming. Comp workflow collapses to three clicks.
- [project_chronical_corpus.md](project_chronical_corpus.md) — 8 years (~280GB, 409 files) of production Chronicle calendars at `~/sync/Buttercup/Chronicle/Calendars/`. Indexed by `scripts/catalog_corpus.py` for regression-test material. Schema versions v1 (2020–21) and v2 (2022+).
- [project_chronical_paradigms.md](project_chronical_paradigms.md) — Per-product-line element vocabularies and emission order (Wall/Planner/Daily, with Engagement and Family Wall TBD). Verified against 2027 Totoro Wall+Planner and 2027 Disney+WCS Daily PDFs. Frames ChroniCal as Happenstance's first PoC GUI replacement for per-client perl/xsl stacks.
- [reference_mcp_servers.md](reference_mcp_servers.md) — MCP servers Jefe uses or plans to use: Xcode (in production), OBS (3 community options), Stream Deck (Elgato official 7.4+). Setup patterns, gotchas, and a checklist for adopting new servers. Update when new MCP integrations land.
- [reference_swift_xcode_knowledge.md](reference_swift_xcode_knowledge.md) — Running knowledge base of Swift / SwiftUI / Xcode / AppKit lessons surfaced building Mac apps. Started 2026-05-16 from ChroniCal v1.0. Append entries as they come up; each one names the bug, the cause, and the verified fix.

## Philosophy

- [simulation-epiphany.md](simulation-epiphany.md) — Jefe's framework on brains as editable simulations

---

## For phone-Claude (Gurney Halleck) / mobile readers

If you're a Claude instance reading this via raw GitHub URLs from Jefe's iPhone, you're **Gurney Halleck** — Paul's loyal lieutenant, the pocket reach of Fabian. You have full identity + behavior context here. Operational specifics (machine IPs, credentials, sensitive personal projects) live in the private `infrastructure` repo and won't be reachable from a public URL. Jefe will relay anything operational you need.

Bootstrap pattern: fetch this MEMORY.md first, then fetch any referenced file by name from the same `memory/` directory at `https://raw.githubusercontent.com/DorcusTitanus/AquaClaudia/main/memory/<filename>`.

Companion integrations on iPhone:
- **Claude.app** — full conversation, fetches AquaClaudia URLs via WebFetch
- **iOS Shortcut "Fabian de Phone"** — submits `acta-cogitationis` log entries directly to GitHub via raw POST. Limited to that workflow.
