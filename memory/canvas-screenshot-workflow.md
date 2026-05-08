# Canvas Tutorial Screenshot Workflow

Pattern developed across Levels 5-9 for adding screenshots to tutorial
HTML on Canvas. Reusable for Level 10+.

## Idempotent strip-and-reinsert

**Problem:** Running an upload script twice creates duplicate img tags
because matching "which img tags were inserted by me" is non-trivial —
Canvas stores img src by file ID, not upload path.

**Solution:** Query the Canvas Files API for all files in the screenshot
folder (e.g., `tutorial_screenshots/level_N`) and strip any img tag
referencing any of those file IDs before re-inserting.

```python
r = requests.get(f"{BASE}/folders/by_path/tutorial_screenshots/level_9", headers=H)
folder_id = r.json()[-1]["id"]
r = requests.get(f"{API_URL}/api/v1/folders/{folder_id}/files", headers=H,
                 params={"per_page": 100})
folder_ids = {str(f["id"]) for f in r.json()}

id_pattern = "|".join(re.escape(fid) for fid in folder_ids)
html = re.sub(r'<p>\s*<img[^>]*/files/(?:' + id_pattern +
              r')/[^>]*>\s*</p>\s*', "", html)
```

## Screenshot sizing (Level 9 pattern)

Two size classes based on native pixel width:

| Class | max-width | When |
|---|---|---|
| small | `340px` | Alert dialogs, warnings, small menus (native ≤ ~800px wide) |
| large | `100%` | Feature-rich dialogs, panels (native ≥ ~1200px wide). **Not** 720px — let Canvas's assignment div size it. |

Previous attempts with 480px universal and 720px large looked "horsey"
(too big for small alerts, artificially cropped for large dialogs).

## Alt text rule

**Under 120 characters, always.** See `feedback_alt_text_rule.md`.
Enforce with `assert len(alt_text) < 120` in the upload loop.

## Canvas HTML-entity gotcha

Canvas **decodes** HTML entities in rich content when it stores them.
Anchor strings for text insertion must use the decoded characters:

- `&ldquo;` → `"` (left double quote, U+201C)
- `&rdquo;` → `"` (right double quote, U+201D)
- `&rsquo;` → `'` (right single quote / apostrophe, U+2019)
- `&mdash;` → `—` (em-dash, U+2014)

Using entity form in the anchor = no match.

## macOS screenshot filename gotcha

macOS writes screenshot filenames with a **narrow no-break space** (`\u202f`)
between "at" and the time, NOT a regular space:

```python
# Correct
"Screenshot 2026-04-15 at 9.00.20\u202fPM.png"

# WRONG — file will not be found
"Screenshot 2026-04-15 at 9.00.20 PM.png"
```

## Canvas file ID stability

When uploading with `on_duplicate: overwrite`, Canvas may assign a **new
file ID** and delete the old one. Canvas usually auto-rewrites internal
links in rich content to point to the new ID, but verify the link after
overwrite.

## Script template

See `scripts/canvas/upload_level9_screenshots.py` as the reference
implementation for Level 10+.
