# Feedback: Alt text 120-char rule

**Date:** 2026-04-16

## The correction

Generated alt text between 120–140 characters in Level 9 screenshot uploads.
Jefe flagged that Canvas accessibility warnings appeared and I had "lost the
<120 char rule." The rule was already documented in:

- `CLAUDE.md` — "Alt text under 120 characters, no 'image of' or file extensions"
- `LEVEL-TEMPLATE.md` — two mentions, including "All images: descriptive alt
  under 120 chars, never a filename"

## Why I failed to apply it

Treated alt text as ad-hoc descriptive prose while writing a Python script,
rather than as a rule to check. No explicit length-check in my loop. The rule
lives in CLAUDE.md which I read at session start but didn't recall when
writing the upload script dozens of messages later.

## How to apply

- **Whenever generating alt text programmatically, assert length < 120 chars
  before uploading.** The assertion is in
  `scripts/canvas/upload_level9_screenshots.py` now — copy this pattern into
  future screenshot upload scripts:

  ```python
  assert len(alt_text) < 120, f"Alt text too long ({len(alt_text)} chars): {alt_text}"
  ```

- **Before writing any HTML that includes an `alt=` attribute, recall the
  three Canvas accessibility rules**:
  1. Alt text under 120 characters
  2. No "image of" or "picture of" prefixes
  3. No filenames as alt text

- **When reviewing student-facing content I generated, quickly scan for the
  three failure modes above.** `grep -oE 'alt="[^"]{120,}"'` catches rule 1.
