---
name: feedback-respect-physical-constraints
description: "Don't let calibration/solver math override physical constraints — fixtures on a truss share Y/Z, only X varies. Model reality, not just the numbers."
metadata: 
  node_type: memory
  type: feedback
  originSessionId: f674c85e-5a5f-4195-9f56-b3a6c44fecbd
---

When fitting a model to measured data (e.g., triangulating DMX-fixture positions from rig aim),
**physical constraints override the math.** Fixtures hung on a rigid truss are co-linear: they
share ONE Y and ONE Z (the truss line); only X (position along the truss) varies per fixture.
Per-fixture Y/Z scatter that comes out of a solver is MEASUREMENT NOISE — a real truss cannot
bend to it. Applying it per-fixture floats lights into mid-air and produces a physically
impossible, useless model.

**2026-06-12 mistake:** triangulated FHC fixture positions had per-fixture Y/Z variation (noise
from assumed mark heights + slop); I wrote a script that moved each fixture to its raw X/Y/Z,
scattering them off the trusses. Jefe (correctly, furiously): "Z and Y are going to be LOCKED IN
A TRUSS. You can't just put fixtures where the math says." The calibration's only legitimate
output was X (±0.1 m); Y/Z are truss constants.

**Why:** A previz/digital-twin model is only valuable if it obeys the real rig's structure. A
number that's "more accurate" but physically impossible is less accurate than the constraint.

**How to apply:**
- Before moving solver output into a model, ask what's physically fixed. Truss → fixtures locked
  in Y/Z, free in X. A wall-mounted thing → locked in its plane. Etc.
- Fit only the free DOF (here, X). Hold constrained DOF at the structure's value.
- If a whole structure looks offset (e.g., uniform Y error across all fixtures on a truss), move
  the STRUCTURE as a unit (truss + its fixtures together) — never the fixtures individually.
- Accept that real measurement slop can't be chased without breaking physics; stop at the
  constraint. [[project-fhc-previz]] [[feedback_weight_explicit_prompts]]
