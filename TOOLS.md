# Zoom MCP Server

Tool and scope reference for the one MCP server this plugin bundles. It was checked against the official [Zoom MCP server page](https://developers.zoom.us/docs/mcp/zoom/) on 2026-11-03. Zoom changes the server independently of this plugin, so treat your client's live MCP `tools/list` response as the authority for tool names and schemas.

- Plugin server name: `zoom-mcp`
- Endpoint: `https://mcp.zoom.us/mcp/zoom/streamable` (Streamable HTTP)
- Token variable: `ZOOM_MCP_ACCESS_TOKEN`
- Tools: 20
- Authentication: Zoom user-level OAuth (OAuth 2.1 with PKCE). Zoom supports only manual client registration.

## Agentic search

Three tools search and answer across your Zoom content:

- `search`: returns matching records.
- `search_describe_capabilities`: lists the datasources and filter fields available to you.
- `ask`: returns one synthesized answer with citations.

They need `agentic_search:read:search` and `agentic_search:read:ask`. The bundled [`zoom-flagship-mcp` skill](skills/zoom-flagship-mcp/SKILL.md) is the entry point that tells Claude which tool and datasource to use. It routes search and Ask work to the [`zoom-search` skill](skills/zoom-search/SKILL.md). Results are limited to what your Zoom account can already see.

## Tools

Rate limits are applied per account and shared with Zoom's Open APIs. Tools are tiered `LIGHT`, `MEDIUM` or `HEAVY`; Docs and Hub import and export use their own `CANVAS-IMPORT` and `CANVAS-EXPORT` tiers.

| Area | Tool | Required scope | Rate limit |
|---|---|---|---|
| Search and Q&A | `ask` | `agentic_search:read:ask` | LIGHT |
| Search and Q&A | `search` | `agentic_search:read:search` | LIGHT |
| Search and Q&A | `search_describe_capabilities` | `agentic_search:read:search` | LIGHT |
| Meetings | `get_meeting_assets` | `meeting:read:assets` | LIGHT |
| Meetings | `in_meeting_control` | `meeting:update:in_meeting_controls` | MEDIUM |
| Meetings | `meeting_create` | `meeting:write:meeting` | LIGHT |
| Meetings | `meeting_delete` | `meeting:delete:meeting` | LIGHT |
| Meetings | `meeting_update` | `meeting:update:meeting` | LIGHT |
| Meetings | `search_meetings` | `meeting:read:search` | LIGHT |
| Recordings | `get_recording_resource` | `cloud_recording:read:content` | MEDIUM |
| Recordings | `recordings_list` | `cloud_recording:read:list_user_recordings` | MEDIUM |
| My Notes | `my_notes_get_note_content` | `my_notes:read:content` | MEDIUM |
| Docs | `canvas_add_file_collaborators` | `docs:write:collaborator` | MEDIUM |
| Docs | `canvas_list_file_collaborators` | `docs:read:list_file_collaborators` | MEDIUM |
| Docs | `canvas_modify_file_collaborator_role` | `docs:update:collaborator` | MEDIUM |
| Docs | `canvas_remove_file_collaborator` | `docs:delete:collaborator` | MEDIUM |
| Docs | `create_new_file_with_markdown` | `docs:write:import` | CANVAS-IMPORT |
| Docs | `get_file_content` | `docs:read:export` | CANVAS-EXPORT |
| Hub | `hub_create_file_from_content` | `hub:write:content` | CANVAS-IMPORT |
| Hub | `hub_get_file_content` | `hub:read:content` | CANVAS-EXPORT |

## Scopes to request on your Zoom app

Request only what you plan to use. All 19 scopes the tools need:

```text
agentic_search:read:ask
agentic_search:read:search
cloud_recording:read:content
cloud_recording:read:list_user_recordings
docs:delete:collaborator
docs:read:export
docs:read:list_file_collaborators
docs:update:collaborator
docs:write:collaborator
docs:write:import
hub:read:content
hub:write:content
meeting:delete:meeting
meeting:read:assets
meeting:read:search
meeting:update:in_meeting_controls
meeting:update:meeting
meeting:write:meeting
my_notes:read:content
```

**Read-only set** (search, ask, read meetings, recordings, notes and docs; nothing can be changed):

```text
agentic_search:read:ask
agentic_search:read:search
cloud_recording:read:content
cloud_recording:read:list_user_recordings
docs:read:export
docs:read:list_file_collaborators
hub:read:content
meeting:read:assets
meeting:read:search
my_notes:read:content
```

The server also advertises three workflow scopes (`workflow:read:definition`, `workflow:read:list_definitions`, `workflow:write:definition`). No tool in the current list uses them, so you do not need them today.

## Deprecated tools

The `zoom-flagship-mcp` skill treats `search_meetings` and `create_new_file_with_markdown` as deprecated, in favor of `search` and `hub_create_file_from_content`. Zoom's server page still lists both.

## Tools that change things

These create, update or delete data. Consider requiring approval for them (see the README):

- `canvas_add_file_collaborators`
- `canvas_modify_file_collaborator_role`
- `canvas_remove_file_collaborator`
- `create_new_file_with_markdown`
- `hub_create_file_from_content`
- `in_meeting_control`
- `meeting_create`
- `meeting_delete`
- `meeting_update`
