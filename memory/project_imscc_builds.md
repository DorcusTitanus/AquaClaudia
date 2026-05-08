---
name: IMSCC Cartridge Build Process
description: Workflow for building Canvas IMSCC cartridges for MULTI 211 levels
type: project
originSessionId: c3bb5033-1381-4dd9-acd3-da5003ca087c
---
## The One True Recipe (use this first, every time)

**Build the level live in Canvas, then ask Canvas to export it.** This produced the first IMSCC with zero issues on Level 10 (2026-04-24). Every previous level was hand-built with `scripts/canvas/build_levelN_imscc.py`, and every one of those had bugs that needed patching.

```python
# Canvas does all the IMSCC structure correctly when it generates its own cartridge.
import sys, time, requests
sys.path.insert(0, 'scripts/canvas')
from canvas_manager import CanvasAPI
api = CanvasAPI()

MODULE_ID = 22294629  # the Level N module

# 1. Kick off selective common-cartridge export of just this module
r = api.session.post(f'{api.base}/content_exports', data={
    'export_type': 'common_cartridge',
    'select[modules][]': str(MODULE_ID),
    'skip_notifications': 'true',
})
export_id = r.json()['id']

# 2. Poll until done (typically 15–30 seconds)
while True:
    e = api.session.get(f'{api.base}/content_exports/{export_id}').json()
    if e['workflow_state'] in ('exported', 'failed'):
        break
    time.sleep(3)

# 3. Download the .imscc to exports/
url = e['attachment']['url']
fname = f'/Volumes/Stilgar/Sync/Documents/ClaudeCode/exports/Level-N-Badge-Name.imscc'
with open(fname, 'wb') as f:
    f.write(requests.get(url).content)
```

### Why this works when the Python build scripts didn't

- Canvas-generated manifests register every `web_resources/` file correctly. Hand-built manifests routinely missed entries, which Canvas silently dropped on re-import.
- Canvas correctly emits `workflow_state="unpublished"` in both wiki page meta tags AND `course_settings/module_meta.xml`. Hand-built cartridges had to remember to do both, and the legacy scripts only did one.
- Canvas already knows how to encode rubrics, peer review settings, quiz questions, allowed file extensions, completion requirements, and assignment groups — every one of which was a bug source in the Python builders.
- File IDs and references stay correct because they come straight out of the live Canvas course.

### Verify after export

Quick sanity check before shipping:

```bash
unzip -q exports/Level-N-Badge-Name.imscc -d /tmp/check
grep -E "workflow_state" /tmp/check/wiki_content/*.html /tmp/check/course_settings/module_meta.xml
grep -oE "<title>[^<]+</title>" /tmp/check/imsmanifest.xml
```

Every wiki page meta and every module item should be `unpublished`. Manifest titles should match the live module item names.

## Build Order (live Canvas, before exporting)

1. Module exists and is named `Level N: Badge Name`.
2. Items in correct order, all `published=false`:
   - Overview (Page) — `must_view`
   - Tutorial (Assignment) — `must_submit`, 5 pts
   - Checkpoint (Quiz) — `min_score 4.0`, 5 pts, unlimited attempts, keep highest, shuffle, show correct
   - Project (Assignment) — `must_submit`, 25 pts, peer review enabled (count=2), level-specific peer review rubric attached
   - Screening Room or Discussion (if applicable) — `must_contribute`
   - Journal (Assignment) — `must_submit` (gated, no time window), 5 pts, Weekly Journal rubric attached
   - Level N+1 Unlocked (Page) — no completion requirement
   - Level N: Badge Collection (Page) — no completion requirement
3. Per-level total: 40 pts (5 + 5 + 25 + 5).
4. Journal gating is `must_submit` with no `unlock_at`/`lock_at` — see `feedback_journal_gating.md`.

## Reference Locations

- **Gold standard template**: `/Volumes/Stilgar/Sync/Documents/ClaudeCode/LEVEL-TEMPLATE.md`
- **Output cartridges**: `/Volumes/Stilgar/Sync/Documents/ClaudeCode/exports/`
- **Badge images**: `/Volumes/Stilgar/Sync/Documents/ClaudeCode/assets/source/*Designer.png` etc.
- **Reusable rubrics on Canvas**: Weekly Journal id=2499989, Tutorial Completion id=2499975

## Legacy: `scripts/canvas/build_levelN_imscc.py`

These hand-built cartridges (Levels 3–9) are now historical artifacts. Don't extend them. If a previously-built level needs to be reissued from a corrected master, **re-export it from Canvas** using the recipe above instead of patching the Python builder.

## Things That Broke in the Old Pipeline (avoid re-introducing)

1. Forgot to register `web_resources/` files as `<resource>` entries in `imsmanifest.xml` → Canvas silently dropped them on import.
2. Bare HTML fragments instead of full `<html><head><body>` documents in wiki pages → import failed silently.
3. Mismatched IDs between manifest, `course_settings/module_meta.xml`, and assignment XML → import warnings or missing items.
4. Missing `workflow_state="unpublished"` in either wiki meta or module_meta.xml → cartridges auto-published on import (violates `feedback_imscc_unpublished.md`).
5. Hardcoded completion requirements that didn't match the latest CLAUDE.md rules.

The Canvas-native export sidesteps all five.
