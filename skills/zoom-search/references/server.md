# Zoom Search: server, scopes, and setup

## Where the tools live

The three Search tools are served by the Zoom MCP Server, Streamable HTTP:

`https://mcp.zoom.us/mcp/zoom/streamable`

Note the `/mcp/` path segment.

## Tools, scopes, and REST equivalents

| Tool | OAuth scope | REST equivalent |
|------|-------------|-----------------|
| `ask` | `agentic_search:read:ask` | `POST /v2/ai/agentic-search/ask` |
| `search` | `agentic_search:read:search` | `POST /v2/ai/agentic-search/search` |
| `search_describe_capabilities` | `agentic_search:read:search` | `GET /v2/ai/agentic-search/search-describe-capabilities` |

- `ask` has its own scope. A token minted with only `agentic_search:read:search` can search and
  discover capabilities, but should not be expected to work for `ask`.
- A missing scope or license on `ask` is handled as in [SKILL.md](../SKILL.md) section 6 (403 is not transient).
- The REST endpoints, if available to you, are an alternative when no MCP connector is available; they
  need an OAuth token with the same scopes. The paths above are carried over from Zoom's search skill.
  They did not appear in Zoom's public API reference or scope list when checked on 2026-11-03, so
  confirm them before relying on them.

## Setup

1. Create or reuse a Zoom user-level OAuth app and add both scopes above.
2. Register `https://mcp.zoom.us/mcp/zoom/streamable` as an MCP server in the client, using a
   user token that carries those scopes.
3. Run `tools/list` and confirm `ask`, `search`, and `search_describe_capabilities` are present.
   Match tools by capability and schema, as the `zoom-flagship-mcp` skill describes; the MCP prefix differs per install.

Registration and token steps for this plugin are in its README ("Connect your Zoom account"). All the scopes the server uses are listed in `TOOLS.md` at the plugin root.
