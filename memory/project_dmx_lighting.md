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

### Rig (preliminary — confirm next visit)
- All Chauvet Rogue
- 4× segmented quadrant movers (likely Rogue R1/R2 Wash — individual ring/cell control)
- 4× beamer-style movers (likely Rogue R1/R2 Beam — tight beam, prism)
- 4× LED pucks audience-facing
- 4× front light
- Some number of back light (count TBD)
- Console: grandMA2 (PC software)

### grandMA2 Export (next visit)
- Setup → Backup → **Save Show As** (NOT Save — avoid overwriting USB content)
- Patch list: Setup → Patch & DMX (fixture names, types, universe, start address)
- Show file captures fixtures, patch, scenes, everything
- Thursday night attempt didn't complete before show; existing `farmhouse 1.show.gz` on /Volumes/TAXES/gma2/shows/ is the binary "MA DATA" format — not directly parseable

### Farmhouse Open Items
- [ ] Get Chauvet Rogue model numbers (sticker on yoke or base)
- [ ] Export grandMA2 show file to USB
- [ ] Photo of patch list from Setup → Patch & DMX
- [ ] Confirm back-light fixture count
- [ ] Build QLC+ fixture profiles from Chauvet docs once models confirmed

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
