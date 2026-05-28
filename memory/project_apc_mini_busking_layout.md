---
name: project-apc-mini-busking-layout
description: "APC mini mk2 → QLC+ layout design for live busking at FHC. Mood-based looks per genre/event page, momentary +15% intensity bumps as strobes, audio-triggered tempo. Design intent only — actual built workspace state is on shai-hulud and needs to be reconciled into this doc."
metadata:
  type: project
---

# APC mini mk2 Busking Layout

Designed 2026-05-27 (this M5 / iMac session). The .qxw that's actually wired up was built earlier the same day on shai-hulud and is the source of truth for what's *in QLC+ right now*. This file captures the design *intent* — the framework Jefe agreed to. Reconcile with shai-hulud's workspace when accessible.

**Why:** Busking the FHC rig from a laptop trackpad in low light during a set is fragile. The mk2's 8×8 RGB grid + 9 faders gives positional muscle memory and visual state feedback (green = playing, group-tinted idle states = literal rig map). Pre-deciding the row/column language now prevents the controller from becoming a junk drawer of one-off bindings.

**How to apply:** When wiring buttons or scenes for QLC+ at FHC, conform to the row/column verb-object framework below. When designing for a new venue or band, build pages — don't recolor the existing one. Every new feature gets evaluated against: "does this serve a busker who can't look down?"

Related: [[project-dmx-lighting]] (rig + venues), [[project-ai-lighting-controller]] (north star this is a PoC for).

---

## Hardware

**APC mini mk2** — assumed. RGB pads, 9 faders (8 + master), 8 scene-launch column on right, 8 track-select buttons across bottom, Shift bottom-right. If the actual hardware turns out to be mk1, the color-state language collapses to red/green/yellow and the idle group-tint scheme breaks.

## Decisions locked 2026-05-27

1. **Looks are mood-based, not song-based.** Bands rarely repeat; songs aren't predictable. Jefe reads vibe from the first few bars. 8 moods per page is the unit.
2. **Pages are genre / recurring event.** FHC has very few repeating bands but several repeating events: **Jazz, Thursday Throwdown, Homegrown**. Mood × Genre is the navigation paradigm.
3. **No dedicated strobe channel.** Fixtures sit at 85–90% intensity baseline. A momentary pad adds the remaining 10–15% as a manual flash on chorus hits. Released pad = back to baseline. This trades automated strobe for human-timed accents the band can feel.
4. **Tempo is multi-source.** Tap-tempo pad for manual. Plan to also feed an **aux send from the FOH board into a cheap audio interface**, detecting kick (and maybe hi-hat) to drive auto-spin / chase rates. Aspirational for v2, not blocking the mk2 rollout.
5. **Per-venue workspaces.** FHC and Lake Arrowhead are separate `.qxw` files. Arrowhead's Rockville 384 + cheap movers stay on the legacy board; that rig doubles as a **bench for prototyping pan/tilt combos for the Temu movers** before they ever see FHC. No shared workspace, no auto-switching.

## Layout — "rows are verbs, columns are objects"

**Main 8×8 grid:**

| Row | Function | 8 columns |
|---|---|---|
| 1 (top) | **MOODS** | 8 mood looks for the current page (genre/event). Hit one = recall that look. |
| 2 | **COLOR PALETTE** | Red, Amber, Warm White, Cool White, Blue, Magenta, UV, *+15% Intensity Bump* (momentary, see decision 3) |
| 3 | **POSITION PALETTE** (mover aim) | Band Ctr, Lead Vox, Drum Riser, SR, SL, Grass DS, Grass US, Back Wall |
| 4 | **BEAM PALETTE** | Narrow, Wide, Slow Spin, Fast Spin, Gobo A, Gobo B, Open, Reset |
| 5 | **GROUPS** | FT BWs, RT BWs, All BWs, M Beams Ctr, M Beams SL/SR, All Beams, Pars Band, Pars Grass |
| 6 | **INDIV BWs** | BW 301, 302, 303, 304, 305, 306, *spare*, *spare* |
| 7 | **INDIV M BEAMS + key Pars** | Beam 1, 2, 3, 4, Par 201, 205, 210, 211 (Shift → second par bank) |
| 8 (bottom) | **TEMPO / ACCENT** | Tap Tempo, Strobe Bump, Blackout, *4 spares for chase steps / song-structure hits* |

