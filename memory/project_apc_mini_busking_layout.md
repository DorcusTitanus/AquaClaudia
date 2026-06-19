---
name: project-apc-mini-busking-layout
description: "APC mini mk2 → QLC+ busking layout for FHC. Universal moods (vetoed per-event mood pages). No on-device paging. QLC+ Channel formula and APC palette confirmed. In-progress mapping to grid + side + fader bindings."
metadata:
  type: project
---

# APC mini mk2 Busking Layout

Started design 2026-05-27 on alia. Hardware arrived and started programming 2026-05-29 on shai-hulud. This is the reconciled doc.

**Why:** Busking the FHC rig from a laptop trackpad in low light during a set is fragile. The mk2's 8×8 RGB grid + 9 faders gives positional muscle memory and visual state feedback. Pre-deciding the row/column language and proving out the QLC+ wiring now lets future sessions just author scenes, not debug protocol.

**How to apply:** When wiring buttons or scenes for QLC+ at FHC, conform to the row/column framework below. Use the **established QLC+ Channel formula** (note + 128) and the bundled **"Akai APC Mini mk2" input profile** which exposes the full named palette.

Related: [[project-dmx-lighting]] (rig + venues), [[project-ai-lighting-controller]] (north star).

---

## Hardware confirmed (mk2)

**Akai APC mini mk2** — RGB pads on the 8×8 grid AND on the 8 scene-launch + 8 track-select side buttons. Shift button is single-color red. 9 faders (8 + master), not motorized. macOS class-compliant USB MIDI — no driver install.

## Decisions locked

1. **Universal moods, not per-event moods.** Jefe vetoed per-genre mood banks. 8 universal moods that overlay any event/genre. Genre context handled via per-venue workspace files, not per-page mood sets. Suggested 8 moods organized by song-arc dynamics (Whisper / Calm / Building / Groove / Lift / Drop / Peak / Reset).
2. **No on-device paging.** The mk2 has no page memory (unlike Stream Deck). Software-paging in QLC+ adds engineering complexity for marginal gain. Use **multiple workspace files** per venue/rig (FHC, Lake Arrowhead, etc.). For mid-show flexibility, use the bottom row track buttons as **modifier toggles** (audience-engagement mode, kick-sync mode, evens-only/odds-only, snap-vs-fade, tap-tempo).
3. **No dedicated strobe channel.** Fixtures sit at 85–90% intensity baseline. A momentary pad adds the remaining 10–15% as a manual flash. Released pad = baseline.
4. **Per-venue workspaces.** FHC and Lake Arrowhead are separate `.qxw` files. No shared workspace, no auto-switching.
5. **Akai mk2 Input Profile is bundled with QLC+ V4.** Must be assigned to the universe before any feedback works.

## Layout — "rows are verbs, columns are objects"

**Main 8×8 grid:**

| Row (top→bottom) | Function | 8 columns |
|---|---|---|
| 1 (top) | **MOODS** | 8 universal moods: Whisper / Calm / Building / Groove / Lift / Drop / Peak / Reset |
| 2 | **COLOR PALETTE** | Red / Amber / Warm White / Cool White / Blue / Magenta / UV / +15% Intensity Bump (momentary) |
| 3 | **POSITION PALETTE** | Band Ctr / Lead Vox / Drum Riser / SR / SL / Grass DS / Grass US / Back Wall |
| 4 | **BEAM PALETTE** | Narrow / Wide / Slow Spin CW / Slow Spin CCW / Gobo A / Gobo B / Open / Reset |
| 5 | **GROUPS** | FT BWs / RT BWs / All BWs / M Beams Ctr / M Beams SL+SR / All Beams / Pars Band / Pars Grass |
| 6 | **INDIV BWs** | BW 301, 302, 303, 304, 305, 306, spare, spare |
| 7 | **INDIV M BEAMS + key Pars** | Beam 1, 2, 3, 4, Par 201, 205, 210, 211 (Shift → second par bank) |
| 8 (bottom) | **TEMPO / ACCENT / MODIFIERS** | Tap Tempo, Strobe Bump, Blackout, Audience-engage, Kick-sync, Odds, Evens, Snap |

**Right column (8 scene-launch) — Mood Slot Bank B.** Second bank of 8 moods (color/intensity variants — e.g. Whisper Red/Cyan/Amber/UV in the top 4; strobe/blackout/reset/panic in bottom 4).

