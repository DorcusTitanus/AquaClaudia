---
name: Verify the compute target before routing a GPU-bound pipeline
description: For splat/ML/render jobs, confirm the machine has the right accelerator (CUDA vs Metal) FIRST, before choosing tools or moving data
type: feedback
---

**Why:** On 2026-06-06 I routed the Bekins splat pipeline to thufir (the Linux
box — intuitive for "ffmpeg/COLMAP server") and discussed COLMAP/gsplat as the
next step *before* checking that thufir has no usable CUDA GPU (RX 560 + a 2008
GeForce 9500 GT). Gaussian-splat training is GPU-bound, so the target machine's
accelerator is the first constraint, not the last. The Mac-native answer
(msplat/CorbeauSplat) was already sitting in Jefe's memory and I didn't surface
it until I'd already sent work to the wrong box. Frame extraction wasn't wasted,
but the path was longer than it needed to be. Jefe was rightly miffed.

**How to apply:** For any GPU/accelerator-bound task (splat training, ML
inference/training, GPU render), step 0 is: *what machine finishes this, and
does it have the right accelerator?* Run `nvidia-smi` / check for Metal / read
hardware_machines.md BEFORE picking tools or moving data. CUDA tools
(Inria 3DGS, gsplat, nerfstudio) need a modern NVIDIA card; on Apple Silicon
the path is Metal/wgpu (Brush, msplat, CorbeauSplat). Also: re-read the
relevant project memory for already-chosen tools before researching from
scratch — the answer is often already in the notes.
