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

Console at FHC: **grandMA2** (PC software). The Hog screenshot Jefe shared is from Dave's touring rig, NOT FHC's house console — but Dave's Hog patch happens to describe the FHC rig (Dave's the FHC LD). When Jefe mixes at FHC he uses the house MA2. The `.show.gz` on /Volumes/TAXES/gma2/shows/ is a separate touring MA2 file (Lenny-PC origin, 2021–2024 venues) and is NOT the FHC patch — fresh XML export from FHC's console is the right source of truth.

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

### Physical Layout (verified at FHC during Boogaloo Assassins gig 2026-05-22)

Rig has **2 trusses**, not 3+3. There is NO "Back Truss":

- **Front Truss (FT)** — 4 positions across, SL → SR (pos 1 = far SL, pos 4 = far SR), plus a wider central span where multiple pars hang. Most downstage truss.
- **Rear Truss (RT)** — single truss split into 3 zones: **SL, Center, SR.** All upstage fixtures (RT BWs, M Beams, RT pars) live on the RT. Band sets up ~3 ft US of RT.

### Position Map (corrected and verified by aim-walking 2026-05-22)

**BeamWashes (6 × Rogue Outcast 1 BeamWash, 30CH):**

| Position | Fixture | DMX |
|---|---|---|
| RT SL | BW 302 | 1 |
| RT SR | BW 301 | 151 |
| FT pos 1 (far SL) | BW 303 | 31 |
| FT pos 2 (mid-SL) | BW 305 | 91 |
| FT pos 3 (mid-SR) | BW 304 | 61 |
| FT pos 4 (far SR) | BW 306 | 121 |

**M1 Beams (4 × Rogue Outcast 1M Beam, 25CH) — all on RT:**

| Position on RT | Fixture | DMX |
|---|---|---|
| RT SL | Beam 4 | 355 |
| RT Center (left half) | Beam 2 | 305 |
| RT Center (right half) | Beam 3 | 330 |
| RT SR | Beam 1 | 280 |

**Pars (12 × Chauvet COLORdash Par-H7X, 9CH):**

| Position | Aim US (band) | Aim Grass (audience) | Aim DS (front light) |
|---|---|---|---|
| RT SL | — | — | Par 201 (DS/audience) |
| RT Center | Par 204 ⚠ (the 1 US/band par) | — | Par 202 *(dead blue, DS)*, Par 203 ⚠ (DS) |
| FT SR (far) | Par 205 | — | — |
| FT 2nd from SR | Par 208, 209 | Par 206 | — |
| FT 2nd from SL | — | Par 210 | — |
| FT SL (far) | Par 211 | Par 212 *(dead)* | — |
| FT (location TBD — likely center span) | — | Par 207 | — |

**RT PARS CORRECTED 2026-06-19 (Jefe, at venue) — supersedes the old "203/204 both US" map.**
The RT truss has **3 pars pointed DS (at the audience) and 1 pointed US (at the band).**
Confirmed on site: **DMX 181 = Par 201 (RT-SL, DS); DMX 190 = Par 202 (RT-Center, DS, dead blue).**
So **3 DS = 201 + 202 + one of {203,204}; the remaining one of {203,204} is the lone US par.**
⚠ Which of 203/204 is US vs DS is NOT yet confirmed — placed as 203=DS, 204=US, swap on site.
**Mount X (Jefe on site 2026-06-19):** 201 sits between M1-SL & BW-SL (RT-SL); 202 between the two
center M1s; 203 between M1-SR & BW-SR (RT-**SR**, not center); 204 ≈ same X as 202 but aimed US.
(In the Blender model these are computed as midpoints of those neighbor fixtures — see place_pars.py.)

Working pars: 11. Dead: Par 202 (blue LED) and Par 212 (whole fixture).

### Patch order is NOT physical order

- BeamWash patch order 303→304→305→306 maps to physical positions 1, 3, 2, 4 (Dave wove the patch, not L→R).
- M Beam patch order 1→2→3→4 maps to physical SR, Center-SL, Center-SR, SL. Reverse weave.
- **Don't infer position from Hog/fixture number.** Always reference the position map above.

