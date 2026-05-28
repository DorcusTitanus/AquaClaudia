---
name: epubcheck-workflow
description: "How to run epubcheck 4.2.1 on a zipped epub vs. an unzipped working tree, and how to correctly repackage"
metadata: 
  node_type: memory
  type: reference
  originSessionId: f9675d30-5405-4d26-b05b-6db42e1fe12e
---

epubcheck 4.2.1 jar (Ashley's pipeline copy):
`/Volumes/Ghola/Sync/Buttercup/zHappenHub/Converters/NorthAtlanticConverter_0412526/post_process_NABv05/xsl/epubcheck-4.2.1/epubcheck.jar`

**Validate a .epub file:**
`java -jar epubcheck.jar /path/to/book.epub`

**Validate an unzipped working tree** (META-INF/, OPS/, mimetype at root):
`java -jar epubcheck.jar --mode exp /path/to/dir`
Without `--mode exp` it errors out with "Mode required for non-epub files."

**Repackaging an unzipped tree into a valid .epub:**
```
cd <root>
zip -X0 out.epub mimetype           # stored, not deflated, FIRST entry
zip -Xr9D out.epub META-INF OPS -x "*.DS_Store"
```
- `mimetype` MUST be the first entry and stored uncompressed.
- Strip `.DS_Store` files before zipping (they're not errors, just noise).

When the source folder contains both a stale `.epub` and newer
unzipped `META-INF/OPS/` dirs (a typical Ashley workflow), the unzipped
tree is the source of truth — re-zip from it rather than editing inside
the stale epub.

See also [[epub-failure-modes-nab]] for the common error patterns
observed in NAB-pipeline epubs.
