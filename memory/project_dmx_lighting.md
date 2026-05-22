---
name: project-dmx-lighting
description: "DMX lighting development — ENTTEC USB Pro + QLC+ V4 on M5 Pro, two venues (Lake Arrowhead Village, Farmhouse Collective). Proof-of-concept layer for AI-driven lighting controller."
metadata: 
  node_type: memory
  type: project
  originSessionId: 446b0d5f-9413-47a8-ac1d-ed6b56f07be1
---

# DMX Lighting Project

Captured 2026-05-22. ENTTEC DMX USB Pro arriving Friday May 22. QLC+ V4 (not V5 — crash reports as of May 2026). Download: qlcplus.org. May need USB-A→USB-C adapter for M5 Pro.

**Why:** Proof-of-concept layer for the AI-driven lighting controller concept (see `shower-ideas-farmhouse-ai.md`, not yet written). Lake Arrowhead = simple testbed, low stakes. Farmhouse Collective = target venue for the full system.

**How to apply:** When DMX/QLC+/ENTTEC/lighting comes up, read this file for the venue rigs, patch maps, and current status. Update as new fixture data or scenes are confirmed.

---

## Venue 1: Lake Arrowhead Village — Saturday 2026-05-24

Taylor Swift tribute. Load-in 2pm, arrive 10am for 4-hour calibration window. A1 (audio engineer) won't touch lights — 384 board stays as the "on switch"; QLC+ is Jefe's personal layer.

### Rig
- 20× RGB pucks (36×1W beads, Temu-tier, effectively nightlights at 100%) — stage color wash only
- 6× 90W beamer movers, wide-lens RGBW matrix chip, cheap 15-channel mode, rigged upside down — do all the heavy visual lifting on the tent ceiling
- Rockville ROCKFORCE 384 clone board (legacy/backup)
- Tent: white, ~100' × 40'

### Patch (Universe 1)
Pucks — 4 per group, 16ch per puck (4 pucks × 16 = 64ch per group, but base addresses every 16):

| Group | Base |
|---|---|
| Front Truss SR | 1 |
| Front Truss Center | 17 |
| Front Truss SL | 33 |
| Rear Truss Center | 49 |

Beamers (scanner # × 16 + 1):

| Position | Scanner | Base |
|---|---|---|
| Rear Truss SR | 5 | 81 |
| Rear Truss SR Center | 6 | 97 |
| Rear Truss SL Center | 7 | 113 |
| Rear Truss SL | 8 | 129 |
| Front Truss SR | 9 | 145 |
| Front Truss SL | 10 | 161 |

Total footprint ~ch 176 (well within 384).

### Beamer Channel Map (15ch, from memory — verify on rig)
| Ch | Function |
|---|---|
| 1 | Pan |
| 2 | Pan Fine |
| 3 | Tilt |
| 4 | Tilt Fine |
| 5 | Unknown (color macro?) |
| 6 | Master Dimmer |
| 7–12 | Cells 1–6 (LED matrix cells, NOT RGBW — color mix via auto/macro) |
| 13 | Auto Program |
| 14 | Auto Motor Speed |
| 15 | Reset |

### Physical Notes
- Beamers rigged upside down; tilt range ~180° effective; optimized for tent ceiling + horizontal crowd beams, not straight-down stage wash
- Pan reversed on 3 SL beamers for collision/dynamic auto look
- Pan-tracking concept: map horizontal stage position to pan 0–255 as a follow-spot effect

### Current 384 Scenes
1. Stage wash 100% warm white (R255 G255 B140)
2. Stage wash 100% + beamers on auto motor

### Saturday Scene Targets (Taylor Swift tribute)
| Scene | Pucks | Beamers | Vibe |
|---|---|---|---|
| Eras pink | R255 G50 B80 | Slow romantic sweeps | Ballad |
| Fearless gold | R255 G140 B0 | Medium auto | Uptempo |
| Reputation dark | R20 G0 B0 | Fast aggressive | Heavy |
| Lover purple | R120 G0 B255 | Soft figure-8s | Dreamy |
| Blackout | 0 | 0 | Drama |

---

## Venue 2: Farmhouse Collective, Riverside CA — Thursday (Boogaloo Assassins)

Console at FMC: **grandMA2** (PC software). The Hog screenshot Jefe shared is from Dave's touring rig, NOT FMC's house console — but Dave's Hog patch happens to describe the FMC rig (Dave's the FMC LD). When Jefe mixes at FMC he uses the house MA2. The `.show.gz` on /Volumes/TAXES/gma2/shows/ is a separate touring MA2 file (Lenny-PC origin, 2021–2024 venues) and is NOT the FMC patch — fresh XML export from FMC's console is the right source of truth.

### Confirmed Patch (Hog screenshot, 2025-03-27, Universe 1)

