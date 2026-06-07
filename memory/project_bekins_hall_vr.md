---
name: Bekins Hall VR Capture
description: 1910 campus dorm closing; Jeff + Bill Malin capturing max-quality raw archive Monday, splat/VR processing deferred
type: project
---

Bekins Hall is a 1910-era campus dormitory going offline for structural
problems. Jeff Wilson and Bill Malin are capturing photographic and 360°
video archives before the building closes. Proof-of-concept site visit
Monday week of 2026-05-12 (public areas only). Full-building return
planned before end of June, contingent on access.

**Clip→room index:** [[Bekins Hall 360 Room Map]] — all 196 clips mapped to rooms (2026-05-18 capture).

**Working docs in /Volumes/Stilgar/Sync/Documents/ClaudeCode/:**
- `bekins-hall-vr-project.md` — internal strategy, not for sharing
- `bekins-monday-plan.md` — public-facing site visit plan
- `bekins-postprocess-pipeline.md` — public-facing capture-and-archive
  workflow

**Monday capture plan:**
- Jeff: Canon EOS 6D 24-200mm on tripod, art and architectural archive,
  ColorChecker color-managed
- Bill: Sony Alpha 1 on tripod, parallel art and architectural archive,
  shared ColorChecker
- Insta360 X4 (8K30): three-height walking passes (waist, chest,
  overhead) through every public area, done during breaks or after
  tripod sessions with rooms cleared

**Strategic pivot:**
Original plan included iPad LiDAR (Scaniverse) and A1 walking video
for splat training. Both removed after Scaniverse testing was
disappointing and the splat-service market revealed itself as
expensive ($20-300/month) for an uncertain-scope project. The 360° X4
covers everything the walking video would have, and processing
decisions are deferred until raw data is reviewed.

**Discipline: capture is the perishable resource.** Bank
maximum-quality raw data Monday, decide processing path later when
data is in hand, tooling has matured further, and project scope is
clearer.

**Cost: ~$30 committed** (Backblaze B2 archive optional). Splat / VR
delivery costs deferred.

**Tools rejected after evaluation:**
- Splatica — rip-off pricing
- Luma AI captures — pivoted away from this product
- Scaniverse on iPad Pro M4 — disappointing output quality

**Deferred processing options (to evaluate later):**
- Open-source on local hardware: gsplat / OpenSfM / CorbeauSplat
- Cloud: RunPod + RealityCapture + Postshot or gsplat (~$25-100)
- Paid SaaS: Polycam Pro $20/mo, Splatware, etc.
- Contribute to open-source 360 splat projects
  (`inuex35/360-gaussian-splatting`)

**Processing status (2026-06-06) — first splat in progress:**
- Source equirect mp4s (exported from X4) live on `thufir:/media/jeff/Piter/360FilesForCOLREC/` (Jefe's folder name; "COLREC" ≠ COLMAP, it's just his label).
- ffmpeg frame extraction done on thufir: **1537 plain equirect frames** from the **focus set** (clips 003–014, 028–052 = Lobby/Jimmy Room, copier, foyer+hallway, common room/closet — one connected scene). Script `~/extract_focus.sh` on thufir.
- **thufir CANNOT train splats** — no usable CUDA GPU (RX 560 + GeForce 9500 GT). It's COLMAP-CPU-capable only. Splat training moved to the **M5 (Shai-Hulud)**.
- **Tool: CorbeauSplat on the M5** — all-in-one Mac-Silicon pipeline; its 360-extractor does the equirect→cubemap reprojection COLMAP needs, then COLMAP/Glomap → Brush → SuperSplat viewer. Feed it the source equirect mp4s (not the 1537 frames — its extractor wants video). See licensing notes in project_splat_business.md.
- Goal: deliverable splats to share with the project's paying sponsor.

**Why:** Irreplaceable 1910 architecture and on-site art going offline.

**How to apply:** Check working docs when Bekins comes up. Do not
re-introduce removed pipeline elements (Scaniverse, Chinese lantern
head-mount lighting, A1 walking sweeps, RealityCapture as primary
tool, president fundraising pitch) without explicit instruction.
Current discipline: capture max quality, defer commitments.
