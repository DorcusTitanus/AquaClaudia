---
name: happenstance-post-process-findings
description: "Top-line findings from mapping the 166KB post_process_NABv05.pl — what's dead code, what's broken, and the three subs that are 25% of the porting effort"
metadata: 
  node_type: memory
  type: project
  originSessionId: f9675d30-5405-4d26-b05b-6db42e1fe12e
---

Top findings from agent-produced catalog of
`post_process_NABv05.pl` (3,957 lines, ~80 subs). Catalog itself at
`~/ClaudeCode/happenstance-typesetting-notes/post_process_NABv05_catalog.md`.
Companion to [[happenstance-typesetting-pipeline]].

## What's actually live

- **Only `epub` and `MSWord` output paths are reachable**.
  `@output_products = ('epub', 'MSWord')` is hardcoded around line 89,
  so the menu can never offer WileyML or ALL.
- **`WileyML` looks broken**: it dispatches to per-series subs named
  `preWileyML_6x9` / `postWileyML_6x9`, but only `preBOT_*` /
  `preEPUB_*` / `postBOT_*` / `postEPUB_*` stubs exist. Would die on
  first call. Likely dead since at least the last menu prune.

## Dead code candidates (cut before porting, confirm with Jefe)

- `appendFigure` — call site commented out, regex matches literal `"6x9"`.
- `epub_parts` — call site commented out; `epub_splitChapters` handles parts inline now.
- `index_numbers` — empty body, labeled obsolete.
- `try_gif` — extension-guess hack triggered on copy failure; smells vestigial.
- 35 of 36 per-series pre/post stubs are no-ops. **Only `preBOT_6x9` does anything** (inlines `ChapterNumber + ChapterTitle`). The "per-series strategy" architecture is mostly aspirational.

## The porting pain is concentrated

Three subs are 25% of the script and where Swift work will be slow:
- `postEPUB` (~200 lines, regex chains over multiline XML)
- `postWileyML` (~240 lines, ~100 entity mappings) — but see above, may not need porting at all
- `build_the_epub` (~460 lines, assembles content.opf / toc.ncx / toc.xhtml with embedded XML templates and image-manifest globbing)

Everything else is shorter and shaped like a pipeline stage —
straightforward to port one at a time.

## Antipatterns to NOT carry into Swift

- **Stateful globals** (`%WileyML_parts_chapters`, `%epub_parts_chapters`,
  `$WileyML_part_DELETE`) — Swift should model the pipeline as
  functions returning new content, not `s///g` over mutable strings.
- **Sentinel placeholders** in content
  (`[RIPPED_SECTION]`, `[•STRIPPED PAGE NUMBER BLOTCHES!!•]`) used as
  multi-pass regex markers. Replace with structured intermediate
  representations.
- **`escape_path` / `UNescape_path` called 6+ times** around `system()` —
  artifact of shelling out with string concat. Swift's
  `URL` + `Process` args-as-array makes this go away entirely.

## Open questions for Jefe

1. Are WileyML / ALL actually used in production via some path other
   than the menu? Or genuinely dead and safe to drop?
2. Same for: `appendFigure`, `epub_parts`, `index_numbers`, `try_gif`,
   the 35 no-op per-series stubs.
3. Why is the per-series strategy mostly empty? Was it abandoned, or
   was it intended to grow and just never did?
