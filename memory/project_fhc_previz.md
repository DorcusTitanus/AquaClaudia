---
name: project-fhc-previz
description: "Home for FHC previsualization: the BlenderDMX model build (FHC_Stage.blend) + the QLC+ → Art-Net link, so the rig can be programmed from home with no venue access. Hub doc linking the laser survey, busking layout, and DMX rig."
metadata: 
  node_type: memory
  type: project
  originSessionId: f674c85e-5a5f-4195-9f56-b3a6c44fecbd
---

# FHC Previsualization — Blender Build + QLC+ Art-Net Link

This is the **home doc** for Farm House Collective previsualization. Three workstreams live here: (1) the Blender stage/rig model, (2) the QLC+ → Art-Net → BlenderDMX link, (3) using them together to program QLC+ from home.

**Why:** Programming the FHC rig requires either on-site access or a faithful visualizer. A measured Blender model + live Art-Net feed lets Jefe author scenes/looks at home and see them land on real geometry — and the Blender skill transfers. Sessions are per-machine and don't sync (the original measurement session was stranded on Alia), so this doc is the durable anchor; work from it, not from a chat transcript.

**How to apply:** When doing any FHC previz work, start here and follow the pointers. Update this doc's "Current state" + "Next steps" as the build progresses. Keep detailed protocol in the linked docs — this stays a hub.

Related: [[reference-fhc-stage-geometry]] (laser measurements = ground truth), [[project-apc-mini-busking-layout]] (QLC+ workspace + rig patch the link drives), [[project-dmx-lighting]] (rig + venue context), [[project-ai-lighting-controller]] (north star that consumes the spatial model).

---

## Files

- **Blender model:** `/Volumes/Stilgar/Sync/Documents/ClaudeCode/FHC_lightRig/FHC_Stage.blend` (+ `.blend1` backup). Blender 5.1.2.
- **Measurements (ground truth):** `AquaClaudia/memory/reference_fhc_stage_geometry.md` — laser survey, June 2026.
- **GDTF fixture profiles (verified channel-by-channel against the QLC+ patch):** in `~/Downloads/` —
  - `Chauvet_Professional@Rogue_Outcast_1M_Beam@Beta_1.0.2.gdtf` → use **25ch** mode
  - `Chauvet_Professional@Rogue_Outcast_1_BeamWash@Rev_1.0.3.gdtf` → use **30Ch** mode
  - (Beams are **1M Beam**, NOT "1 Beam"/"1L"/"2 Beam".)
- **QLC+ workspace the link drives:** `~/Documents/QLC+/Farmhouse-Busking-Base.qxw`.

## The QLC+ → Art-Net link (previz path)

- **Visualizer:** BlenderDMX add-on (free, Apple-Silicon native, GDTF import, no save/motion limits). Capture was rejected — free tiers disable save or disable motion (fatal for an all-mover rig).
- **Connection:** QLC+ → Inputs/Outputs → add an **Art-Net output** on the universe (alongside the ENTTEC out). BlenderDMX listens.
- **Launch order matters:** start the visualizer + its Art-Net input **FIRST**, then QLC+ (port binding). All local/LAN, no cloud — within secops boundary.
- ArtNetView = value monitor only (sanity check, not 3D).

## Current state (2026-06-09)

- **Blender structure built** from the survey: `01_Stage+Structure` (Stage_Platform), `02_Back_Wall_LED` (LED_Screen), `03_Trusses` (FT + RT truss set), `05_Roof_Pergola` (front/rear I-beams + pillars).
- **Art-Net → BlenderDMX link PROVEN end-to-end 2026-06-09**: Beam GDTF imported, Beam01 patched (BlenderDMX universe 1, DMX address 280 = QLC+ Beam 1), injected ArtDMX packets rendered live light in the viewport. BlenderDMX 2.2.2 runs clean on Blender 5.1.2.
- **Survey** complete and synced; several items still estimated (sub-box dims, column base, fan hub-to-hub, pergola depth — see survey "Still needed").
- **QLC+ workspace** mature (busking layout v2, scenes fn1–fn781) — see busking-layout doc. Art-Net output added to Universe 1 (`127.0.0.1`, ArtNet universe 1) alongside the ENTTEC out; backup `Farmhouse-Busking-Base.qxw.bak-artnet`.

## Link debugging lessons (2026-06-09 — hard-won, reread before next session)

