---
name: IMSCC imports must be unpublished by default
description: All IMSCC cartridges must import as unpublished/draft — never auto-publish on import
type: feedback
---

IMSCC cartridges must import with all items unpublished (draft). Never ship a cartridge with `active` or `published` workflow states.

**Why:** Jefe imports cartridges to multiple Canvas instances (Crafton, Redlands, sandbox). Auto-publishing on import means students could see unfinished content before Jefe has reviewed it.

**How to apply:** All build scripts call `set_unpublished(BUILD_DIR)` from `scripts/canvas/imscc_utils.py` before packaging. This flips:
- module_meta.xml: `active` → `unpublished`
- wiki pages: `content="active"` → `content="unpublished"`
- assignment_settings.xml: `published` → `unpublished`
- assessment_meta.xml: `published` → `unpublished`, `available` → `false`
- discussion meta .xml: `active`/`published` → `unpublished`