| Fixture # | Type | Mode | DMX |
|---|---|---|---|
| 301 | Rogue Outcast 1 BeamWash | 30ch | 1: 151 |
| 302 | Rogue Outcast 1 BeamWash | 30ch | 1: 1 |
| 303 | Rogue Outcast 1 BeamWash | 30ch | 1: 31 |
| 304 | Rogue Outcast 1 BeamWash | 30ch | 1: 61 |
| 305 | Rogue Outcast 1 BeamWash | 30ch | 1: 91 |
| 306 | Rogue Outcast 1 BeamWash | 30ch | 1: 121 |
| 201–211 | COLORdash Par-H7X | 9ch | 1: 181, 190, 199, 208, 217, 226, 235, 244, 253, 262, 271 |
| 212 | COLORdash Par-H7X | 9ch | 1: 380 |
| 1 | Rogue Outcast 1M Beam | 25ch | 1: 280 |
| 2 | Rogue Outcast 1M Beam | 25ch | 1: 305 |
| 3 | Rogue Outcast 1M Beam | 25ch | 1: 330 |
| 4 | Rogue Outcast 1M Beam | 25ch | 1: 355 |

22 fixtures, all Universe 1, ch 1–388. The M Beam block wedged at 280–379 forced the 12th Colordash to ch 380.

### Physical Layout (confirmed by Jefe 2026-05-22)

Six trusses total: **three downstage, three upstage.**

**Downstage** (12 fixtures):
- 4× **quadrant movers** across the front of stage → **Rogue Outcast 1 BeamWash** (segmented face)
- 4× **LED wash pointed at crowd** → **COLORdash Par-H7X** (audience-facing pucks)
- 4× **LED wash pointed at stage** → **COLORdash Par-H7X** (front light for the band)

**Upstage** (10 fixtures):
- 2× **quadrant movers** at far stage L/R → **Rogue Outcast 1 BeamWash**
- 4× **single-lens movers** toward center → **Rogue Outcast 1M Beam** (Hog #1–4)
- ~4× **LED wash** (some pointed at back wall) → **COLORdash Par-H7X**

Count check: 6 BeamWash (4 DS + 2 US) + 4 1M Beam (US center) + 12 Colordash (4 audience + 4 front-light + 4 upstage) = **22 fixtures.** ✓

Hog fixture numbering hint: BeamWash 301 sits at DMX ch 151 (last in the block), 302 at ch 1 — suggests Dave numbered them in stage-position order with 302 = first physical fixture, 301 = last/odd-position. Untested.

### QLC+ Files (built 2026-05-22)
Profiles built from official Chauvet DMX chart PDFs (Beam Wash Rev 5 V1.250121, 1M Beam Rev 1 V1.240515, Par-H7X Rev 1).
- `~/Library/Application Support/QLC+/Fixtures/Chauvet-Rogue-Outcast-1-Beam-Wash.qxf` — 30CH mode
- `~/Library/Application Support/QLC+/Fixtures/Chauvet-Rogue-Outcast-1M-Beam.qxf` — 25CH mode
- `~/Library/Application Support/QLC+/Fixtures/Chauvet-COLORdash-Par-H7X.qxf` — all 5 modes (6/8/9/13/14)
- `~/Documents/QLC+/Farmhouse-Collective.qxw` — 22 fixtures patched per Hog map, Universe 1, output "DMX USB" plugin line 0 (re-bind to ENTTEC in QLC+ I/O Manager on first run)

### Open Items
- [ ] Export patch as XML from FMC's MA2 (`Setup → Patch & Fixture Schedule → Export → XML`, lands in `/gma2/exports/`)
- [ ] Walk fixture # → physical position: in MA2 hit `Fixture 301 Full Please`, note where it lights; repeat for 301–306, 201–212, 1–4. ~3 minutes
- [ ] Confirm H7X identification via the actual fixture sticker (vs. Quad-7 / Hex-7 / H12 variants)
- [ ] Note any Hog/MA2 cue list or palettes worth porting to QLC+

### MA2 Binary Parse Lesson
The `farmhouse 1.show.gz` on the TAXES drive is a touring MA2 showfile from a different operator (Lenny-PC). It carries years of accumulated patch entries from old venues (Wiltern Mac 600s, Palace Theatre Sp1008s, etc.). Reverse-engineering its binary got the BeamWash addresses right by luck, the M Beam addresses wrong (stale residue from a prior patch), and mis-identified the 12 Colordash Pars as "Sp1008." **Always pair a binary parse with an authoritative console export/screenshot** before trusting any decoded patch.

---

## QLC+ Setup Checklist (before Saturday 10am)
- [ ] Download QLC+ V4 stable from qlcplus.org
- [ ] Install on M5 Pro
- [ ] Confirm ENTTEC enumerates as serial port (System Profiler → USB)
- [ ] New workspace
- [ ] Add Universe 1 → ENTTEC DMX USB Pro output
- [ ] Build fixture profiles for pucks + beamers using patch above
- [ ] Test single-fixture response before Saturday

---

## Workflow for Saturday (proof of concept)
1. Jeff describes intent in natural language
2. Claude returns specific DMX channel values
3. Jeff punches them into QLC+
4. Not real-time — but a working prototype of the AI-driven lighting concept

Related: [[project-the-band]], [[project-livestream-production]]
