---
name: find through ~/sync symlink silently returns empty
description: BSD find on macOS doesn't traverse a symlink when it IS the starting path — use trailing slash or -L
type: feedback
---

# `find ~/sync` Silently Returns Empty

## The Trap
macOS uses BSD `find`, which by default does **not** follow symlinks. When the symlink is the **starting path** (not just encountered mid-walk), find treats it as a single file, traverses nothing inside it, and exits 0 with empty output. No error, no warning. You think the file doesn't exist; it just wasn't searched.

This bit me on 2026-05-15 right after I introduced the `~/sync` symlink convention. Hunted for `2028WallBlank.docx`, find returned nothing, I told Jefe the file didn't exist. Jefe pointed at the file. The file was always there — find just refused to look.

## How to Apply
When searching anywhere under `~/sync`, **always** use one of:

```bash
find -L ~/sync ...           # follow all symlinks (explicit)
find ~/sync/ ...             # trailing slash forces dereference (terse)
find /Volumes/Stilgar/Sync ...  # canonical path (machine-specific, defeats the symlink point)
```

Default to **trailing slash** — minimal cognitive overhead, works for any tool that respects the convention.

## Also Affects
- Some `ls` invocations (less commonly — `ls ~/sync` works fine because `ls` of a symlink to a dir typically lists its contents)
- `du`, `tar`, `rsync` — check their `-L` / `--dereference` flags before running through a symlinked root
- `rg`/`ripgrep` — also doesn't follow symlinks by default; use `--follow`

## Why
BSD find: "If no `-H`, `-L`, or `-P` is specified, default is `-P` (do not follow symbolic links)." This is intentional for safety (avoids loops, doesn't accidentally walk into unintended trees). The cost is silent search failure when the convention itself involves symlinks.

## Lesson
Introducing a path convention has second-order tooling implications. Test the tools you'll use against the convention before declaring it done.
