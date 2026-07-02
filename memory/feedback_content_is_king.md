---
name: Content is king — never silently drop docx content
description: The docx→XML roundtrip exists specifically to stop the content loss the old regex text factories caused via greedy matches. Every content-bearing paragraph in the Word doc MUST reach the input XML — misplaced or flagged is acceptable, dropped is not.
type: feedback
---

Happenstance adopted the XML roundtrip precisely to STOP losing content on
import. The prior approach — regex "text factories" that reformatted the text —
kept dropping content through greedy searches. So the non-negotiable rule for any
docx→XML extraction (ChroniCal `DocxBodyBuilder` and every future client
converter):

**If it's in the Word doc, it must appear in the input XML — even as an error.**

**Why:** Editorial docx files are "generally filled with mistakes," but a mistake
that reaches the comp as visible/flagged content is fixable; content that silently
disappears is not — it's the exact failure mode the roundtrip was built to kill.
Comps can delete or restyle unwanted content; they can't recover what was never
emitted.

**How to apply:**
- Never silently `continue`/discard a content-bearing paragraph. The biggest
  offender today is `DocxBodyBuilder.preambleStart()`, which discards everything
  before the first `[Month]` — that's how the page-1 Wall art got dropped
  (see [[reference_chronicle_frontmatter]]).
- When placement is uncertain, EMIT into the most sensible slot (e.g. cover/front)
  AND raise a lint finding so the comp reviews it — emit-and-flag, never drop.
- Only truly empty paragraphs and known style-demo boilerplate may be skipped, and
  even those are better lint-noted than silently removed.
- Good invariant to enforce/test: every non-empty docx content paragraph maps to an
  emitted element (or an explicit flagged finding). A "content accounting" check
  (docx content count vs emitted+flagged) operationalizes this.
