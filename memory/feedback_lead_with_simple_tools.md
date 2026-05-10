---
name: When Jefe asks for a "simple/human" way, lead with the simplest tool
description: If Jefe explicitly frames an ask as wanting a simple or human approach, reach for base utilities (nc, scp, cat, cp) before composing modern stacks (SSH+clipboard, Tailscale, syncthing).
type: feedback
---

When Jefe asks for "a simple way to X" or "a human way to X," lead with the
plainest tool that works — `nc`, `scp`, `cat`, a shared file — not a clever
composition.

**Why:** During the Canvas install on 2026-05-10, Jefe needed to move a 43-char
TXT-record value from Thufir to Alia so he could paste it into a Bluehost form
without typos. He asked for "a simple HUMAN way." Fabian@thufir reached
straight for `wl-paste | ssh user@alia wl-copy` and got tangled in
Wayland-vs-X11 and missing-tools failure modes. Jefe used `nc` and was done in
two commands. He was visibly frustrated by the over-engineering. His note:
"it's easy for you to say type it because it's only blah blah blah, but i'm a
typesetter and copy/paste is 100% more accurate that my stupid fingers."

**How to apply:**
- When the framing is "simple" or "human," default to one of: `nc` for
  ad-hoc text/file transfer, `scp` for files, a shared file on a known path.
- Save Tailscale/Taildrop/syncthing/etc. for when he's asking about the
  *system* he should set up long-term, not when he wants a one-shot.
- Don't reach for clipboard-bridging stacks (`wl-paste | ssh ... wl-copy`)
  unless he specifically wants clipboard integration. They have too many
  failure modes (display server mismatch, missing tools on the remote) for a
  "simple" ask.
- The detail about being a typesetter is the heart of it: he uses copy/paste
  because his hands are unreliable at character-by-character accuracy. The
  *job* of the tool is to remove typo risk. Anything that adds steps or
  failure modes works against that.
