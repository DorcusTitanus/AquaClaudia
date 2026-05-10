---
name: ! prefix and && don't compose
description: The `!` slash-prefix shell only runs the first command on the line; `&&`-chained tails are dropped (and may be reparsed as new commands).
type: feedback
---

The `!` slash-prefix shell in Claude Code does NOT compose with `&&`. When the user types `! cmd1 && cmd2`, only `cmd1` runs; `cmd2` is silently dropped or, worse, reparsed as a standalone command on the next line.

**Why:** Observed 2026-05-10 during Canvas install on **Thufir (Ubuntu 24.04, bash)** — I sent `! cd ~/claudeCode/projects/canvas-self-hosted && docker compose build ...` twice and only the `cd` ran. The orphaned tail then surfaced as `--progress=plain: command not found`, which was confusing because it looked unrelated to my failed build.

**Scope:** Confirmed only on Ubuntu/bash (Thufir). Behavior on macOS (zsh on M1, or bash on the older Macs) is UNTESTED — may or may not be the same. The `!` prefix is a Claude Code feature, not a shell feature, so it could be either the harness or the shell at fault. If working on a Mac, test `! echo a && echo b` once to confirm before assuming.

**How to apply:** When proposing a `! <cmd>` line on Thufir, send ONE shell command — no `&&` chains. If a specific cwd matters, don't bake `cd` into the bang line; ask Jefe what dir he's in, or send `cd` as its own bang line first. Default: ask before issuing a path-sensitive command.
