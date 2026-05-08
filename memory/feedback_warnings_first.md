---
name: Warnings and important info go at the start of a response
description: Put cautions, prerequisites, and destructive-command warnings BEFORE the command itself, not after — Jefe nearly ran a dd command before scrolling to see the "triple-check the disk" warning underneath.
type: feedback
originSessionId: a8143d1b-7aef-4cae-ada0-8e2def0270b3
---
Any warnings, cautions, prerequisites, or important context that affect what the user does next go at the **beginning** of the response, before the commands or steps themselves.

**Why:** On 2026-05-06 during the Thufir Hawat Linux install, I put a "⚠️ triple-check `/dev/rdisk7` before hitting return" warning after the `dd` command block. Jefe started running the command before scrolling far enough to see the warning. dd is irreversible — wrong disk = catastrophic data loss. The warning was useless where I put it.

**How to apply:** Whenever a response includes destructive, irreversible, or high-blast-radius operations (`dd`, `rm -rf`, `eraseDisk`, force pushes, drops, anything that can't be undone), lead with the warning. Same for prerequisites the user must verify ("make sure X is unmounted first," "this assumes Y is true") — surface them above the command block, not below. Generally: anything the user needs to know to do the action correctly belongs before the action, not after.
