---
name: Canvas hides controls at narrow browser widths
description: Before chasing "missing button" or "expected control isn't there" bugs in Canvas, widen the browser window — Canvas's responsive layout hides things at narrow widths instead of wrapping.
type: feedback
---

Canvas's responsive layout HIDES controls at narrow browser widths rather than wrapping or reflowing. Toggles, action buttons, and admin controls can vanish entirely until the window is wider.

**Why:** Observed 2026-05-10 during the LTI 1.3 developer-key creation. The enable/disable toggle on a newly created key did not appear in the row's Type column until Jefe widened his browser. We almost spent debugging time on a non-bug.

**How to apply:** When Jefe (or a student) reports "I don't see the X button" or "where is the Y toggle" in Canvas, the FIRST check is "widen the browser window or maximize it." Only after confirming the control is still missing at full width should you look for permission issues, feature flags, or actual bugs. Applies equally to admin pages, course settings, and the gradebook.