**Bottom row (8 track-select):** **modifier toggles** per row 8 above. Hardware labels are VOLUME/PAN/SEND/DEVICE/▲/▼/◀/▶ but we override with our own meaning.

**Shift (bottom-right):** momentary blackout-with-hold. Modifier for alt assignments.

## QLC+ Channel formula (CRITICAL — discovered empirically 2026-05-29)

```
QLC+ Channel = MIDI_note + 128
```

So QLC+ Channel 128 = MIDI note 0, Channel 255 = MIDI note 127. For CC messages the offset differs (probably +256 or similar); we mapped only Note On so far.

## APC mini mk2 MIDI map (auto-detected and confirmed on shai-hulud)

| Region | MIDI note range | QLC+ Channel range | Profile labels |
|---|---|---|---|
| 8×8 grid pads | 0–63 | **128–191** | "Button R-C" where R=1 (bottom) → 8 (top), **C=1 (right) → 8 (left)** (columns counted from right!) |
| Scene-launch (right col, top→bottom) | likely 111–118 | **239–246** | "Soft Keys" (top), "Clip stop", "Solo", "Mute", "Rec Arm", "Select", "Drum", "Note", "Stop All Clips" |
| Track-select (bottom row, left→right) | 100–107 | **228–235** | "Volume", "Pan", "Send", "Device", "▲", "▼", "◀", "▶" |
| Shift (bottom-right) | 122 or 123 | **250 or 251** | "Shift Button" |

