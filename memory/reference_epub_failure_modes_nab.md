---
name: epub-failure-modes-nab
description: "Recurring epubcheck error patterns observed in North Atlantic Books pipeline epubs (Ashley's workflow); future Happenstance Typesetting validator/autofix should target these"
metadata: 
  node_type: memory
  type: reference
  originSessionId: f9675d30-5405-4d26-b05b-6db42e1fe12e
---

Catalog of failure modes seen in a real NAB epub (Write Yourself Home,
ISBN 9798889843634, fixed 2026-05-27). Useful as seed cases for the
Happenstance Typesetting epub validator and autofix logic.

## 1. RSC-005: U+2028 LINE SEPARATOR inside XHTML
Invisible Unicode line separators (` `, bytes `E2 80 A8`) embedded
in `toc.xhtml` (likely from a Word/InDesign export). The XHTML parser
treats them as text content inside `<ol>`, which is invalid → "text not
allowed here; expected ... element 'li'."

Fix: `perl -i -CSD -pe 's/\x{2028}//g' file.xhtml`
Detect: `grep -c $'\xe2\x80\xa8' file`

## 2. RSC-005: Mis-nested `<li>` at part-boundary transitions
Each `<li>` for `PART III/IV/V/VI` was missing its closing `</li>` after
its inner `</ol>`. Pattern in source:
```
</ol>
</li></ol>           ← extra </ol> closes inner; </li> for PART X never appears
<li>PART X+1...
```
Result: next PART's `<li>` nested inside the prior one, cascading errors
that the parser reports far downstream (e.g. b02-b06 entries flagged
hundreds of lines later).

Fix: insert `</li>` after the inner `</ol>` so each part is a sibling
under the outer `<ol>`.

## 3. RSC-011: Manifest items missing from spine
`p03.xhtml`-`p06.xhtml` existed on disk and in `<manifest>` but had no
`<itemref>` in `<spine>`. The toc linked to them, so every link became a
"reference to a resource that is not a spine item" error.

Fix: insert `<itemref idref="xpNN"/>` in the spine before each part's
first chapter. Validator rule: every toc/nav `href` that resolves to a
manifest item must also have an `<itemref>` in `<spine>`.

## 4. RSC-012: Endnote anchors missing from rearnote asides
Each chapter superscript linked to `b02.xhtml#cXX-endnote-NNN`, but
every `<aside class="note" epub:type="rearnote">` in `b02.xhtml` was
emitted WITHOUT any `id` attribute. 22 dangling refs.

Fix (1:1 ordered injection): the Nth aside in source order corresponds
to the Nth endnote ref (also in spine-reading-order across chapters).
Walking both lists in parallel and writing the id into the aside opener
fixes all of them.

## 5. `[placeholder].xhtml` literal hrefs
Earlier draft of the pipeline emitted `href="[placeholder].xhtml#..."`
as a literal string when a target file resolution step failed silently.
Worth detecting with a regex pass: any `href="\[.*?\]"` is a smoking gun.

## Implications for Happenstance Typesetting validator
- Pre-validate XHTML files for U+2028, U+2029, BOMs inside body, and
  other invisible-character traps before running epubcheck — they
  produce confusing downstream errors.
- After generating spine + manifest, cross-check that the set of toc
  hrefs ⊆ set of manifest hrefs AND set of spine items.
- After generating endnote/rearnote asides, verify every cross-file
  fragment href has a matching `id` in the target file.
- Autofix can safely add missing `</li>` at part boundaries when the
  parent `<ol>` is unambiguous, but human review is still safer.

See [[epubcheck-workflow]] for tooling commands.
