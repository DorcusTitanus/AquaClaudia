---
name: Sync drive symlink convention
description: Cross-machine sync drives share a stable logical path via per-machine symlink
type: feedback
---

# Sync Drive Symlink Convention

## The Pattern
Each of Jefe's Macs has a symlink at `~/sync` pointing to the local sync drive's `Sync/` subdirectory:

- **Shai-Hulud (M5):** `~/sync → /Volumes/Stilgar/Sync`
- **Other Macs (Muad'Dib, etc.):** `~/sync → /Volumes/Ghola/Sync` (or whichever Dune-named drive lives on that machine)

## Why
Drives keep distinct Dune names (Stilgar, Ghola, etc.) so we can tell them apart, mount multiple at once for migrations, and avoid macOS's `/Volumes/Stilgar 1` collision behavior. The symlink absorbs the difference so paths in memory, scripts, and conversation stay machine-agnostic.

## How to Apply
- **In memory and prose:** write `~/sync/Documents/ClaudeCode/...`, not `/Volumes/Stilgar/...` or `/Volumes/Ghola/...`.
- **In Bash tool calls:** `~` expands normally — `cd ~/sync/Documents/...` works.
- **In tools needing absolute paths (Read, Write, Edit):** expand to `/Users/jefe/sync/Documents/...`. Jefe's username is `jefe` on all his Macs, so this is portable too.
- **Migration is incremental.** Existing memory files with `/Volumes/Stilgar/...` paths still work on Shai-Hulud. Update them opportunistically as we touch them, not in a mass rewrite.

## Setup Command (one-time per machine)
```bash
ln -s /Volumes/<DriveName>/Sync ~/sync
```

## Gotchas
- Claude Code's internal project directory (`~/.claude/projects/-Volumes-Stilgar-Sync-...`) is derived from the canonical filesystem path, not the symlink. Don't touch it — it still works.
- Some tools (git, Python's `pathlib.resolve()`) canonicalize symlinks to the underlying path. That's fine for execution; just be aware logs may show `/Volumes/Stilgar/...` instead of `~/sync/...`.
- If `~/sync` already exists on a machine before setup, check what's there before clobbering.

## Established
2026-05-15 on Shai-Hulud during ChroniCal work. Triggered by the realization that memory said `/Volumes/Ghola/...` but the live workspace was on `/Volumes/Stilgar/...`.
