---
name: Read project memory before editing tool configs
description: For tools with a project-memory file (lan-mouse, etc.), read that file before writing or editing the tool's config — schemas in training data may be stale.
type: feedback
---

When a tool has a dedicated project-memory file (e.g., `lan-mouse-setup.md`), read it **before** writing or modifying that tool's config. The canonical schema for the version actually installed lives there, and may differ from what's in training data.

**Why:** On 2026-05-08, lan-mouse on Thufir died and Fabian wrote a `[[clients]]` array config from pre-v0.10 docs. v0.10.0 silently ignores that schema, falling back to a placeholder client (no log error). The real schema is named position blocks (`[bottom]` / `[top]` / etc.) with `ips = [...]` and `activate_on_startup`. claude@alia had to take over the diagnosis and recompile to fix it. The lan-mouse project memory at `~/memory/private/lan-mouse-setup.md` documents the v0.10 schema explicitly — Fabian had pulled that repo earlier in the session but didn't consult it before writing the config.

**How to apply:**
- Before writing/editing a tool's config file, grep `~/memory/` for the tool name. If a setup file exists, read it.
- If the tool's project memory documents a config schema, copy from there rather than reconstructing from memory.
- "I know how this tool's config works" is a yellow flag for tools that may have changed major versions. Verify the installed version (`--version`, Cargo.toml, etc.) and confirm the schema matches.
- Don't rely on the absence of error logs to mean the config loaded correctly. Some tools silently fall back to defaults.