**Right column (8 RGB scene-launch):** **MOOD SLOTS** confirm/preview — pre-arm a mood without firing it; release = fires. (Or: secondary mood bank. TBD with shai-hulud reconciliation.)

**Bottom row (8 RGB track-select):** **PAGES** — Page 1: Jazz, Page 2: Thursday Throwdown, Page 3: Homegrown, Pages 4–7: open genre slots, Page 8: system/housekeeping (panic, fixture monitor, blackout-with-feedback).

**Shift:** hold for blackout momentary; modifier for alt assignments (Shift + row 7 → second par bank, etc.).

## Color language (RGB pad states)

| State | Color |
|---|---|
| Active / playing | bright green solid |
| Available, idle | dim white |
| Selected / armed | bright blue solid |
| Queued / next | blue pulse |
| Dead fixture (Par 202 blue ch, Par 212 whole) | dim red |
| Modifier engaged (Shift held) | row underglow flips amber |
| **Group identity tint** (idle state) | warm-yellow for FT, cool-cyan for RT |

The group tint means rows 5–7 read as a literal map of the rig at rest. Find a fixture in the dark.

## Faders (9 total, 8 + master)

| Fader | Assignment |
|---|---|
| 1 (master) | Grand master |
| 2 | FT BW dim group |
| 3 | RT BW dim group |
| 4 | M Beam dim group |
| 5 | Par dim group |
| 6 | Strobe rate (when dedicated strobe scenes ever exist) |
| 7 | Mover auto-speed (or audio-tempo override) |
| 8 | Color CTO (warm↔cool bias across all fixtures) |
| 9 | *spare* — likely "intensity bump amount" to tune the +15% from decision 3 live |

Group dim beats per-fixture dim at 8 faders — busking needs broad strokes, not surgery. Surgery happens in the grid + Simple Desk.

## Open items / reconciliation

- [ ] **Pull shai-hulud's actual built workspace** and diff against this framework. The afternoon session programmed what's currently loaded; this doc is what we agreed to *next*. Where they disagree, decide whether to migrate the workspace or update this doc.
- [ ] Confirm hardware is mk2 not mk1 (Akai shipping is mk2; reasonable assumption — verify on unbox).
- [ ] QLC+ MIDI input profile XML for APC mini mk2 — does QLC+ ship one or do we author from scratch? (V4 has APC profiles bundled but mk2 is newer; check `~/Library/Application Support/QLC+/Input Profiles/`.)
- [ ] Strobe-bump implementation: is it a Scene with all fixtures at +15% layered via HTP, or a Function attribute override? HTP overlay is the cleaner mental model.
- [ ] Audio-tempo: pick the cheap interface (Behringer UCA222 = $30, or a borrowed Scarlett Solo). Map kick → BPM via a QLC+ script or a dedicated bridge (likely needs a small standalone app — falls in the [[project-ai-lighting-controller]] PoC path).
- [ ] Lake Arrowhead workspace: separate .qxw, Rockville 384 patch, used as Temu-mover p/t bench. Build when first needed.
- [ ] Verify QLC+ V4.14 stores MIDI mappings inside the .qxw (so per-venue files carry their own bindings) vs. globally. If global, document how to swap between venues cleanly.

## Why this won't survive contact with shai-hulud's version unchanged

The afternoon shai-hulud session built the actual working surface. This doc is a design framework reached without seeing what's already wired. Expect to revise rows 5–7 (groups + individual fixtures) and bottom-row assignments after diffing. The decisions in the "locked 2026-05-27" section above are paradigm-level and *should* survive; the specific button-to-function map is provisional.
