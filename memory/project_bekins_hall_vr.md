---
name: Bekins Hall VR Capture
description: 1910 campus dorm closing; Jeff + Bill Malin capturing 3D/VR archive before access ends
type: project
---

Bekins Hall is a 1910-era campus dormitory going offline for structural
problems. Jeff and Bill Malin are self-producing a high-fidelity 3D/VR
archival capture. Proof-of-concept site visit week of 2026-05-12 (public
areas only). Full-building return planned before end of June, contingent
on access.

**Working docs in /Volumes/Stilgar/Sync/Documents/ClaudeCode/:**
- `bekins-hall-vr-project.md` — internal strategy doc, not for sharing
- `bekins-monday-plan.md` — public-facing site visit plan
- `bekins-postprocess-pipeline.md` — public-facing processing report

**Capture roles:**
- Jeff: iPad Pro M4 (Scaniverse LiDAR + on-device splats, primary
  geometric record), Canon EOS 6D 24-200mm on tripod (art, ornament,
  architectural detail, ColorChecker color-managed)
- Bill Malin: Sony Alpha 1, 24mm prime, 8K video sweeps for Luma AI Pro
  splat training, head-mounted paper lantern with high-CRI 5500K LED

**Pipeline (three streams → Unreal 5 → existing campus VR lab):**
1. iPad Scaniverse → OBJ + PLY on-device, same-day
2. A1 video → Luma AI Pro cloud → hero-room splats
3. 6D RAW → ColorChecker color-managed → archival TIFFs + projected
   textures

**Cost: ~$60 total** (Luma Pro + Backblaze B2 archive).

**Explicitly out of scope:**
- Photogrammetry / RealityCapture / RunPod (redundant with iPad LiDAR)
- Building VR lab infrastructure (campus already has one)
- Fundraising pitch to college president (kept simple)

**Why:** Irreplaceable architecture and on-site art going offline.
Closure deadline is the project's strongest lever.

**How to apply:** Check the working docs first when Bekins comes up. Do
not re-introduce removed pipeline elements without Jeff's explicit
instruction. The campus VR lab handles ingestion and headset config —
delivery ends at handing over the packaged Unreal project.
