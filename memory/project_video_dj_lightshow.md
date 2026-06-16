---
name: project-video-dj-lightshow
description: "Vision for a new performance role — 'video DJ' driving a DMX rig from pixel-mapped music video: derive colors from video frames → complementary chases on washes, tempo → flash timing. Built on the FHC previz + QLC+ XML agent-loop pipeline."
metadata: 
  node_type: memory
  type: project
  originSessionId: f674c85e-5a5f-4195-9f56-b3a6c44fecbd
---

# Video DJ / Pixel-Mapped Light Show

Crystallized in a nightly brain dump (2026-06-10). The realization: the FHC previz + QLC+
stack isn't just a busking aid — it's the foundation for a **new performance role** ("video
DJ") with a real market.

**Why:** Get the Blender model's fixture XYZ airtight, and QLC+ scene/look generation can be
run by Claude as an agent loop — human interaction drops toward zero. Layer video-derived
color on top and you have novel busking infrastructure nobody's packaging as a role yet.

**How to apply:** Treat this as the north-star *application* that [[project-fhc-previz]] and
[[project-ai-lighting-controller]] feed. Build bottom-up: alignment reference first, then the
XML agent loop, then video pixel-mapping. Don't chase the video layer before the model is
calibrated and the agent loop is generating valid QLC+ XML.

Related: [[project-ai-lighting-controller]] (north-star NL+closed-loop controller),
[[project-fhc-previz]] (the model + Art-Net link this rides on), [[project-dmx-lighting]] (rig),
[[project-content-pipeline]] / [[project-summer-26-content-pipeline]] (this is content-worthy).

## The pipeline (spine)

1. **Blender model → precise beam-alignment reference.** Aim all movers at one XYZ point,
   photograph the real rig hitting it **with a physical ruler in frame**. That photo is the
   ground-truth fiducial that makes the model's XYZ alignment airtight.
2. **QLC+ XML agent loop.** With alignment trusted, Claude generates/edits QLC+ `.qxw` scenes
   and looks programmatically (the XML schema is already well understood — see
   [[project-apc-mini-busking-layout]]). Human-in-the-loop drops to near zero.
3. **Video pixel-mapping → color-driven show.** Feed a music video, derive frame colors,
   drive washes with complements, sync flash timing to tempo. Color-driven busking from media.
4. **Video DJ role.** The packaged performance offering — a music-video-driven, pixel-mapped,
   tempo-synced light show.

## Concept seeds (not yet scoped)

- **Pixel-map-to-wash:** feed a music video → extract dominant colors → wash colors chase the
  complements, tempo drives flash timing. Genuinely novel busking infrastructure.
- **"Planets around a black hole":** M1 Beamer with gobo/prism spinning one direction while all
  other movers pan/tilt in the same orbital pattern around it — an orbital signature look.
