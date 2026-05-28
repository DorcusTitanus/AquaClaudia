---
name: happenstance-app-architecture
description: "Architecture direction for the Swift \"Happenstance App\" — one app, all clients, ChronCal-shaped GUI with per-client config"
metadata: 
  node_type: memory
  type: project
  originSessionId: f9675d30-5405-4d26-b05b-6db42e1fe12e
---

Architecture anchor from Jefe 2026-05-27: build the Happenstance
Typesetting app **as one app that handles all clients**, modeled on
ChronCal's pattern ([[chroncal-gui]]). Per-client variation lives in
config, not in forked codebases.

## Core decisions made so far

- **One Swift app, not per-client builds.** Replaces the current
  fork-and-edit converter directory pattern
  ([[happenstance-client-dupe-pattern]]).
- **ChronCal-shaped GUI**: SwiftUI mac app, file-based config, comp
  drives it through a GUI with toggles + smart defaults + in-app
  help.
- **Each client is a flavor**, defined by:
  - Front-matter (FM) and back-matter (BM) template differences
  - Design differences (typography, trim size for PRE, CSS for epub)
  - Tag vocabulary (DTD variants — `Happenstance_v01` vs.
    `NABenstance_v01`, etc.)
- **"Give the comp all of the options"** — the UI exposes every
  toggle; per-client config preselects the right defaults so a comp
  picking NAB doesn't have to know which switches NAB needs.

## Known clients to support

At minimum:
- NAB (North Atlantic Books) — only client with an extant converter
  in the repo, most recently active.
- CCC — mentioned by Jefe alongside NAB; converter not yet found in
  the repo, needs enumeration.
- Plus surviving long-tail indie publishers (TBD via the converter
  enumeration pass).

Already-dead clients (do NOT design support for):
- Wiley ([[user-wiley-history]]) — offshored 2013.
- No Starch Press ([[publishing-decline-timeline]]) — moved to
  Overleaf 2023.

## Architecture decisions locked (Jefe 2026-05-27)

1. **Client configs live on the Sync.com drive** (Buttercup/zHappenHub
   pattern — same place as current Converters/). Auto-backed-up, every
   comp has access. Happenstance web server is the fallback if Sync.com
   becomes a problem (target 99.9% uptime).
2. **Client = top-level toggle/radio selector**, same UX shape as
   ChronCal's product selector. Selecting a client pulls that client's
   masthead, logotypes, assets, and toggle defaults. Not derived from
   filename/job number — explicit comp choice per job.
3. **PRE and POST unified in one app.** Both stages produce XML or
   XML-derived outputs: PRE → InDesign-ready human-readable XML;
   POST → Word XML, epub xhtml, and any other structured-data outputs a
   client needs. Internal model is "XML transformation pipelines with
   different output targets," not "PRE app + POST app."
4. **App drives InDesign automatically.** Swift fires JSX (or future
   UXP) via AppleScript / Scripting Bridge / Process. Keep existing
   JSX exporters intact for v1 — zero porting required. Comp's only
   manual step inside the workflow is the actual typesetting work.

## Decided 2026-05-27 (continued)

5. **Layout-complete signal = comp-initiated button.** The Swift app
   exposes a "start post-process" button the comp clicks when they're
   done in InDesign. No file watchers, no auto-detection — the comp
   is in control of when POST runs. This matches the production reality
   (see cadence below) where the InDesign stage can last days or weeks.

## Still open (decide before coding starts)

- **UXP migration path**: keep JSX forever or port to UXP eventually?
  Adobe has been "deprecating" ExtendScript for years without removing
  it; safe to defer.

## Production cadence (sizing constraint)

A single book's run through the pipeline takes anywhere from **just
under a week to over a month**. Drivers per Jefe 2026-05-27:
- Ship date (deadline pressure)
- How clean the dev editor + author got the .docx before handoff
- Whether the client paid for quick turnaround
- "...plus a bunch of other shit that I don't understand"

Architectural implication: the app must tolerate **very long-lived
jobs**. A comp may PRE-process a book today and POST-process it five
weeks from now after the InDesign work concludes. Jobs are durable
state that survives app restarts, machine reboots, and (probably)
macOS upgrades. The job's working files + config live on disk in a
known place; the Swift app is a viewer/controller, not the system of
record for the work itself.

This also kills any "automatic" handoff scheme — five weeks of
InDesign work means file-watcher heuristics will fire wrong dozens of
times. Comp-button is the right call.

## Inheriting from ChronCal

- Iterative not big-bang ([[chroncal-iterative-not-oneshot]]) — ship
  v1 covering NAB epub + Word only; add clients and formats over
  releases.
- Comp-friendly: lint review pane, comprehensive pre-checks, in-app
  help, save/save-as, ⌘+/− zoom (ChronCal v1.1 reference points).
- Regression harness from day one against the TBs of historical
  titles on Sync.com — diff old-pipeline vs. new-pipeline output.
