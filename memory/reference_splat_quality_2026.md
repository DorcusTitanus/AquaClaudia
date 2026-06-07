---
name: Gaussian Splat Quality Reference (2026)
description: What separates great Gaussian splats from mediocre ones, SOTA fixes per failure mode, and the Apple-Silicon/Brush reality — for Bekins archival + Splat Haus
type: reference
---

Deep-research synthesis (2026-06-06): 27 sources, 25 claims adversarially
verified (24 confirmed, 1 killed). For project_bekins_hall_vr.md (archival) and
project_splat_business.md (real estate). Honest about gaps — see bottom.

## Headline / reality check
Splat quality = controlling a handful of named failure modes, each with a
targeted academic fix. BUT nearly all SOTA fixes are CUDA/PyTorch reference
code. Whether they exist in Brush/msplat (Metal) is the biggest open question.
**On the M5 path, quality comes mostly from capture discipline + clean COLMAP
poses + Brush's built-in densification/pruning — not from fancy regularizers.**
Treat the SOTA list as "what to chase / ask Brush about," not "free wins."

## Failure modes → SOTA fix (all verified)
- **Floaters.** Two mechanisms: (a) opacity gradients vanish at a pseudo-
  equilibrium canceling color errors → **StableGS** dual-opacity
  (arXiv:2503.18458, +PSNR/−LPIPS); (b) poor SfM init → under-optimized
  gaussians → **EFA-GS** low-frequency-first (+1.68 dB, arXiv:2508.02493).
  Numbers are author self-reports, not independent repros.
- **Aliasing / off-trajectory instability** (vanilla 3DGS erodes on zoom-in,
  over-thickens on zoom-out). **Mip-Splatting** (CVPR 2024): Nyquist 3D
  smoothing + 2D box filter; single-scale training generalizes to unseen
  scales. *Highest-leverage method for real-estate "fly the camera anywhere"
  stability.*
- **Geometry/surface accuracy** (archival priority). **SuGaR** (CVPR 2024,
  arXiv:2311.12775): surface-alignment regularizer + Poisson mesh extraction.
- **Gaussian-count bloat / inconsistent FPS** (delivery priority).
  **Scaffold-GS → Octree-GS** (TPAMI 2025): multi-res anchors, LOD by camera
  distance → consistent FPS for web/mobile/Vision Pro.
- **Reflective/specular** (the windows problem). **SpecGaussian**
  (ACM MM 2024, arXiv:2409.05868): replaces SH with 8D diffuse + 8D specular
  latent descriptors; Shiny PSNR 27.23 vs 25.58 vanilla.

## Apple Silicon / licensing path (verified — this IS the stack)
CorbeauSplat (COLMAP/Glomap → Brush → SuperSplat) + **Brush (Apache-2.0,
Burn + wgpu→Metal)** is the commercially-clean, CUDA-free path; sidesteps the
Inria 3DGS non-commercial license (load-bearing for Splat Haus). Delivery:
**PlayCanvas open-sourced the SOG compression format** (pairs with SuperSplat
for shippable web sizes). Brush browser support needs WebGPU (Chrome 134+).

## Fidelity metrics
PSNR (crude pixel), SSIM (structural), **LPIPS (perceptual — most trusted)**.
All measured on held-out frames of the SAME scene → reward fitting the capture
trajectory, not true novel-view robustness. For a client audience,
floater-free + view-stable beats high PSNR.

## What could NOT be verified (the real gaps)
1. **Concrete Brush tuning numbers don't exist publicly.** No verified
   densification/opacity-reset/SH-degree/iteration/pruning values. CorbeauSplat
   exposes presets (e.g. "Aggressive Densification") + a Params tab but no
   published numbers. → tune empirically in Brush.
2. **Capture-side determinants for X4 footage: zero verified coverage** (motion
   blur, overlap/parallax, equirect→cubemap view count + FOV). Leads (blog
   tier): realitystack 360→splat writeup; github nicolasdiolez/360Extractor.
3. **Which SOTA methods run on Metal/Brush is unknown** — all 5 papers are CUDA
   reference code; portability to the M5 path is the central practical unknown.
4. No verified coverage: 2DGS, appearance embeddings (varying light),
   .spz/.ksplat specifics.

## Practical conclusion
On the M5/Brush path, **capture quality + COLMAP pose accuracy move results far
more than algorithm choice** (the SOTA is mostly CUDA-locked for now). Get a
clean floater-free splat from Brush first; chase Mip-Splatting/SuGaR-grade
quality only when it lands on Metal, or via a one-off cloud run for a hero
asset. Archival wants SuGaR (geometry); real estate wants Mip-Splatting +
Octree-GS + SpecGaussian + SOG compression.

## Key sources
Mip-Splatting niujinshuchong.github.io/mip-splatting · SuGaR arXiv:2311.12775 ·
Octree-GS city-super.github.io/octree-gs · SpecGaussian arXiv:2409.05868 ·
StableGS arXiv:2503.18458 · EFA-GS arXiv:2508.02493 · Brush
github.com/ArthurBrussee/brush · CorbeauSplat github.com/freddewitt/CorbeauSplat
· SOG blog.playcanvas.com/playcanvas-open-sources-sog-format-for-gaussian-splatting
