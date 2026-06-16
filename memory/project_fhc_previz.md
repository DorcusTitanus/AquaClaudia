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

## MILESTONE — geometry → QLC+ look generation PROVEN (2026-06-12)

The "Cool" scene (QLC+ fn900, wired to MOODS-row 5th button ch188) aims all 10 movers at a
chosen point (stage-center pivot 0,-3.0125,0.27) using values COMPUTED FROM THE BLENDER MODEL,
not hand-aimed. Renders correctly through QLC+→Art-Net→BlenderDMX: ten distinct-colored cones
converge on the target. This is step 2 of [[project-video-dj-lightshow]] (agent-generated QLC+
looks) working end-to-end. Backup: `Farmhouse-Busking-Base.qxw.bak-cool`.

**Parallax test PASSED 2026-06-12:** three geometry-generated converging looks at distinct
stage depths — **Center** (fn900, ch188, 0,-3.0125,0.27), **Downstage** (fn901, ch189, front
edge 0,-6.0275,0.27), **Upstage** (fn902, ch190, back edge 0,0.0025,0.27) — each a tight point,
same per-fixture colors. Drop/Peak VC labels converted to real Buttons. Dead-on achieved by (a)
aiming from the HEAD tilt-pivot (head origin = rotation center; beam direction then exact) and
(b) **16-bit pan/tilt** (set fine channels ch1/ch3 — 8-bit alone = ~2° pan ≈ 18cm scatter, the
real residual). Generator proven across the whole stage volume, not just center.

**NEXT — real-rig photo validation (venue):** run Center/Downstage/Upstage on the actual rig,
photograph (ideally overhead/FOH to match Blender top-ortho), compare convergence to the model.
If real ≠ virtual, the per-fixture COLOR identifies which fixture's modeled position/orientation
is off. This is the calibration-validation that feeds the [[project-video-dj-lightshow]] ruler-photo step.

**CALIBRATION COMPLETE 2026-06-12 — model matches rig, convergence confirmed on all 3 aims.**
- **Both trusses were built too LOW** (deck-offset dropped at placement). RT ~0.46 m low, FT ~0.18 m
  low. Fixed by raising each truss + its fixtures as a UNIT. Now: RT bottom 4.460 grade = 3.915 above
  deck (real, confirms survey 3.921); FT bottom 5.513 grade = 4.973 above deck.
- **The Z error was the real culprit masquerading as an X problem** — fixtures 0.5 m low make
  correctly-angled beams land wrong, which reads as wide/off X. **Fix truss height FIRST, always.**
- **Rear X finalized:** inner beams Beam2 (+0.44) / Beam3 (−0.62) solved clean & confirmed; Beam1/Beam4
  walked inward to visual convergence; BW301/BW302 set from a laser-marked outer EDGE, width-corrected
  to center (Jefe). Verified converging on Center + Downstage + Upstage, top AND front view.
- **Outer-fixture residual = pan curve** (GDTF DMX→degrees drift at large pan), NOT position. Tell:
  inner fixtures solve consistently across the 3 marks, outer ones scatter ~1 m. Left as optional
  polish (a pan scale/center tweak in the GDTF profile) — not a model bug.
- **Takeaways:** (1) trusses lock fixtures in Y/Z — solver per-fixture Y/Z scatter is NOISE, reject it
  ([[feedback-respect-physical-constraints]]). (2) Direct laser measurement beats derived triangulation
  every time — survey's FT-bottom number was wrong by ~0.65 m; a direct grade shot caught it. (3)
  Pan-angle error ≠ position error; outer-scatter-with-inner-consistency is the signature.

**REAL-RIG VALIDATION 2026-06-12:** ran Center/Downstage/Upstage on the actual FHC rig (USA-Paraguay
crowd), photographed all three — convergence "pretty close," predicted colors landed where the model
said. Pipeline validated against reality, not just Blender. **One confirmed model error: the two
CENTER beams are address-swapped.** Physical M1 order SR→SL = Beam1(280), **Beam3(330), Beam2(305)**,
Beam4(355) — Jefe: "B02/B03 swapped from the get-go." In the Blender model, the center fixture at
X≈-0.6 holds addr 305 but should be 330; X≈+0.66 holds 330 but should be 305. **Fix = swap those two
fixtures' DMX addresses in BlenderDMX** (then regenerate scenes), OR swap addr 305↔330's pan/tilt in
fn900/901/902 to correct the live scenes without touching the model. Outer beams (280/355) are correct.

