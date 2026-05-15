---
name: Use `git commit -o <files>` when other claude sessions may be running
description: Multi-session work in the same repo creates staging-area race conditions; `git add <file>` + `git commit` can sweep in unrelated work
---

# Multi-session commits — use `-o` to scope the commit

## What happened

While working in `/Users/jefe/memory/public/` (symlinked into the
AquaClaudia repo), another claude session was concurrently creating
feedback memo files in the same directory. The pattern:

1. I ran `git status` — saw 5 untracked files (not mine), all clean
2. I wrote my own memo
3. I ran `git add MEMORY.md project_chronical_paradigms.md`
4. I ran `git commit -m "..."`
5. Commit swept in **7 files** instead of the 2 I named.

The other session had run `git add` on its files between my steps 1 and
4, so when I committed, the entire index — mine + theirs — landed in one
commit under my message.

## Why

`git commit` (without `-o`) commits everything currently in the index,
not just the files passed to `git add` in this shell. A second process
adding things to the index races freely.

## How to apply

When Jefe says "another session is running" or any time the repo isn't
exclusively yours, do:

```
git commit -o file1 file2 -m "msg"
```

The `-o` ("only") flag commits **only** the listed files, even if other
things are staged. Other work stays in the index for the other session
to commit on its own terms.

Same principle for amends, reverts, etc. — scope every git mutation to
the files you actually own.

## Don't

- `git add -A` or `git add .` — sweeps everything indiscriminately
- `git commit -a` — same problem, includes all tracked-and-modified
- `git stash` to "clear the way" — actively destroys the other session's
  in-progress state

## Do

- `git commit -o <files> -m "..."` for scoped commits
- `git diff --staged <file>` to verify exactly what's about to land
- Coordinate with the other session before sweeping operations
