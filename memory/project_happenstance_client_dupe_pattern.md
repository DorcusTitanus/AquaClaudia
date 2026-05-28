---
name: happenstance-client-dupe-pattern
description: "How per-client converters are produced today (fork-and-edit, no real abstraction) and what that means for the Swift rewrite"
metadata: 
  node_type: memory
  type: project
  originSessionId: f9675d30-5405-4d26-b05b-6db42e1fe12e
---

The way Happenstance handles per-client variation in the current
pipeline: **duplicate the previous client's converter directory and
hand-edit it.** No shared library, no real abstraction. Confirmed by
Jefe 2026-05-27: "We just dupe the old file and update for new clients."

Consequences observed in `post_process_NABv05.pl`:

- 36 per-series stub positions (`preBOT_<size>`, `postBOT_<size>`,
  `preEPUB_<size>`, `postEPUB_<size>` for 9 trim sizes) — but only
  `preBOT_6x9` is non-empty. The "series strategy" wasn't an
  abandoned architecture so much as a forked artifact that nobody
  trimmed because the cost of leaving it was low.
- "Trim size" matters for PRE-process (mono/code character counts
  must fit a fixed line width in print), but NOT for POST-process
  (epubs are reflowable). So the per-trim-size dispatch in the
  post-process script never had a real reason to exist; it was a
  copy-paste fossil.
- Genuinely dead Wiley-specific subs (`appendFigure`, `try_gif`,
  most of the WileyML family) sat in the file for years because
  removing them was never worth the risk.

For the Swift rewrite:

- **Don't replicate fork-and-edit.** Design a real client-config
  concept from day one: a small declarative config (YAML/JSON/Swift
  struct) per client describing tag vocabulary, output formats,
  asset paths, post-process toggles. Same pattern Jefe converged on
  for ChronCal's per-title design toggles
  ([[chroncal-iterative-not-oneshot]]).
- **PRE and POST are different worlds.** PRE cares about trim size
  (typography). POST does not (reflowable). Don't share a "series"
  concept across the two — model them separately.
- **Be conservative about WHICH dead code is actually dead.**
  Jefe confirmed 2026-05-27 that `appendFigure` and `try_gif`
  are genuinely dead and that "there is a tremendous amount of dead
  code." But because the dupe pattern means dead code in NAB's
  converter could still be live in some other client's converter,
  the right verification is to enumerate every client under
  `Converters/` and grep for the function name before declaring it
  globally dead.

Related: [[happenstance-typesetting-pipeline]],
[[happenstance-post-process-findings]].
