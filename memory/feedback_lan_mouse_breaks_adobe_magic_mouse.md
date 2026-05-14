---
name: lan-mouse interferes with Magic Mouse in Adobe apps
description: On the Mac mini, running lan-mouse caused the Magic Mouse to misbehave inside Adobe products; uninstalled 2026-05-14
type: feedback
---

Lan Mouse running as a daemon on the Mac mini (Macmini.lan) caused the Magic Mouse to misbehave inside Adobe products (Photoshop, etc.). Cool experiment, not viable for daily driving. Removed from Mac mini and Thufir on 2026-05-14.

**Why:** Lan Mouse intercepts pointer input at a low level to forward it across machines. On macOS this conflicts with Magic Mouse gesture/scroll handling inside Adobe apps specifically — Adobe's input pipeline is sensitive to that kind of injection.

**How to apply:** Don't suggest reinstalling lan-mouse on the Mac mini. If Jefe ever reports flaky Magic Mouse behavior in Adobe products in the future, check first for any pointer-forwarding or input-injection tool running as a daemon (lan-mouse, Synergy, Barrier, Input Leap, etc.) before chasing Adobe-side fixes.