**Confirmed data points:**
- TRK100 (my code's first track button) → Channel 229 = "Volume Button" ✓ leftmost track button
- SHIFT button → Channel 251 = "Shift Button" ✓
- SCN112 (my code's first scene-launch) → Channel 241 = "Clip stop Button" — this is the **second** scene-launch button. The top one ("Soft Keys") is likely note 111 = Channel 239.
- P63 (my top-right code label) → Channel 192 = "Button 8-1" — per profile, row 8 (top), col 1 (counting from right) = top-right
- P0 (my bottom-left code label) → Channel 129 = "Button 1-8" — row 1 (bottom), col 8 (counting from right) = bottom-left

**Column orientation in profile:** Column 1 = RIGHTMOST, Column 8 = LEFTMOST. Different from my initial assumption. Doesn't break my code (the channel numbers still flow left-to-right correctly because of how the underlying notes are arranged), but worth knowing for profile label interpretation.

## MIDI VERIFIED LIVE (2026-06-18, direct CoreMIDI sniff on M5)

Sniffed the actual hardware with a tiny Swift CoreMIDI listener (`FHC_lightRig/midimon.swift`,
`swiftc -o midimon midimon.swift; ./midimon <seconds>` — logs port-tagged decoded MIDI to
`/tmp/midi.log`; multiple clients can read a CoreMIDI source, so it works even with QLC+ open).
This SUPERSEDES guesses above where they conflict.

- **Two ports exposed:** `APC mini mk2 Control` and `APC mini mk2 Notes`.
  **In Session mode (our busking mode) EVERYTHING rides on the `Control` port, MIDI ch1** —
  grid, Shift, scene-launch, track row, AND faders. The `Notes` port is unused in Session mode
  (it's for the musical Note mode). **→ Wire QLC+ to the single `Control` input line.**
- **Confirmed note/CC map (ch1, Control port):**
  - 8×8 grid pads = **notes 0–63** (top-left = 56, bottom-right = 7) → QLC+ ch 128–191 (note+128 ✓)
  - Scene-launch (right col, top→bottom) = **notes 112–119** → QLC+ ch 240–247
  - Track row (bottom, L→R) = **notes 100–107** → QLC+ ch 228–235
  - **Shift = note 122** → QLC+ ch 250
  - **Faders 1–8 = CC 48–55** (master fader presumably CC 56), all ch1
- **SHIFT IS A CLEAN, BINDABLE NOTE (note 122, momentary NoteOn/NoteOff).** Earlier worry that
  Shift can't be used for paging is WRONG for this firmware/port — a plain Shift tap sends only
  note 122 with no side effects. **→ Page via Shift → "Next Page" on a multipage VC frame**, which
  keeps the clean 0–63 grid identical on every page. (SysEx only fires when Shift is HELD to change
  pad modes.)
- **Drum mode = MIDI channel 10** (not ch7 as a doc claimed): same pads, ch10, remapped notes
  (mk2 Drum is a 4-quadrant Ableton drum-rack layout, notes 36–99, per the mk2 manual).

### DRUM-MODE PAGE-2 = DEAD END (2026-06-19, do NOT re-attempt at a gig)
Spent a whole venue session trying to use Drum mode (ch10) as a second 64-pad layer so page 2
would work on the controller. It does NOT work with QLC+ + this controller, for layered reasons:
- **QLC+'s bundled "Akai APC Mini mk2" input profile only maps the Session-mode (ch1) pads.** It has
  no entries for Drum/Note-mode pads, so QLC+ never recognizes those presses (auto-detect sees
  nothing; only Shift, which IS in the profile, registers).
- **The MIDI plugin input is filtered to MIDI Channel = 1**, so ch10 (Drum) is dropped regardless.
- **The Init Message was set to the mk1 ("APC mini Ableton mode 2")**, not "APC mini mk2 Ableton
  mode 2". Fixing it + relaunch did NOT help.
- The profile **wizard** would not capture Drum/Note pads either.
- Even when channels were forced, a deeper killer remains: **QLC+ multipage delivers a shared input
  channel to only ONE widget**, so page-1 and page-2 buttons can't share the same pad note anyway —
  AND the page-flip-must-track-pad-mode sync is too fragile to trust live.
**Resolution (ship this):** page 1 = full pad-driven grid; **page 2 = MOUSE-ONLY long tail**; put the
handful of must-trigger page-2 functions on the **16 side pads** (scene-launch ch240–247 + track
ch228–235 — unique channels, always live, no mode switching). That's the reliable busking surface.

## FHC BUSKING VC — gig-ready (`~/Documents/QLC+/Farmhouse-Busking-mk2.qxw`, 2026-06-19)

Built the real Session-mode VC this session. Generators: `build_mk2_vc.py` (+ earlier
`build_busking_vc.py`). State:
- **8×8 grid, 2 pages** in a multipage Frame; **Shift (ch250) flips pages** (Previous + PagesLoop).
  Page 1 = performance (specials/M1 colors/M1 pos/M1 beam/BW colors/BW pos/par colors/EFX). Page 2 =
  detail (mouse-only — see dead-end above).
- **9 faders wired** (L→R): M1 int · FT BW int · RT BW int · FT-Pars int · BW zoom · BW ring-macro
  (ch9 select) · BW ring-speed (ch10) · **EFX-speed Speed Dial (CC55, range 30s→0.5s)** · Master.
- **Speed Dial** drives ALL 5 EFX (multiple `<Function>` children); AbsoluteValue input on the fader.
- 4 par **chases** built (Chaser + step-scenes): Red/Green base + white runner, White base + red/green
  runner, over audience pars, dead-212 as a gap.

### Hard-won rig/QLC+ fixes from this session (all real, all verified on the rig)
- **BW intensity faders must drive BOTH ring dimmer (ch14) AND center dimmer (ch16).** The visible
  "wash" is the RING; center-only (ch16) barely registers — symptom was "only RT-SL lights" until we
  diffed a working dump and found ch14=0 vs 255. Faders 2/3 now own ch14+ch16.
- **Shutter open = 255 for M1 (ch9) and ~22 for BW (ch18/19).** Rig opens M1 at 5 but BlenderDMX only
  renders open at 216+; 255 opens BOTH. Additive color scenes now open the shutter themselves so a
  color button + intensity fader = light, no base scene needed.
- **Blackout: use the "Toggle Blackout" button ACTION, not a dimmers-to-0 scene.** Level faders hold
  the dimmers up, so a scene can't pull them down. (Same reason any all-dimmers-0 scene won't black.)
- **Par aim IDs confirmed at venue:** DMX 181 = Par 201 (RT-SL, aims DS/audience); DMX 190 = Par 202
  (RT-Center, DS, dead blue LED). Audience-facing set = 201,202,+one of 203/204 (RT) + 206,207,210
  (FT DS) + dead 212. RT-Center has only 2 pars; 3rd RT-DS par (203 or 204) still to confirm on site.
- Sniffer tool: `FHC_lightRig/midimon.swift` (CoreMIDI, port-tagged) — reusable for any controller.

### ROADMAP (next QLC+ sessions, per Jefe 2026-06-19)
- Move the 4 chases to **page 1** (they read well).
- Top row → real **base looks with descriptions** (full scenes: color theory + light positions).
- Use **front BW positions to make spotlight specials** (their tight focus is the point).
- **Complex moving EFX**: shutter blacks out part of the motion → reads as a *sweep*; fan the FT BW
  out at the audience with a **color chase stacked on top**.
- **Gobos + prisms (M1) can't be previz'd in Blender** — dial those in at a **night venue session**.

## QLC+ VC widget XML schemas (status 2026-06-18)

- **Level slider channels (confident, from the live .qxw):** `<Level LowLimit="0" HighLimit="255"
  Value="0"><Channel Fixture="ID">chIndex</Channel>...</Level>` — channel index is element TEXT,
  fixture ID is the `Fixture` attr. (NOTE: QLC+ source-via-LLM mis-reported the attrs as
  `LevelLowLimit` etc. — the real serialized names are `LowLimit/HighLimit/Value`. Trust the live
  file, not the summary.)
- **Multipage frame / Solo frame / Speed Dial:** NOT yet verified against a real save. LLM
  reconstruction of the source was unreliable (wrong serialized names). Before authoring these into
  the show file, get one real example of each from the QLC+ UI (create + save), then read it back —
  the proven pattern that has never broken the file.

## QLC+ Custom Feedback structure

The **Custom Feedback Configuration** dialog (per VC widget) has:

**Left pane — Values:**
- **Lower Value** (0–255): MIDI velocity sent when button is in OFF state (idle).
- **Upper Value** (0–255): velocity sent when button is in ON state (pressed/toggled active).
- **Monitor Value** (0–255): velocity for "currently being controlled" state.

**Left pane — MIDI Channel** (3 dropdowns, one per value):
- "From plugin settings" (default)
- "On 10% / 25% / 50% / 65% / 75% / 90% / 100% brightness"
- "Pulsing 1/16, 1/8, 1/4, 1/2"
- "Blinking 1/24, 1/16, 1/8, 1/4, 1/2"

**Right pane — Profile color palette** (read-only, populated when input profile is bound):
- 128 named colors at EVEN velocity values 0, 2, 4, ... 254 + value 255
- Named palette: Off, Dark/Light Gray, White, Light/Med/Dark Red, Peach, Orange, Yellow (variants), Green (14+ variants), Cyan, Blue (13+ variants), Purple, Magenta (4+ variants), Pink, Brown, White 10/25/40/50%, Cold white, etc.

**Critical:** odd velocity values are NOT valid palette entries. Setting Upper=3 produces NO light. Setting Upper=10 → red. **Always use even values from the profile table** when patching feedback.

**Useful palette anchors:**

| Color | Velocity |
|---|---|
| Off | 0 |
| White | 6 |
| Red | 10 |
| Orange | 18 |
| Yellow | 24 |
| Light green | 40 |
| Green | 42 |
| Cyan/Teal | 36 |
| Sky Blue | 80 |
| Blue | 82 |
| Purple | 162 |
| Magenta | 188 |
| Pink | 112 |

## Buttons need a Function attached to toggle

A VC button with `<Function>No function</Function>` will not toggle state, so Upper Value feedback never fires (button stays at Lower forever). Workaround: attach a **dummy Scene** with no FixtureVal entries (Function ID 999 in APCmini-MIDI-Test.qxw, named "Feedback Dummy"). Toggle then flips state and Upper feedback fires correctly.

For real busking widgets, the actual Scene/Chaser being controlled provides the toggle target naturally.

## Faders (9 total, 8 + master)

| Fader | Assignment | Notes |
|---|---|---|
| Master (far right) | **Grand Master** | Built-in QLC+ |
| 1 | All movers Pan offset (±64 from current) | Additive, not absolute, so releasing doesn't snap fixtures |
| 2 | All movers Tilt offset | Same additive |
| 3 | All movers Speed | P/T speed channel |
| 4 | FT BW intensity group | Channel Group needed |
| 5 | RT BW intensity group | Channel Group needed |
| 6 | M Beam intensity group | Channel Group needed |
| 7 | Par intensity (combined) | Channel Group needed |
| 8 | CTO bias (warm↔cool) | Cross-fixture Channel Group, drives BW RGBW + M1 RGB + Par A/W ratios |

## Files created

**`~/Documents/QLC+/Farmhouse-Busking-Base.qxw`** — full busking workspace with:
- 22 FHC fixtures patched per the Hog map
- 25 palette scenes (7 POS, 8 COL, 5 GOB, 1 INT, 4 LOOK)
- VC layout with color-coded button rows (Pos blue / Col tinted / Gob purple / Look green)
- ZOOM Pulse chaser on BWs (1.5s breath)
- All BWs have Zoom=220 baked into wash-role position scenes (less-orange warm)
- **No MIDI mappings yet** — that's the next session's work

**`~/Documents/QLC+/APCmini-MIDI-Test.qxw`** — standalone controller test workspace:
- No fixtures, no DMX
- 2 universes (U1 reserved, U2 = APC mini input/feedback)
- 81 VC buttons: 8×8 grid (Channels 129–192) + 8 scene-launch (Channels 241–248 — note top button may be 239 not 241) + 8 track-select (Channels 229–236) + 1 Shift (Channel 251)
- Each button has HSV-cycled BackgroundColor for rainbow rendering
- Dummy Scene "Feedback Dummy" (Function ID 999) attached to every button
- **Outstanding:** Custom Feedback Upper/Monitor values not yet bulk-patched per button. Manual test with Upper=10 → red pad works after profile is assigned.

## QLC+ I/O setup (verified working)

- **Universe 2** dedicated to APC mini control surface
- Universe 2 → **Input ✓**, **Output ✗**, **Feedback ✓**
- Profile tab → **"Akai APC Mini mk2"** assigned to Universe 2
- Universe 1 (separate) → reserved for ENTTEC DMX output to fixtures

Separates control-surface MIDI from DMX traffic cleanly. Standard pattern for any QLC+ rig with both.

## Outstanding for next session

- [ ] **Verify scene-launch top button channel** — press the topmost right-column pad ("Soft Keys") and confirm Channel. Likely 239 (note 111), making the full scene-launch range 111–118 = Channels 239–246. If so, my patch missed by 1 and the bottom scene-launch button has no binding.
- [ ] **Bulk-patch Custom Feedback** — write Python that sets each VC button's Lower Value (off state, e.g., dim white = 1 or off = 0) and Upper Value (on state) to palette indices matching the button's BackgroundColor. **First step:** have Jefe manually configure one button with Custom Feedback in the UI, save the .qxw, then I read the file to see how QLC+ serializes the feedback XML (probably as `<Feedback>` children inside `<Input>`). Then write the bulk patch.
- [ ] **Add Channel Groups** for fader assignments (FT BW dim, RT BW dim, M Beam dim, Par dim, CTO bias).
- [ ] **Wire faders 1-8** to their assignments via External Input on VC Slider widgets.
- [ ] **Author the 8 universal mood looks** in Farmhouse-Busking-Base.qxw (currently has 4 placeholder LOOK scenes; replace with the 8 song-arc moods).
- [ ] **Strobe-bump scene** — HTP overlay scene that adds +15% intensity to all fixtures, mapped to a momentary VC button with Flash action.
- [ ] **Audio-trigger setup** — defer until after one gig with the mk2.
- [ ] **Reconcile** — the APCmini-MIDI-Test.qxw proved the protocol; now apply the working MIDI mappings + Custom Feedback to Farmhouse-Busking-Base.qxw.

## Lessons learned (carry forward)

- **Always plug in the controller BEFORE launching QLC+** so the OS detects it cleanly and QLC+ enumerates it on startup.
- **Assign the Input Profile FIRST** before doing any feedback work. Without it, the palette table is empty and Custom Feedback fails silently.
- **Use Auto Detect for the first button of each region** to discover the QLC+ Channel offset, then derive the rest mathematically. The +128 offset on Note messages is universal in QLC+.
- **Attach a dummy Function** to test buttons that don't drive any real Scene. Toggle action needs SOMETHING to toggle.
- **mk2 palette uses EVEN velocity values only** (0, 2, 4, ... 254 + 255). Odd values produce no light. Read the profile palette table for valid values.
- **Side buttons (mk2 scene-launch + track-select)** are RGB on the mk2 (were single-color on mk1). Worth using the colors.
- **macOS doesn't need a driver** for the mk2 — class-compliant MIDI USB.
- **Column orientation in QLC+ profile labels** counts from right (Col 1 = rightmost). Doesn't affect channel arithmetic but matters for interpreting "Button R-C" labels.
