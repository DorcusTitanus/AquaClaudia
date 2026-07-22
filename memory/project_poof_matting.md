---
name: project_poof_matting
description: How ¡Poof! got a background remover that beats Apple's — BiRefNet (MIT) converted to Core ML and run on-device. The MIT-license choice, the two conversion blockers solved, and the OOM fix. Private repo DorcusTitanus/poof-ios.
metadata:
  type: project
---

# ¡Poof! — on-device matting that beats Apple Vision

¡Poof! is Jeff's resale-photo app (the template for [[project_niche_app_strategy]]): shoot an
item → background removed → centered on a white square → edit → save. iPhone-only, SwiftUI,
fully on-device, no network. Code: **private repo `DorcusTitanus/poof-ios`** (mirror of
`/Volumes/Stilgar/Sync/Documents/ClaudeCode/Poof`). Public beta shipped July 2026.

## Why Apple's cutout wasn't good enough

Apple's `VNGenerateForegroundInstanceMaskRequest` (Photos long-press model) runs at a fixed
**512×512** internal resolution and is class-agnostic **segmentation**, not alpha matting — and
it already applies a guided-filter edge upsample. So naive post-processing (erode/dilate/
feather) has almost no headroom. The only real lever is a better model. Result of switching:
about **4× cleaner** on hair and fuzzy fabric (builder's eyeball, not a benchmark).

## The model choice was a LICENSE decision as much as a quality one

- **BiRefNet — MIT license** (© 2024 ZhengPeng), covers code AND default weights → commercial
  use OK. This is why it won. Used the **lite matting** variant (swin_v1_t backbone, 88.9 MB,
  trained 1024²).
- **Bria RMBG-1.4 / 2.0 — DISQUALIFIED**: CC BY-NC, needs a paid Bria license. Great quality,
  wrong terms for a paid app.
- MIT obligates shipping the copyright + license text: it's in the app on an Acknowledgements
  screen. Never ship OSS model weights without the attribution.

## The two conversion blockers (BiRefNet → Core ML)

1. **Swin backbone broke `torch.jit.trace`** (int-cast on a non-scalar; pinning torch didn't
   help). Fix: use the **`torch.export` frontend + `.run_decompositions({})`**, not jit.trace.
2. **Core ML has no `deform_conv2d`.** BiRefNet's ASPPDeformable decoder uses modulated
   deformable convolution. Reimplemented it with **`grid_sample`** — same weights, verified
   numerically identical (~1.9e-5) — and monkeypatched onto `DeformableConv2d.forward`.
   Accumulate one kernel tap at a time (not all 9 stacked) to hold memory down.

Env that works: python 3.12 arm64, **torch pinned 2.7.0**, coremltools 9.0. Core ML output
matches PyTorch to ~0.035 (fp16 rounding). Full pipeline in `model-conversion/` in the repo.

## The OOM war (the non-obvious win)

First on-device test crashed. The **JetsamEvent log** showed iOS killed the app at **1.31 GB**.
Attributed with stage-wise `phys_footprint`: Core ML's **`.cpuAndGPU` (Metal) path allocated
~1.3 GB** of buffers for the resample ops — NOT the model math, NOT the CIEdgePreserveUpsample.
Fix was one line: **`.cpuOnly`** → inference footprint 1428 MB → **181 MB**, same 1024² quality,
~1.4 s vs 1.07 s. Lesson: on constrained devices, measure the footprint per compute-unit; the
GPU path can be dramatically hungrier than CPU for unusual ops.

## The pipeline design (decoupled)

Matte estimated at the model's native **1024²**, then **`CIEdgePreserveUpsample`** upsamples it
to the full-res photo using the photo's own luminance as a guide — so the saved cutout stays
full resolution with edges that snap to real pixels, not a 1024 grid. Output never resampled.

Write-up (shareable): `docs/matting-writeup.html` in the repo.