### QLC+ Files (built 2026-05-22)
Profiles built from official Chauvet DMX chart PDFs (Beam Wash Rev 5 V1.250121, 1M Beam Rev 1 V1.240515, Par-H7X Rev 1).
- `~/Library/Application Support/QLC+/Fixtures/Chauvet-Rogue-Outcast-1-Beam-Wash.qxf` — 30CH mode
- `~/Library/Application Support/QLC+/Fixtures/Chauvet-Rogue-Outcast-1M-Beam.qxf` — 25CH mode
- `~/Library/Application Support/QLC+/Fixtures/Chauvet-COLORdash-Par-H7X.qxf` — all 5 modes (6/8/9/13/14)
- `~/Documents/QLC+/Farmhouse-Collective.qxw` — 22 fixtures patched per Hog map, Universe 1
- Functions: 22 ID Scenes + Blackout, "Walk Fixtures" Chaser (single-shot, manual advance), VC Cue List widget (Space/Backspace/Esc)
- 6 Fixture Groups (placeholder roles, to be re-sorted post-walk)

### ENTTEC Binding (verified 2026-05-22 at home, pre-FHC)
- Device: ENTTEC DMX USB Pro Mk1, S/N 2340106, OS dev nodes `/dev/cu.usbserial-EN520106` + `/dev/tty.usbserial-EN520106`
- QLC+ Inputs/Outputs → Universe 1 Output → "DMX USB / 2: DMX USB PRO - DMX Output 1"
- Driver: **libFTDI** (NOT the macOS VCP driver — QLC+'s bundled libFTDI claims the device cleanly on M5 Pro / current macOS). No kext fiddling needed.
- Trick if device disappears: quit QLC+, replug ENTTEC, then launch QLC+ (order matters — QLC+ needs to claim the chip before the OS VCP driver locks it).

### QLC+ V4 Docs Source
Official docs live in the qlcplus GitHub repo at `resources/docs/html_en_EN/`. Fetch via raw GitHub URL:
`https://raw.githubusercontent.com/mcallegari/qlcplus/master/resources/docs/html_en_EN/<page>.html`

The qlcplus.org site is behind Cloudflare mod_security and blocks WebFetch. Use the repo path. Key pages:
- `mainwindow.html` — toolbar buttons and shortcuts (DMX Monitor = ⌘M, Address Tool, DMX Dump = ⌘D, Operate Mode toggle = ⌘F12)
- `fixturemonitor.html` — DMX Monitor (DMX view + 2D view)
- `dmxusbplugin.html` — ENTTEC USB Pro specifics
- `disable_apple_ftdi_driver.html` — macOS FTDI VCP workaround if libFTDI ever fails to claim
- `vccuelist.html` — Cue List widget schema
- `howto-input-output-mapping.html` — I/O binding tutorial
- Macros: the macOS app menu bar is **View** + standard Mac menus, not "Tools" — earlier sessions hallucinated a "Tools" menu.

(The detailed position map above this section supersedes earlier inline tables. Effective rig: **20 working fixtures** — Par 202 has dead blue LED but R/G/A/W/UV still work, Par 212 is fully dead.)

### Show-day design patterns (2026-05-22, Boogaloo Assassins + Jazz Quartet)

**Band-position-dependent aim strategy:**

- **Band mid-stage** (jazz quartet pattern, 3 ft US of RT):
  - FT BWs (303, 304, 305, 306) tilted moderately back toward US, **warm amber** wash on the band
  - RT BWs (301, 302) used as **cool kickers** aimed at mid-stage (different pan for each — one tilts forward, one tilts back depending on physical orientation; both converge on center)
  - M Beams: SL+SR pair cross-aimed onto opposite-side back walls (Beam 1 SR→SL wall, Beam 4 SL→SR wall) for **larger wall gobos via longer throw** AND avoidance of the LED screen; Center pair (Beam 2, Beam 3) on the grass as **audience gobo playground**.

- **Band pushed downstage** (vocalist pattern, expected to come up):
  - FT BWs need a **more acute angle** (less back-tilt, more straight-down) since the band is now closer to the FT
  - RT BWs become **kickers from behind with a different pan** — aim across stage rather than at center

**Color principle confirmed:** warm = FT, cool = RT. Cross-fixture color discipline lets you flip mood by reassigning fixtures rather than recoloring.

**Gobo behavior insight:** kids in the audience interact with gobo projection on the grass. Slow color/gobo cycles read as "magical" — fast cycles read as "strobe / nausea." For the 1M Beam: gobo scroll value **128** is slow (NOT 188 — the chart says 128→189 is slow→fast).

### Saved live-tuned scenes (in `~/Documents/QLC+/Farmhouse-WarmFrontCoolRear.qxw`)

- **Function ID 56 "WarmFrontCoolKickersForBeamWash"** — captured BW pan/tilt/color/zoom for mid-stage band. FT warm amber (BW 305 came out yellow R255 G255 — outlier in saturation), RT pure blue kickers at center stage.
- **Function ID 57 "Warm Front Spinning cool beamers"** — captured M Beam state: Beam 1+4 cross-aim onto back wall, Beam 2+3 grass gobos.

Use these as the canonical "live values" reference when rebuilding scenes for similar gigs.

### Lessons Learned at FHC (2026-05-22)

- **BLACK OUT button visual state is unreliable on macOS Sequoia.** The toolbar button toggles state but doesn't visually indicate on/off — Qt button styling issue. Verify state via DMX Monitor (`⌘M`) or build a VC Button with type "Toggle Blackout" which does render state correctly.
- **Functions don't fire by double-clicking in V4 Operate Mode.** They must be triggered via VC widgets (Button, Slider, Cue List), MIDI/OSC, or scripts. Function Manager is authoring-only.
- **VC Button XML schema** that works in QLC+ 4.14.4 (script-injection had wrong format earlier):
  ```xml
  <Button Caption="Name" ID="N" Icon="">
    <WindowState Visible="True" X="..." Y="..." Width="..." Height="..."/>
    <Appearance>...</Appearance>
    <Function ID="N"/>
    <Action>Toggle</Action>
    <Key>B</Key>
    <Intensity Adjust="False">100</Intensity>
  </Button>
  ```
  The `Icon=""` and `<Intensity Adjust="False">100</Intensity>` are required. Building via UI (right-click VC canvas → Add → Button → Properties) is more reliable than authoring XML.
- **Cue List widget Steps got stripped** during QLC+ 4.14.4 load-and-resave of the workspace built in 4.13.0. The "Walk Fixtures" chaser had 22 steps in source XML but loaded empty.
- **BeamWash needs all of: dimmer + color channel + strobe in "On" range (20-24).** Pushing just the dimmer ≠ light. Earlier successful single-channel tests worked because residual color values lingered from Dave's prior show; once reset, dimmer-only produced no light.
- **ENTTEC unplug/replug mid-session drops the output binding silently.** Re-tick the Output checkbox in Inputs/Outputs after any replug. Don't unplug during a session — treat ENTTEC like an audio interface (connect once, leave it).
- **Simple Desk page indicator** = number between the orange arrows in top-left. Each page = 32 channels. Page N starts at channel (N-1)*32 + 1.
- **Network: dedicated bridge router needed for FHC pattern.** Jefe's Linksys was in router mode (NAT) by default, putting iPad on 192.168.1.x while M32C lived on venue's 10.20.0.x — couldn't reach. Bridge Mode fixed it. Future: kit a separate AP-only router (GL.iNet Slate AX recommended ~$80) for venues where venue provides DHCP, keep main router for UI24R / TF3 gigs.

### Scenes Built 2026-05-22
- **"All Blue (3s fade)"** — Function ID 24 in Farmhouse-Collective.qxw. Fade In/Out = 3000ms. All 22 fixtures asserted blue (BW: R=0,G=0,B=255,W=0, dimmers 255, strobes 22; Beam: R=0,G=0,B=255, dim 255, strobe 5; Par: R=0,G=0,B=255, dim 255). VC Button "All Blue" wired to it (keyboard shortcut B).

### Open Items
- [ ] Verify Par 202 (DMX 190) — push ch 190 + 192 in Simple Desk; if no light, second dead fixture
- [ ] Visually confirm Par 212 dead at FT SL Grass position (vs. just unpatched)
- [ ] Build palette scenes: color palettes (red/amber/blue/warm white/CTO), position palettes (lead center, drum riser, SR/SL), beam palettes (zoom narrow/wide, strobe)
- [ ] Build look scenes for Boogaloo Assassins set (intro/verse/chorus/solo)
- [ ] Re-populate Fixture Groups (QLC+ 4.14.4 stripped Heads on save — currently empty placeholders)
- [ ] Rebuild "Walk Fixtures" Chaser steps if needed (lost in 4.13→4.14 migration)
- [ ] Export MA2 patch — likely not needed since physical walk done, but a fresh XML export of just the LED PARS + Beam Wash layers would cross-verify

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
