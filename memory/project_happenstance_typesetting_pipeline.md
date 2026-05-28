---
name: happenstance-typesetting-pipeline
description: "Architecture of Jefe's existing Happenstance typesetting pipeline (InDesign + JSX + Perl + XSLT), the system the planned Swift app will replace"
metadata: 
  node_type: memory
  type: project
  originSessionId: f9675d30-5405-4d26-b05b-6db42e1fe12e
---

The current Happenstance typesetting pipeline that Jefe plans to rewrite
in Swift (next major project after [[chroncal-gui]]). Documenting state
as of 2026-05-27 from a first read of the codebase.

## Pipeline stages

1. **PRE-PROCESS** (Word → InDesign-ready XML)
   - `pre_process_xml_NABv05.pl` (~20 KB Perl, per client)
   - XSL: `Word_to_happenstanceID5_v18.xsl` maps Word styles to
     Happenstance ID5 tags; `HappenstanceID5_Features_v18.xsl` adds
     feature handling; `entities_external.xsl` for entities.
   - Output: XML that InDesign places with style mappings preloaded.

2. **LAYOUT** (in InDesign, by Happenstance compositors)
   - Manual typesetting; XML structure preserved as tagged content.
   - **This stage stays Adobe.** Swift can't replace InDesign layout.

3. **EXPORT FROM INDESIGN** (.indd/.indb → XML)
   - JSX exporters in `Scripts/ScriptsPanel_041423/`:
     - `BookExporter_041423/happenstanceXMLexporter_vBook.jsx` (loops a Book file's chapters)
     - `FileExporter_041423/happenstanceXMLexporter_vFile.jsx` (single .indd)
   - For each chapter: imports DTD, strips `^i` indent-here chars, walks
     the XML tree adding `<_PageNumber page="N"/>` markers each time the
     reading order crosses a page boundary (with table + anchored-frame
     handling, recursive `testChildren` for nested elements).
   - **At export**, applies `returnfinder_*.xsl` which performs the
     critical `translate(., '&#8233;&#x2029;&#x2028;', '&#xA;')` — this
     is where Jefe's U+2028/U+2029 problem gets fixed at the source.
     Any Swift replacement of stage 4 must keep this normalization in
     case XML comes from a non-Happenstance source.
   - Per-client DTDs: `Happenstance_v01.dtd` (200 KB, generic),
     `NABenstance_v01.dtd` (106 KB, NAB variant).

4. **POST-PROCESS** (XML → epub / Word / WileyML / "ALL")
   - `post_process_NABv05.pl` (**166 KB Perl, ~80 subroutines** — the
     heaviest unknown in the system). Top-level subs include:
     `epub`, `epub_01validate`, `epub_02verify`, `epub_03package`,
     `epub_04check`, `epub_splitChapters`, `epub_parts`,
     `epub_endnote_linker`, `epub_index_linker`, `build_the_epub`,
     `zip_the_epub`; `MSWord`; `WileyML` + `WileyML_*` family for the
     publisher delivery format; helpers like `charStripper`,
     `appendFigure`, `artRenamer`, `fixHappenstanceTables`,
     `try_gif`, `TypeORamaSymbol`.
   - Pipeline pattern: chained XSL transforms via `runXSL` (uses Saxon
     8 bundled at `xsl/saxon8/`), then Perl regex passes for the bits
     XSLT can't easily express.
   - Helper XSL: `xsl_epub/` (01_Move_Footnotes, 02_NumberNotes,
     03_Happenstance_to_epub); `xsl_word/` (01_Happenstance_to_Word_v8).
   - Static ebook assets: `ebooks_components/` (CSS template, cover,
     mimetype, META-INF, NAB-specific PNGs).
   - Validation: bundled `epubcheck-4.2.1` ([[epubcheck-workflow]]).
   - The `epub_endnote_linker` / `epub_index_linker` subs are where the
     RSC-012 anchor-injection problem I hand-fixed today
     ([[epub-failure-modes-nab]]) is *supposed* to happen — likely
     either silently failing or unwired for this title.

## Per-client model

NAB is one converter; the directory structure (`Converters/<Client>Converter_<date>/{pre,post}_process_*`) implies one full pipeline per client. DTD vocabularies are layered (Happenstance = base; NABenstance = NAB derivation). Swift rewrite needs a first-class **client configuration** concept: per-client DTD + XSL set + post-process rules.

## Implications / decisions for the Swift rewrite

- **Scope**: Swift app replaces stages 1 (pre) and 4 (post). Stages 2-3 stay Adobe; Swift can *orchestrate* InDesign via AppleScript / Scripting Bridge to invoke the existing JSX, but the JSX itself probably keeps running in ExtendScript since it must live inside InDesign.
- **XSLT runtime**: existing code uses Saxon 8 (XSLT 2.0). Options: (a) keep Saxon as subprocess from Swift (fastest path to parity); (b) port stylesheets to Swift XML walkers (long-term, more maintainable); (c) libxml2 + libxslt (XSLT 1.0 only — won't run existing 2.0 stylesheets).
- **Perl-to-Swift**: the 166 KB perl is the migration risk. Best path is probably to (1) deeply catalog every sub and what it transforms, (2) write a regression harness that runs both old (perl) and new (Swift) pipelines on the same input and diffs outputs, (3) port subroutine-by-subroutine. Pattern matches the ChronCal "no big-bang" feedback ([[chroncal-iterative-not-oneshot]]).
- **Training data**: TBs of past titles on the Sync.com drive give a regression corpus — any Swift sub should be tested against N historical inputs and outputs.

## Per-client variation visible already

- NAB: tags `FeatureBoxSidebar`, `FeatureBoxCustom` appear in `vFile.jsx` but not `vBook.jsx`.
- File exporter prompts for 6-digit job number; Book exporter pulls it from filename's first 6 chars.
- Suggests the JSX layer also varies per client; that bears auditing across all clients before assuming "two exporters."

## What's NOT yet read in detail

- `post_process_NABv05.pl` beyond the subroutine list — the actual logic in `epub_endnote_linker`, `epub_splitChapters`, `build_the_epub`, the WileyML family.
- The `pre_process_xml_NABv05.pl` script.
- The DTDs (200 KB and 106 KB — likely the right way to read them is via examples of valid documents, not the DTDs themselves).
- Any other clients beyond NAB (path `Converters/` should be enumerated).
- The `HappenstanceXref_v001.jsx` cross-reference handler.

See [[epubcheck-workflow]] and [[epub-failure-modes-nab]] for the
epub-validation pieces already documented.
