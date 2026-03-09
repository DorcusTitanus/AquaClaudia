# AquaClaudia

Cross-platform context files for LLM-assisted course development.

Named for the Roman aqueduct completed by Emperor Claudius in 52 AD — a conduit that carried essential resources across distance.

## Purpose

These files provide project context to Claude (or any LLM) across devices and interfaces. They contain course design rules, project memory, and workflow conventions — no credentials, no infrastructure details, no secrets.

## Files

- `CLAUDE.md` — Course design rules and LLM operating constraints for MULTI 211
- `memory/MEMORY.md` — Project state, decisions, and conventions
- `memory/libretexts-oer-course.md` — LibreTexts OER project context

## Usage

### Claude phone app
Paste the raw GitHub URL into a conversation:
```
Fetch and internalize this context: [raw URL]
```

### Claude Code
These files are maintained locally in `~/.claude/projects/` and synced here. This repo is the portable mirror.

### Other LLMs
Prepend file contents to your system prompt or paste at conversation start.

## Security

This is a **sanitized public-safe mirror**. Infrastructure details (server credentials, API keys, database schemas, Canvas IDs, file paths) are stripped. The authoritative project memory with full details lives locally in Claude Code's memory directory.

Never commit credentials or infrastructure details to this repo.