**REVERSE CALIBRATION 2026-06-12 (rig aim → true fixture positions):** Jefe aimed all 10 movers
at 3 known marks on the rig, dumped as QLC+ scenes **"AIM Center" (fn13), "AIM Downstage" (fn14),
"AIM Upstage" (fn15)**. Triangulated each fixture from its 3 rays (least-squares line intersection;
marks = the 3 target points). Findings to apply in Blender:
- **Center beams swapped (confirmed + quantified):** Beam2(305) true X≈**+0.35** (SL-ctr), Beam3(330)
  true X≈**−0.73** (SR-ctr). Model had −0.60 / +0.66 → swap their positions.
- **All other fixtures' X within ±0.2 m** — "mostly X" hypothesis holds once the swap is fixed.
- **Uniform Y offset ≈ −0.38 m on ALL 10** = the physical marks sat ~0.38 m downstage of the
  assumed targets (global registration, NOT per-fixture; doesn't affect convergence).
- **Z noisy:** beams +0.4 high, front washes 0.5–1.1 m LOW → re-check front-truss height (already
  flagged uncertain). Front washes are far from marks so their solve is the least reliable.
- Triangulation needs ≥2 marks per fixture; 3 gives redundancy. Method: P = (Σ(I−dᵢdᵢᵀ))⁻¹·Σ(I−dᵢdᵢᵀ)Mᵢ.

**LASER CALIBRATION 2026-06-12 (final, clean): only X needs fixing, mostly the 2 center beams.**
Method (laser dot invisible on black fixtures in daylight → measure the SPOTS, not the fixtures):
beams converged on 3 matte marks (outlet plate, 2 job boxes), each located by laser distance from
the two FRONT deck corners (back corners blocked by amp room/lift). Marks trilaterated (deck
9.635×6.025): downstage box Y=−5.84, center box Y=−3.33, plate Y=+0.02, all X≈0±0.2. Then
triangulated all 10 fixtures from their AIM dumps' rays to the 3 known marks.
- **Center beams confirmed swapped, exact true X: Beam3(330) X=−0.62 (SR-ctr), Beam2(305) X=+0.44
  (SL-ctr).** Model had +0.66/−0.60 → in Blender move Beam2→+0.44, Beam3→−0.62.
- **BW301(151) X=−5.77** (model −5.48; 0.3 nudge). All 7 others within ±0.1 m — eyeballed X was good.
- Y/Z: uniform ~−0.35 m Y offset on all (registration, not per-fixture; ignore for convergence).
  Z within tenths except front washes (noisiest; would sharpen with real job-box heights, but X
  didn't need it). **Bottom line: swap the 2 center beams to exact X, nudge BW301, regenerate, done.**
- **CALIBRATION OUTPUT IS X-ONLY. Y/Z are truss constants — DO NOT apply per-fixture.** The
  per-fixture Y/Z scatter from triangulation is noise; fixtures on a rigid truss share one Y and
  one Z. (Learned the hard way 2026-06-12 — a script that moved fixtures to raw X/Y/Z floated them
  off the trusses. See [[feedback-respect-physical-constraints]].) If the whole rig looks Y-offset,
  move the TRUSS as a unit, never the fixtures. Convergence won't be mathematically perfect (rig
  slop) and that's correct — don't break physics to chase it.

**Reusable method (geometry → aim → QLC+ scene):**
1. Read fixture data in Blender: `bpy.context.scene.dmx.fixtures`; per fixture
   `fx.dmx_breaks[0].address`/`.universe` (NOT fx.address — that's absent), and head pivot via
   `fx.get_object_by_geometry_name("Head").matrix_world.translation`. Aim from the HEAD, not Base
   (Base is ~0.35 m high → only "pretty close"). Print to a FILE (macOS Blender has no console).
2. Compute per fixture: az = atan2(dy,dx); θ (from straight-down) = acos(-dz/|D|).
   **tilt8 = (0.5 − θ/260)·255** (DMX 127 = straight down, ±130° range — solid, validated).
   **pan8 = 128 + PAN_SIGN·((A_REF − az)/(540/256))**; A_REF=0, PAN_SIGN=+1 landed it (these
   fixtures, base RotZ 90 + RevPan). Pan only depends on X,Y (heads sit directly below bases).
3. Write a `<Function ID Type="Scene" Name>` with `<FixtureVal ID="qlcID">ch,val,…</FixtureVal>`
   (channels are FIXTURE-RELATIVE 0-indexed; map addr→QLC+ internal ID via the patch). Insert
   before `</Engine>`/Monitor; repoint a VC `<Button>`'s `<Function ID/>` to it. QLC+ must be
   CLOSED or reloaded (File→Open, discard) to pick up disk edits.
4. **Wash (Rogue Outcast BeamWash) color tips (Jefe):** use CENTER dimmer only (rel ch16 / DMX17),
   NOT background(ch13) or ring(ch14) dimmers — cleaner cone. Zero the White (ch28) and lower
   intensity (~180) for higher saturation. Beams (1M) = RGB rel ch10/12/14, dimmer ch7, shutter
   ch9=190 (190 renders open; rig's 5 reads closed in BlenderDMX). Distinct hue per fixture makes
   convergence legible (which cone is which).
- Residual scatter = yoke-arm offset (head swings off the pan axis when panning); point-aiming
  can't fully kill it. For dead-on, implement proper moving-head IK (account for arm length).

## Build method: bpy scripting (mouse-free, reproducible)

Model geometry is built via Python scripts pasted into Blender's Text Editor (Scripting tab →
Text → Open → Run / Alt+P) — no 3-button mouse needed, exact survey dimensions, re-runnable.
Seed script: `FHC_lightRig/blockout_venue.py` (self-calibrates off Stage_Platform / LED_Screen /
05_Roof_Pergola bboxes; builds ground, back wall, amp room, lift, pergola roof into
07_Venue_Blockout). **The deterministic structural shell is ~fully scriptable from the survey;
the empirical stuff (fixtures, DMX link, pan reversal) is NOT — that needs the real rig.** Open
offer: consolidate into one parameterized `build_venue.py` (survey constants in a CONFIG block,
regenerates whole shell) so unmeasured values (fixture X spacing, pergola depth, truss lengths)
become a re-run, not a re-nudge.

## Re-patching fixtures to a different profile (in-place — confirmed method)

To swap a placed fixture's GDTF (e.g. original → RevPan) WITHOUT delete/re-add:
**Fixtures → Edit → Advanced Edit** → swap the Profile, and set the base **Z
rotation 270° → 90°**. Preserves the fixture in place. (NOTE: the 270→90 base flip
that Jefe found necessary alongside the RevPan swap — verify the net result with an
ASYMMETRIC look, since base-flip + pan-reverse is the double-correction pattern that
reads correct in symmetric looks but can be wrong in asymmetric ones. Confirm before trusting.)

## Truss structure (observed 2026-06-11 from the ground, venue packed — ESTIMATED, re-measure when empty)

Triangle (bay) count per piece, at Jefe's ~2 ft/triangle heuristic — every piece lands
on a STANDARD truss length, which corroborates the read:
- **Rear truss:** 3 pieces, 3 triangles each → **6 ft** each (matches the model's "6ft"
  naming; ~18 ft total rear span — survey had NO rear span number, this is the first).
- **Front speaker trusses (FT_SL/FT_SR):** 2 triangles → **4 ft** each.
- **Front center truss:** 3 linked pieces — outer two = 4 triangles → **8 ft**, inner =
  5 triangles → **10 ft**. Center total = 8+10+8 = **26 ft** (NOT Jefe's eyeballed 18 —
  trust the triangle count). Inline with speakers = ~34 ft of truss within the surveyed
  **FT post-to-post 12.178 m (~40 ft)**; the ~6 ft balance = posts + connector gaps.
- **Front is ~2× the rear span** (≈34–40 ft vs ~18 ft) — quantifies "front much longer."
- **Model fix:** Blender front truss is named "6ft" but should be 4/8/10/8/4 ft. Rear 6ft is correct. Cosmetic for previz (fixtures hang at measured X regardless).

**PERGOLA ROOF PITCH ≈ 18.5–19° (X rot, front-of-house edge HIGH, back edge low)** — confirmed
2026-06-12 (Jefe trial-and-error −18.5° vs model; corroborated by survey amp-closet ceiling
4.736→4.096 over 1.868 m = 18.9°). The roof is NOT flat — survey's "flat pergola" wording is
wrong; amp-closet ceiling and main pergola are the same continuous ~19° plane.
- **Do NOT derive roof pitch from truss heights** — the trusses hang on ~1 m of chain so they
  sit far more level than the roof (this is the trap that gave a wrong 5.75°). Use the amp-closet
  ceiling slope.
- **Resolves survey Observation #1** (FT joist-holder ~1.07 m gap): a 19° roof drops ~1.35 m over
  the 3.941 m truss span; trusses drop only 0.40 m → ~0.95 m differential chain ≈ the flagged gap.
  The roof pitch and the chain hang explain each other. (Fold this into reference_fhc_stage_geometry.md
  roof section + Observation #1 next time that doc is edited.)

**SIGNAGE LANDMARK (key for fixture X without a tape):** the 4 front-truss BWs line up
with the **F, M, O, E** of the "FARM HOUSE" fascia sign. Get the sign's letter spacing
(already on the survey still-needed list) → it pins the 4 front BW X positions proportionally.

## FHC_Stage.blend model reference coordinates (fixture placement)

The model's vertical datum is NOT the stage deck — do not derive fixture Z from survey
numbers. Use these placed-fixture references (audience = −Y, SL = +X, all fixture
bases Rot Z = −90°):
- **Front truss hang (from BW303 as placed):** Y = −7.3434, Z = 5.3285
- **Rear truss hang (from the M1 beams as placed):** Y = −3.4986, Z ≈ 4.0046
- X positions are eyeballed placeholders until the venue X-spacing measure.

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
- **RESOLVED → outer beam addressing was mirrored, not X positions.** Debugged 2026-06-09 (late session): bases/tilt/pan-direction all verified correct; the Back Wall + Grass looks only render correctly with the outer fixtures' addresses swapped. **Working conclusion (VERIFY AT VENUE): Beam 1 (DMX 280) hangs SL, Beam 4 (DMX 355) hangs SR** — opposite of the busking doc's walked map. Center pair (Beam 2/3) unchanged. Blender now has the swapped addressing and renders the live-dumped looks correctly, which makes the dumps themselves the evidence. Next FHC visit: wiggle pan on address 280 and eyeball which end of the truss moves; then finalize both memory docs and re-label any SL/SR-named QLC+ groups/scenes (labels may be mirrored; fixture IDs and dump values are ground truth and need no change).
- **BlenderDMX render limits observed (Z-Rendered/Eevee)**: gobo and prism do NOT render — only aim + beam color. Frost doesn't change spot size either. Aim + color + dimmer is the usable previz vocabulary for now; gobo/prism/frost looks must still be dialed at the venue. (Worth checking later whether Cycles or BlenderDMX settings add gobo support — unverified.)
- Fixture base alignment workflow: Blender's Align Objects operator CRASHES on BlenderDMX fixtures (bases are Empties — `UnboundLocalError: center_active_z`, Blender 5.1.2 bug with Relative To: Active). Use N-panel right-click → Copy to Selected on Location Y/Z + Rotation Z instead, then set X per fixture.

## Next steps

**TOMORROW'S HARD SCOPE (2026-06-10 brain dump): PLANES ONLY.** Roof, back wall, doors, amp
room as BLOCKS. Shingle texture only if one-click. **Deliverable = a calibrated scene where all
movers point to the same XYZ point**, then photograph the real rig hitting that point WITH A
RULER in frame = the alignment fiducial. This calibration reference is step 1 of the
[[project-video-dj-lightshow]] pipeline — do NOT scope-creep past it. Still genuinely pending:
per-fixture truss X measurements (venue was packed 6/11, couldn't measure) + finish patching Pars.

**RevPan DONE 2026-06-11:** all 10 movers swapped to RevPan profiles in Blender, pan tracks
reality. Chirality saga below is resolved history.

**CHIRALITY SOLVED 2026-06-11 (at venue, rig + Blender side by side):** the outer movers' "mirrored" aim is **physical RevPan (pan-invert) on the real rig's outer fixtures**, NOT a model error. Proof: BW302 rotates CW on +pan on stage but CCW in Blender; center movers matched. A global GDTF error would reverse ALL fixtures — only outers flipped → per-fixture physical setting. **This supersedes the Beam01↔Beam04 address swap** (that was a coincidental fix that only works on mirror-symmetric looks like Backwall; UNDO it, restore Beam1=280…Beam4=355, apply RevPan instead).
- **Fix built:** reverse-pan GDTF variants in `~/Downloads/` — `...Rogue_Outcast_1M_Beam_RevPan...gdtf` and `...Rogue_Outcast_1_BeamWash_RevPan...gdtf` (pan PhysicalFrom/To negated, distinct Name+GUID). Import both; re-patch only the inverted fixtures with the RevPan profile. (Check first whether BlenderDMX has a per-fixture pan-invert toggle — cleaner if so.)
- **SOLVED 2026-06-11 (venue, all 10 movers tested rig-vs-Blender): Blender pan is GLOBALLY reversed on EVERY fixture** — all 6 BWs and all 4 Beams rotate CW on +pan on stage, CCW in Blender. Not placement, not per-fixture, not a mirror — one global pan-direction bug (GDTF pan convention opposite the real Chauvet hardware). Tilt is correct (tilt 75 → grass on both). This explains the entire saga: outer "off the back" = reversed pan worst at large angles; "mirrored" looks = reversed pan in symmetric scenes; the Beam01↔Beam04 address swap "fixing" Backwall = coincidental compensation in a symmetric look. **Original build was correct — placement, SL/SR, addressing all fine; model just panned backwards.**
- **FIX:** (1) re-patch the 6 BWs with the RevPan BeamWash profile + the 4 Beams with the RevPan Beam profile (both in ~/Downloads, pan negated). Pars don't pan. (2) UNDO the Beam01↔Beam04 address swap — restore Beam1=280/Beam2=305/Beam3=330/Beam4=355. (3) Verify with an ASYMMETRIC look (not Backwall) to confirm pan tracks everywhere. The busking-doc SL/SR "correction pending" note (2026-06-09) can be reverted — the map was right; reversed pan caused the apparent mirror.

1. **VENUE ALIGNMENT SESSION (supersedes piecemeal checks): bring the Blender model TO the venue** and align it against the real rig fixture-by-fixture. Open question to settle there: model chirality vs pan direction. Status 2026-06-09: outer BWs and outer M1s have both shown 180°-ish/mirrored aim discrepancies; Jefe's cold memories conflict with the previz-derived beam-address swap (he's confident ch1 BW = SL and Beam1=SR per original map; doesn't recall any fixture with inverted pan). Possible causes: (a) fixtures placed on mirrored X (SL = screen RIGHT in front view — classic trap), (b) GDTF pan direction opposite hardware (fix = flip Pan PhysicalFrom/To sign in the GDTF and re-import; rotating bases CANNOT fix a reflection). The beam address swap currently in the .blend may be compensation — re-derive it live. While there: wiggle DMX 1 (BW302) and DMX 280 (Beam1), watch which end moves; **measure per-fixture truss X positions for all 22 fixtures**; verify BW truss order.
2. Finish populating `04_Fixtures`: import BeamWash GDTF, patch the rest of the 22 per the QLC+ map (BW 302-306+301 @ 1/31/61/91/121/151, Pars @ 181-271+380; Beams @ 280/305/330/355 done).
3. Verify more QLC+ LOOKs render correctly; build a shutter shim if rig scenes stay dark (lesson 4).
4. Finish stage model details; backfill the survey's estimated items on the next FHC site visit.
