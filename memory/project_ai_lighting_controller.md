---
name: project-ai-lighting-controller
description: North-star vision — silicon-native macOS DMX+AI app. Realtime natural-language control of a lighting rig, with multi-camera (incl. 360°) visual feedback closing the loop back to the agent.
metadata:
  type: project
---

# AI Lighting Controller — North Star

End-goal app: **silicon-native macOS DMX+AI controller** that lets an operator (or director, or vibes-driven musician) drive a lighting rig in real time via natural language, while the agent watches the room through cameras and corrects itself based on what the lights actually look like.

**Why:** Every existing console (MA2, Hog, GrandMA3, QLC+, Chamsys) is a deep-modal expert tool. The interesting frontier isn't replacing them — it's adding a closed-loop natural-language layer on top, with the agent literally seeing the result. Solo operators and small productions can punch way above their weight; large productions get a co-pilot for busking.

**How to apply:** Anything DMX/lighting Jefe builds in 2026 should be evaluated against whether it advances this loop. The QLC+/ENTTEC work ([[project-dmx-lighting]]) is the PoC layer — open-loop, human-in-the-middle, no vision yet. Don't let it accrete features that don't lead toward closed-loop NL control.

---

## The Loop (target architecture)

```
        ┌──────────────────────────────────────────────┐
        │  Operator: "warmer, pull the SL beam in"     │
        └──────────────────────┬───────────────────────┘
                               ▼
              ┌────────────────────────────────┐
              │  Agent (on-device, Apple sili) │
              │  - rig model (fixtures+patch)  │
              │  - intent → DMX channel deltas │
              └────────────┬───────────────────┘
                           ▼
                ┌──────────────────────┐
                │  DMX out (sACN /     │
                │  Art-Net / USB Pro)  │
                └──────────┬───────────┘
                           ▼
                ┌──────────────────────┐
                │   Physical rig       │
                └──────────┬───────────┘
                           ▼
        ┌──────────────────────────────────────────┐
        │  Cameras (multi-angle + 360°)            │
        │  → frame-grab → vision model            │
        │  "SL beam still too wide, color is cool" │
        └──────────────────────┬───────────────────┘
                               ▼
                       (back to agent)
```

## Core Capabilities (vision)

- **Rig ingestion** — import grandMA2 XML export, QLC+ workspaces, GDTF, or hand-rolled JSON. Build a typed model: fixtures, modes, channel functions, physical position, orientation, color profile.
- **Realtime NL command** — "knock the front wash down to half," "follow the singer SL," "give me Reputation energy for the next song." Streaming command, sub-second response.
- **Multi-camera vision** — 2+ angles plus optional 360° (Insta360 X-series is already in the toolkit, see [[project-splat-business]]). Per-camera ROI for stage zones; agent reasons over composited view.
- **Closed-loop correction** — agent compares intent vs. observed output and adjusts. Example: "warmer" → bumps amber, sees camera still reads cool → bumps further until vision agrees.
- **Operator override** — every agent action is a diff the operator can punch through, reject, or freeze. Never the only thing holding the rig.
- **Cue capture** — "save this look as 'Eras pink'" — store the resolved DMX state, regenerable on recall.
- **Silicon-native** — Apple Silicon target (M-series). On-device vision + LLM where viable; remote model fallback. Use the Metal / Core ML / MLX stack rather than wrapping Electron + Python.

## Why Silicon-Native

- Latency: cue-to-light needs to feel like an instrument, not a chatbot. On-device inference + USB/Art-Net keeps the loop tight.
- Reliability: venues have shit Wi-Fi. The thing has to work disconnected.
- Distribution: ships as a single .app (cf. [[feedback-apps-ship-self-contained]]).
- Aligns with the Mac-native stack Jefe is already building ([[project-splat-business]], [[project-chronical]]) — Swift/SwiftUI + Python helpers, MetalSplatter / Core ML / MLX patterns transfer.

## Sequencing (rough, not committed)

1. **PoC layer (now)** — ENTTEC USB Pro + QLC+ on M5. Open-loop. Human is the vision system. Lake Arrowhead Saturday is the test. ([[project-dmx-lighting]])
2. **NL → DMX bridge** — Swift app or CLI that takes natural-language commands and writes sACN/Art-Net. No vision yet. Validate the intent-to-channel mapping on the Lake Arrowhead rig.
3. **Single-camera feedback** — one webcam, vision model reads stage zone colors, agent self-corrects on color/intensity. Movers still open-loop.
4. **Multi-camera + 360°** — composited stage model, position-aware feedback (mover pan/tilt closes the loop).
5. **Cue library + show mode** — saved looks, song-aware sequencing, busking with safety rails.

Each step ships and gets used at a real show before the next one starts.

## Open Questions (not blockers, but to track)

- DMX transport: ENTTEC USB Pro is fine for one rig, but networked sACN/Art-Net is the right path for multi-universe. Cheap interfaces: ENTTEC ODE / DMXKing.
- Fixture profiles: GDTF/MVR is the open standard but adoption is uneven. May need to keep a hand-curated JSON layer for cheap Chinese fixtures.
- Vision latency budget: how fast does the loop need to close to feel "live"? Probably 500ms acceptable, 100ms aspirational.
- LLM choice: on-device (MLX, gemma-3-on-mlx, Qwen) vs. Claude API. Probably hybrid — local for tight loop, Claude for high-level intent parsing.
- Show-day failure mode: if the agent crashes mid-song, the rig must hold the last state, not blackout. Watchdog + last-known-good freeze.

## Adjacent Projects

- [[project-dmx-lighting]] — current PoC layer, two venues
- [[project-splat-business]] — same silicon-native Mac stack, Insta360 SDK already known
- [[project-livestream-production]] — lighting is part of the broader self-owned show stack
- [[project-the-band]] — modular live act, the rig this controls in the long run
- [[reference-mcp-servers]] — OBS/Stream Deck MCPs are adjacent integrations