1. **BlenderDMX's Art-Net listener can go stale**: it can hold UDP 6454 but silently not process packets ("Waiting for data..." forever). Happens when input was enabled before universes/fixtures existed. **Fix: toggle "Enable Art-Net Input" off/on after any universe/fixture change** (or restart Blender). The toggle does NOT persist across Blender relaunches — re-enable each session.
2. **QLC+ universe ↔ Art-Net universe mismatch**: QLC+ Universe 1 (internal ID 0) defaults to Art-Net universe 0; BlenderDMX's "Art-Net Universe" slot is universe 1. Set QLC+'s ArtNet output universe to **1** via the wrench config on the `127.0.0.1` line.
3. **Check WHICH universe the output checkbox is on** — the I/O mapping grid belongs to the universe selected in the left pane. Easy to check ArtNet on Universe 2 (APC) by accident.
4. **Shutter mapping differs between the real rig and the GDTF**: real-rig scenes use Beam shutter ch9 = 5 (open on hardware), but the GDTF renders that dark. **Shutter = 190 renders open in BlenderDMX.** Expect rig scenes to need a shutter shim for previz.
5. **Viewport must be in Material Preview or Rendered shading** to see any light; enable Setup → Beam Volume for the cone. Solid shading shows movement only.
6. **Re-checking a QLC+ output checkbox RESETS its plugin parameters** — the ArtNet universe silently reverts to 0. After ANY change to the I/O mapping, reopen the wrench config and re-verify ArtNet Universe = 1. Symptom: BlenderDMX says "Online" (packets arrive) but ingests nothing (wrong universe in the packets).
7. **Debugging tools that worked**: `lsof -nP -i UDP:6454` (who owns the port); launch Blender from shell with stdout to a log to see BlenderDMX's DEBUG logging (Setup → Logging → Debug shows `set_universe` lines proving ingest); inject hand-built ArtDMX packets from Python to bisect QLC+-side vs Blender-side (packet: `b'Art-Net\0' + 0x00 0x50 0x00 0x0e 0x00 0x00 + uniLo + uniHi + 0x02 0x00 + 512 bytes`).

**LOOP CLOSED 2026-06-09 ~19:53**: QLC+ Simple Desk faders drive Beam01 live in the Blender viewport via Art-Net loopback. Full previz chain operational: QLC+ U1 → ArtNet out (127.0.0.1, ArtNet universe 1) → BlenderDMX universe 1 → GDTF fixture. Stray 10.1.132.22 output removed; only the 127.0.0.1 line active.

**All 4 M1 Beams rigged in Blender (2026-06-09, same session).** PoC declared a success by Jefe. Open items from first full test:
- **Beam X positions suspect**: running the Back Wall look sent some beams to the wrong place — likely the fixtures' X spacing on the truss doesn't match the real rig order/spacing (real-rig position scenes only land right if each virtual fixture sits where its physical twin does; also verify base rotations match the physical hang). Jefe debugging next session.
- **BlenderDMX render limits observed (Z-Rendered/Eevee)**: gobo and prism do NOT render — only aim + beam color. Frost doesn't change spot size either. Aim + color + dimmer is the usable previz vocabulary for now; gobo/prism/frost looks must still be dialed at the venue. (Worth checking later whether Cycles or BlenderDMX settings add gobo support — unverified.)
- Fixture base alignment workflow: Blender's Align Objects operator CRASHES on BlenderDMX fixtures (bases are Empties — `UnboundLocalError: center_active_z`, Blender 5.1.2 bug with Relative To: Active). Use N-panel right-click → Copy to Selected on Location Y/Z + Rotation Z instead, then set X per fixture.

## Next steps

1. **Fix the 4 beams' X positions** so the Back Wall look lands correctly. Physical order from the busking doc: Beam4 = SL, Beam2+Beam3 = center, Beam1 = SR. Check spacing against the survey + base rotations against the physical hang.
2. Finish populating `04_Fixtures`: import BeamWash GDTF, patch the rest of the 22 per the QLC+ map (BW 302-306+301 @ 1/31/61/91/121/151, Pars @ 181-271+380; Beams @ 280/305/330/355 done).
3. Verify more QLC+ LOOKs render correctly; build a shutter shim if rig scenes stay dark (lesson 4).
4. Finish stage model details; backfill the survey's estimated items on the next FHC site visit.
