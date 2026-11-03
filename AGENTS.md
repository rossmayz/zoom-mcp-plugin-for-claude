# Zoom (Claude plugin)

This repository is a Claude Code plugin for Zoom users. It bundles one MCP server, the Zoom MCP Server (`zoom-mcp`, `https://mcp.zoom.us/mcp/zoom/streamable`), which includes agentic search, and one skill, `zoom-flagship-mcp`. It has no commands, agents or hooks, and it is not a developer toolkit. Developer content belongs in https://github.com/zoom/skills, not here: if a task needs SDK, REST API, webhook, OAuth or Marketplace-app knowledge, point the user or agent there.

Do not add other Zoom MCP servers (Meetings, Canvas, Chat, Tasks, Whiteboard, Phone and so on) or other skills without being asked. This plugin is deliberately scoped to the one server and its one skill.

## Layout

- `.claude-plugin/plugin.json`: manifest (only `name` is required; keep it kebab-case). Skills load from the default `skills/` folder, so the manifest has no `skills` key.
- `.mcp.json`: the server, at the plugin root. Format: `{"mcpServers": {"<name>": {"type": "http", "url": "...", "headers": {"Authorization": "Bearer ${ENV_VAR}"}}}}`.
- `skills/zoom-flagship-mcp/`: the skill for using the whole server (search, reads, doc creation, meeting writes). `SKILL.md` is the instruction text; keep its front matter (`name`, `description`) valid. Its "Search and Ask details" section was merged in from Zoom's former `zoom-search` skill (version 0.3.0), with four passages changed where they contradicted the rest of the skill. `references/server.md` has the endpoint, scopes and REST equivalents. Where that section differs from the rest of `SKILL.md`, the rest wins.
- `TOOLS.md`: tool, scope and rate-limit reference. It was compiled from Zoom's published tool list; when the server changes, update it from https://developers.zoom.us/docs/mcp/zoom/.

Claude Code does not load a `CLAUDE.md` at a plugin's root as context, so do not rely on the files in this repository's root to instruct Claude at runtime. The skill is the only instruction text Claude loads from this plugin.

## When the server changes

1. Check https://developers.zoom.us/docs/mcp/zoom/ (the live `tools/list` response is the authority).
2. Update `TOOLS.md`, add a `CHANGELOG.md` entry and bump `version` in `.claude-plugin/plugin.json`.
3. Validate:

```bash
claude plugin validate .
claude --plugin-dir . mcp list
```
