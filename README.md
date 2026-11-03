# Zoom for Claude

Use your own Zoom account from Claude Code. The plugin connects Claude to the Zoom MCP server, so you can ask in plain language and Claude searches, answers questions about and works with your meetings, recordings, notes and docs.

It bundles one MCP server and two skills that teach Claude how to use it. It has no commands, agents or hooks.

## Need developer skills?

This plugin is for *using* Zoom. If you or an agent need to *build* with Zoom (SDKs, REST API, webhooks, OAuth, bots, Marketplace apps, AI Services), use Zoom's developer skills instead: **https://github.com/zoom/skills**. In Claude Code:

```text
/plugin marketplace add zoom/skills
/plugin install zoom-skills@zoom-skills
```

## What you can ask for

- **Search and Q&A (agentic search):** search across your Zoom content, or ask a question and get an answer with citations. The skills document meetings, notes, chat, Docs, Sheets, calendar, Zoom Phone records and apps connected through Zoom. What you can reach depends on your Zoom account and licenses.
- **Meetings:** find a meeting by topic, read its summary and assets, schedule, change or cancel meetings, and use in-meeting controls.
- **Recordings:** list your cloud recordings and read their transcripts and other resources.
- **My Notes:** read your Zoom notes.
- **Docs, Sheets and Hub:** read and create Zoom Docs, Paper docs and spreadsheets, and manage who a doc is shared with.

Example prompts:

```text
Find my Zoom meetings about the budget and summarize the decisions.
Ask Zoom what we agreed with the design team last week.
Create a Zoom Doc with the action items from my last meeting.
```

## The skills

Two skills teach Claude how to use the server. They are instructions only, and they only work while the Zoom MCP server below is connected. `zoom-search` is read-only; `zoom-flagship-mcp` also covers actions that change data, under its confirmation rules.

- [`zoom-flagship-mcp`](skills/zoom-flagship-mcp/SKILL.md) is the entry point. Claude loads it on its own when a question depends on what you discussed, decided, scheduled or were sent. It routes the request: search and Ask work goes to `zoom-search`, and the rest it handles itself. That covers which tool and datasource to use for meetings, recordings, notes, chat, Docs, Sheets, calendar and Zoom Phone, how to read and create docs, and how to schedule, change and cancel meetings and manage doc collaborators. Its write rules say to resolve the exact target first and to confirm deletes, collaborator removals and in-meeting controls before running them.
- [`zoom-search`](skills/zoom-search/SKILL.md) holds the detailed guidance for the three search tools (`search`, `search_describe_capabilities`, `ask`): datasources, filters, paging, third-party apps, time zones and `ask` errors. It is Zoom's search skill, included unmodified. `zoom-flagship-mcp` routes search and Ask work to it.

In Claude Code they appear as `zoom-plugin:zoom-flagship-mcp` and `zoom-plugin:zoom-search`.

## Install

Load the plugin from a local checkout:

```bash
claude --plugin-dir /path/to/zoom-mcp-plugin-for-claude
```

Or add the directory to a marketplace you manage and run `/plugin install zoom-plugin@<your-marketplace>`. Check the manifest any time with `claude plugin validate /path/to/zoom-mcp-plugin-for-claude`.

## Connect your Zoom account

Zoom's MCP server uses OAuth 2.1 with PKCE and only supports manual client registration, not dynamic client registration. Claude Code's built-in sign-in therefore cannot register itself with Zoom automatically. Pick one route.

**Route 1: a Claude connector (recommended for everyday use).** In Claude on the web or desktop, open **Customize > Connectors > Add custom connector**. Enter the server URL `https://mcp.zoom.us/mcp/zoom/streamable` and your Zoom app's OAuth Client ID and Secret, and add `https://claude.ai/api/mcp/auth_callback` as a redirect URL on the Zoom app. Claude handles sign-in and token refresh. Connectors you add this way also show up in Claude Code when you are signed in with the same claude.ai account. If you use this route, switch off the plugin's own `zoom-mcp` server in `/mcp` so you do not get two.

**Route 2: this plugin's bundled server with an access token.** [`.mcp.json`](.mcp.json) registers the server and reads a bearer token from the environment. You need a Zoom account that is allowed to create apps (admin or developer role):

1. In the [Zoom App Marketplace](https://marketplace.zoom.us/), choose **Develop > Build App** and create a **General app** that uses user-level OAuth.
2. Under **Scopes**, add the scopes you need. [`TOOLS.md`](TOOLS.md) lists all 19 and a read-only set. Search needs `agentic_search:read:search`, and `ask` needs `agentic_search:read:ask`.
3. Register a redirect URL on the app (for example `http://localhost:8080/callback`), authorize at `https://zoom.us/oauth/authorize`, and exchange the code at `https://zoom.us/oauth/token`, authenticating with your app's client ID and secret (HTTP Basic). Zoom requires PKCE, so send a `code_verifier`.
4. Export the access token, then start Claude Code:

```bash
export ZOOM_MCP_ACCESS_TOKEN="your_access_token"
```

Zoom access tokens last about an hour, so refresh the token and restart Claude Code when the server starts returning `401`. Run `claude mcp list` to see whether the server connected.

## Stay in control of write actions

Some tools change or delete things, such as `meeting_delete`, `meeting_update` and `hub_create_file_from_content`. Claude Code names plugin tools `mcp__plugin_<plugin>_<server>__<tool>`, so you can require approval for specific ones in `settings.json`:

```json
{
  "permissions": {
    "ask": [
      "mcp__plugin_zoom-plugin_zoom-mcp__meeting_create",
      "mcp__plugin_zoom-plugin_zoom-mcp__meeting_update",
      "mcp__plugin_zoom-plugin_zoom-mcp__meeting_delete",
      "mcp__plugin_zoom-plugin_zoom-mcp__in_meeting_control"
    ]
  }
}
```

[`TOOLS.md`](TOOLS.md) lists every tool that changes data. Requesting only the read-only scopes is the safest setup.

## Files

```text
.claude-plugin/plugin.json   plugin manifest
.mcp.json                    the Zoom MCP server
skills/zoom-flagship-mcp/    entry-point skill: routes requests
skills/zoom-search/          Zoom's search skill, unmodified
TOOLS.md                     tools, scopes and rate limits
```

## License

MIT
