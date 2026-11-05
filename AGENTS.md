# Zoom (Claude plugin)

This repository is a Claude Code plugin for Zoom users. It bundles one MCP server, the Zoom MCP Server (`zoom-mcp`, `https://mcp.zoom.us/mcp/zoom/streamable`), which includes agentic search, and two skills, `zoom-flagship-mcp` and `zoom-search`. It has no commands, agents or hooks, and it is not a developer toolkit. Developer content belongs in https://github.com/zoom/skills, not here: if a task needs SDK, REST API, webhook, OAuth or Marketplace-app knowledge, point the user or agent there.

Do not add other Zoom MCP servers (Meetings, Canvas, Chat, Tasks, Whiteboard, Phone and so on) or other skills without being asked. This plugin is deliberately scoped to the one server and its skills.

## Layout

- `.claude-plugin/plugin.json`: manifest (only `name` is required; keep it kebab-case). Skills load from the default `skills/` folder, so the manifest has no `skills` key.
- `.mcp.json`: the server, at the plugin root. Format: `{"mcpServers": {"<name>": {"type": "http", "url": "...", "headers": {"Authorization": "Bearer ${ENV_VAR}"}}}}`.
- `skills/zoom-flagship-mcp/`: the landing page. `SKILL.md` is the entry-point skill for Zoom requests; its "Start here: route the request" section sends each kind of request to a skill, and the rest of the file handles requests that are not routed (reads, doc creation, meeting writes). Keep its front matter (`name`, `description`) valid.
- `skills/zoom-search/`: Zoom's standalone `zoom-search` skill (metadata version 1.0.0), copied from the enterprise-rag source with four local edits listed in the 2.1.0 CHANGELOG entry (a connector exclusion in the description, an untrusted-content line, no shell command for the time zone, and no instruction to route around a direct connector). Send those edits upstream; when Zoom provides an update, replace the file and reapply any that upstream lacks. It owns all search guidance; the flagship delegates search to it and reports search as unavailable if it is missing. It keeps Zoom's trigger-style description, so Claude may invoke it directly as well as through the flagship.
- `TOOLS.md`: tool, scope and rate-limit reference. It was compiled from Zoom's published tool list; when the server changes, update it from https://developers.zoom.us/docs/mcp/zoom/.

Claude Code does not load a `CLAUDE.md` at a plugin's root as context, so do not rely on the files in this repository's root to instruct Claude at runtime. The skills are the only instruction text Claude loads from this plugin.

## Adding a routed skill

1. Create `skills/<name>/SKILL.md` with `name` and a `description` that says it is reached through `zoom-flagship-mcp`. Consider `user-invocable: false`, and avoid a trigger-style description: it lets Claude skip the landing page.
2. Add a row for it to the table in "Start here: route the request" in `skills/zoom-flagship-mcp/SKILL.md`, and say what happens if it is unavailable.
3. Keep search guidance in `zoom-search` and write actions in the flagship so its confirmation rules cover them. Follow the live tool schema for supported operations and parameters.
4. Describe it in `README.md`, add a `CHANGELOG.md` entry and bump `version` in `.claude-plugin/plugin.json`.

## When the server changes

1. Check https://developers.zoom.us/docs/mcp/zoom/ (the live `tools/list` response is the authority).
2. Update `TOOLS.md`, add a `CHANGELOG.md` entry and bump `version` in `.claude-plugin/plugin.json`.
3. Validate:

```bash
claude plugin validate .
claude --plugin-dir . mcp list
```
