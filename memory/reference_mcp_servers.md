# Reference — MCP Servers in Jefe's Workflow

**Status:** 2026-05-15, MCP becoming a major part of the workflow.
**Sibling memo:** [livestream-production.md](livestream-production.md) —
where OBS/Stream Deck MCP fits into the show stack.

## Why this memo

MCP (Model Context Protocol) is the open standard that lets Claude (and
other AI agents) plug into apps as tools. Jefe sees it growing into a
major part of his production workflow — calendar typesetting (Xcode),
live music streaming (OBS, Stream Deck), and more to come. Capture the
servers as they get adopted so future instances don't re-research from
scratch each time.

## Setup pattern (all MCP servers)

```
# User-scope so it's available from any project directory:
claude mcp add --scope user --transport stdio <name> -- <command-or-binary>

# Verify:
claude mcp list

# Remove:
claude mcp remove <name>
```

Config lands in `~/.claude.json` (or scope-specific equivalent). After
`add`, **restart the Claude Code session** — MCP tool surfaces load at
session start, not mid-session.

To discover candidates from within a session:
`mcp__mcp-registry__search_mcp_registry` (Anthropic-curated) or web
search for community options.

## Confirmed in use

### Xcode (Apple, official)
- **Source:** Built into Xcode 26.3+. Binary: `xcrun mcpbridge`.
- **Toggle:** Xcode → Settings → Intelligence → Model Context Protocol →
  "Allow external agents to use Xcode tools."
- **Install:** `claude mcp add --scope user --transport stdio xcode -- xcrun mcpbridge`
- **Capabilities:** ~20 tools — BuildProject, RunAllTests/RunSomeTests,
  RenderPreview (SwiftUI), DocumentationSearch (Apple docs),
  GetCurrentFile, Glob/Grep/LS/Read/Write/MV/RM/MakeDir,
  ListNavigatorIssues, RefreshCodeIssuesInFile, ExecuteSnippet,
  ListWindows, GetBuildLog, GetTestList.
- **Sessions can disconnect** when Xcode closes the project — re-toggle
  in settings and reconnect via `claude mcp list`.

## Worth setting up (production workflow targets)

### OBS Studio (community)
Three viable options; pick one. All wrap OBS's built-in WebSocket protocol.

| Server | Stack | Surface | Notes |
|---|---|---|---|
| `sbroenne/mcp-server-obs` | .NET 10 | 81 tools, 9 groups | Most comprehensive |
| `royshil/obs-mcp` | Node | Smaller surface | Popular community pick |
| `ironystock/agentic-obs` | Python | MCP-native design | Cleanest Python install |

**Prereq:** Enable OBS WebSocket in OBS → Tools → WebSocket Server Settings.
**Use:** scene/source/recording/streaming/audio control via Claude prompts.

### Stream Deck (Elgato, official as of 7.4, April 2026)
- **Official binary:** "Elgato MCP Server" — install via the Stream Deck app.
- **Enable:** Stream Deck app → MCP settings → opt actions into the
  dedicated "MCP Actions" profile.
- **Capability model:** trigger pre-bound Stream Deck actions by name —
  good for action chains like "get my stream ready" → opens OBS, sets
  scene, lights on, music up.
- **Community alternative:** `verygoodplugins/streamdeck-mcp` —
  reads/writes Stream Deck profile files directly. Build themed decks
  programmatically (per-project layouts, app-specific boards) instead of
  hand-clicking in the GUI.

### Combined: Stream Deck + OBS
The pairing is the high-leverage move for livestream production:
voice/text → Stream Deck MCP triggers a chain → OBS MCP does the scene
work. Single prompt orchestrates show start, scene transitions,
recording, even Bunny.net push controls (if separately wired).

## Anthropic-curated (one-click in Claude.ai)

These show up in `mcp__mcp-registry__list_connectors`. Already present
but listed here for completeness:

- Google Drive / Gmail / Google Calendar — surface as auth-required.
  **Per secops boundaries, leave these disconnected.** Jefe's intentional.

## Setup checklist (when adopting a new MCP server)

1. Find/choose the server (registry search, GitHub, vendor docs).
2. Install the server binary/runtime per its README (`npm i -g`,
   `pip install`, `.dmg`, `cargo install`, etc.).
3. Run `claude mcp add --scope user --transport stdio <name> -- <cmd>`
   (or `--transport http <url>` for hosted).
4. `claude mcp list` to confirm `✓ Connected`.
5. Restart Claude Code session.
6. Smoke-test one tool from the new surface before relying on it.
7. **Update this memo** with the new entry so other instances can find it.

## Gotchas

- **Session restart required** after `mcp add` — current session won't
  see new tools, only future sessions.
- **Scope matters:** default scope is project-local (writes to the
  current project's `.claude.json`). Use `--scope user` for global
  availability.
- **MCP servers disconnect** when their host app closes (e.g., Xcode
  quitting, OBS closing). The session shows them as disconnected;
  reconnect by relaunching the host and (sometimes) running
  `claude mcp list` to re-probe.
- **Race conditions in multi-session work** — see
  `feedback_commit_only_in_multi_session.md` for the staging-area issue
  when two Claude sessions touch the same repo simultaneously.
