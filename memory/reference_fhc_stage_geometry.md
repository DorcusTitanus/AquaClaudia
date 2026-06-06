---
name: reference-fhc-stage-geometry
description: "Farmhouse Collective stage survey — laser-measured June 2026 for Blender model and rig spatial reasoning. Stage, trusses, LED, barn doors, amp closet, sub boxes. Some elements still estimated; outstanding items listed at bottom."
metadata:
  type: reference
---

# Farmhouse Collective — Stage Survey

Surveyed June 2026 by Jeff / Backhouse Studios with laser measure. Built for Blender pre-visualization but also feeds the [[project-ai-lighting-controller]] spatial model and any future livestream multi-cam blocking.

**Why:** Survey data is expensive to recapture (requires on-site access + laser tool + uninterrupted stage). Logging it once means the Blender model, the AI controller rig geometry, and future production blocking all draw from one source of truth instead of re-measuring.

**How to apply:** Use these numbers as the structural ground truth for any FHC venue work. When a measurement here disagrees with a model or a downstream doc, fix the downstream. When something here is marked estimated, replace it with a laser value on the next site visit before relying on it for engineering.

Related: [[project-dmx-lighting]] (rig patch + venue context), [[project-ai-lighting-controller]] (north star that will consume this).

---

## Stage Platform

| Dimension | Value |
|---|---|
| Width (SL→SR) | 9.635 m |
| Depth (front→back) | 6.025 m |
| Height off grade | 0.54 m |
| Material | Teak or teak-stained wood |

## Stage Right Staircase

| Dimension | Value |
|---|---|
| Distance from front edge of stage | 2.549 m |
| Width | 1.481 m |
| Depth | 0.846 m |
| Steps | 3 |
| Railing | Present (visible in photos) |

## Accessibility Lift (SL area)

| Dimension | Value |
|---|---|
| Width | 1.889 m |
| Height | 1.079 m |
| Depth | 1.509 m |

## Sub Boxes (front of stage, SR→SL)

| Segment | Value |
|---|---|
| SR edge to box 1 | 0.613 m |
| Gap: box 1 → box 2 | 1.484 m |
| Gap: box 2 → box 3 | 1.881 m |
| Gap: box 3 → box 4 | 1.878 m |
| Box 4 → SL edge | 0.382 m |
| Box dims (estimated from photo) | ~0.6 m cube |

> Individual box W/H/D not yet laser-measured.

## LED Screen

| Dimension | Value |
|---|---|
| Width | 5.934 m |
| Height | 2.512 m |
| Bottom off deck | 0.85 m |
| Top off deck (calc) | 3.362 m |
| Position | Centered on back wall |

## Barn Doors (cover LED screen)

| Dimension | Value |
|---|---|
| Panels | 2, center-opening |
| Each panel width (~half screen) | ~2.967 m |
| Panel height | ~3.446 m (floor to rail bottom) |
| Door back face → handle | 2.958 m |
| Handle → edge (est) | ~0.10 m |
| Material | Teak or teak-stained, horizontal slat |
| Rail mount | Back wall, overhead |
| Rail bottom off deck | 3.446 m |
| Floor guide: SL wheel from LED edge | 0.669 m |
| Floor guide: SR wheel from LED edge | 0.416 m |
| Floor track | None — hanging system, spot guide wheels only |

## Back Wall

| Dimension | Value |
|---|---|
| Deck → top beam/ceiling at back of stage | 4.28 m |
| Rail bottom below ceiling beam (calc) | 0.834 m |
| Cladding | Teak vertical planks flanking LED |

## Amp Closet (back of stage, separate room)

| Dimension | Value |
|---|---|
| Room width | 1.819 m |
| Room depth (front→back, doors open) | 1.868 m |
| Door width | 0.908 m |
| Door height (est, 7 ft) | 2.152 m |
| Ceiling at front wall (off deck) | 4.736 m |
| Ceiling at back wall (off deck) | 4.096 m |

**Roof slope (calc):** 0.640 m drop over 1.868 m → **~19°** pitch, ~4.1:12 imperial equivalent.

## Trusses

### Front Truss (FT)
| Dimension | Value |
|---|---|
| Distance from back wall | 7.367 m |
| Post-to-post span | 12.178 m |
| Ground → joist holder | 5.932 m |
| Stage deck → FT bottom | 4.318 m |
| Tube diameter | 2 in (confirm both trusses match) |

### Rear Truss (RT)
| Dimension | Value |
|---|---|
| Distance from back wall | 3.426 m |
| Stage deck → RT bottom | 3.921 m |

**Truss separation (calc):** 7.367 − 3.426 = **3.941 m**

## Roof / Overhead Structure

| Element | Value |
|---|---|
| Style | Flat pergola — exposed black steel framing |
| Ceiling | Teak horizontal slats with black backing |
| Corner columns | Square steel, matte black; ~0.25 m square (est) |
| HVLS fans | 2 units on center drop pole, 8-blade commercial |
| Fan blade span (est) | ~3.65 m (12 ft standard) |
| Signage | "FARM HOUSE" on front fascia beam, red/white |

## Reference landmarks (not structural)

| Description | Value |
|---|---|
| Far east edge of accessibility lift → front of amp cabinet | 10.808 m |
| Interior stage (steps → amp cabinet face) | 10.808 m |

---

## Observations / sanity checks (flagged 2026-06, resolve before final model)

1. **FT joist holder math doesn't close.** Stage deck = 0.54 m off grade. FT bottom = 4.318 m above deck → 4.858 m above grade. "Ground → joist holder" = 5.932 m. Gap = **1.074 m**. Most likely the truss hangs from the joist holder via ~1 m of chain/cable, not bolted to it. Matters for the Blender model — the truss is in space, not flush with the holder. Confirm on next visit.
2. **Sub boxes are not evenly spaced.** First inner gap (1.484 m) is ~0.4 m tighter than the other two (1.881, 1.878 m). Either intentional (cluster biased SR for crowd density?) or worth verifying. Edge offsets are also asymmetric: 0.613 m SR vs 0.382 m SL.
3. **Barn door floor-guide wheels asymmetric** vs LED edge: SL 0.669 m vs SR 0.416 m. Either doors don't park symmetrically (one set first when closing) or one measurement needs re-shooting. Easy verify: do inside edges meet at LED center, or biased?
4. **RT is 0.4 m lower than FT** (3.921 vs 4.318 m off deck). RT clears LED top (3.362 m) by only 0.56 m — any fixture hung below the RT eats sightline budget. Keep in mind for future rig changes and for the AI controller's rig model.

---

## Still needed (next site visit)

- Sub box individual W/H/D (each)
- Column dimensions — laser base of one column
- Fan drop pole length — hub-to-hub and ceiling-to-lower-hub
- Stage left staircase — confirm exists; measurements
- Amp closet door swing direction (into room or out onto stage?)
- Amp closet interior ceiling reference point
- Roof structure depth front to back (total pergola depth)
- Fascia sign panel W/H ("FARM HOUSE")
- FOH position — distance from stage front to mix
- Wing clearance — how far stage extends L/R before open air
- Resolve FT joist-holder vs FT-bottom gap (observation 1)

## Photo references

- `IMG_2882.JPG` — ceiling detail: teak slats, HVLS fans, lighting cluster on center drop pole, SL truss column with fixtures
- `IMG_2872.JPG` — ceiling detail: both HVLS fans on center spine, fixture cluster between fans, barn door slat texture
- `IMG_2864.jpeg` — full front FOH view: complete stage, LED active, barn doors parked open, sub boxes, stairs, signage, columns, turf audience area
