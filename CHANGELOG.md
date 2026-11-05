# Changelog

All notable changes to this plugin are documented in this file.

This plugin continues [zoom/zoom-plugin-claude](https://github.com/zoom/zoom-plugin-claude); entries before 2.0.0 describe that repository.

## 2.0.1

- synced `zoom-search` unmodified with the current enterprise-rag source, including discovery-based datasource routing, Ask-based third-party retrieval, and authorization notices; its metadata version remains 1.0.0 and it can be loaded independently
- replaced `zoom-flagship-mcp` with the supplied revision: search guidance now belongs to `zoom-search`, while the flagship handles named-item reads and writes; search-dependent actions stop when `zoom-search` is unavailable

## 2.0.0

- made the plugin consumer-focused; it now bundles only the Zoom MCP Server (`zoom-mcp`, `https://mcp.zoom.us/mcp/zoom/streamable`) and two skills, `zoom-flagship-mcp` and `zoom-search`
- updated to the server's current 20 tools, including agentic search (`search`, `search_describe_capabilities`, `ask`), My Notes, meeting create/update/delete and in-meeting controls, and Canvas collaborator tools; `search_zoom` no longer exists
- added the `zoom-flagship-mcp` skill, the entry point for Zoom requests: it routes search and Ask work to `zoom-search` and handles recordings, notes, chat, Docs and Sheets, calendar, Zoom Phone, and meeting and collaborator writes itself
- added Zoom's `zoom-search` skill (version 1.0.0), unmodified, alongside the flagship; the flagship routes search and Ask work to it and takes precedence where the two differ
- removed the other six bundled servers (Meetings, Canvas, Chat, Tasks, Revenue Accelerator, Whiteboard)
- removed all developer skills and the `zoom-mcp` reference skill with its `triggers` (SDKs, REST API, webhooks, OAuth setup, Marketplace app building, bots, debugging)
- replaced `CONNECTORS.md` with `TOOLS.md`, which lists every tool with its scope and rate-limit tier, a read-only scope set, and the tools that change data
- documented the claude.ai custom connector route, since Zoom supports only manual OAuth client registration
- pointed developers to https://github.com/zoom/skills for SDK, API and integration skills
- rewrote the README, AGENTS.md and CONTRIBUTING.md

## Before 2.0.0 (never versioned)

- added Zoom AI Services Scribe Live Mode guidance for authenticated WebSocket transcription,
  PCM16 streaming, browser relay architecture, event handling, limits, and graceful shutdown
- updated the Zoom MCP catalog for the seven current official product servers, including current Chat, Canvas, Tasks, Revenue Accelerator, Meetings, and Whiteboard endpoints and scopes
- synced the canonical Marketplace template catalog, including Meetings, Canvas, Tasks, Revenue Accelerator, Chat, and Whiteboard MCP manifests plus legacy Docs compatibility
- added the machine-readable template index and safe full-replacement workflow for updating existing General App manifests
- updated product and MCP routing to distinguish General App manifests from native S2S and Meeting SDK create requests
- chained Marketplace app workflows through the helper automatically with confirmation and read-back verification

## 1.2.0

- added `/setup-zoom-marketplace-app` for app-model selection, manifest validation, scopes, events, credential handling, and post-create setup
- bundled 25 scenario templates for General, S2S, Meeting SDK, webhook, WebSocket, RTMS, Team Chat, Phone, Contact Center, Zoom Apps, Plugin SDK, and MCP registration paths
- routed planning, OAuth, SDK, event, RTMS, product, and MCP workflows through Marketplace setup where app registration is required
- clarified that Meeting/Webinar RTMS uses a user-managed General App, Contact Center Voice RTMS can use General or S2S based on scope, and Team Chat S2S support does not include chatbot subscriptions

## 1.1.3

- synced plugin skills with the current canonical AI Services updates, including Summarizer and Translator
- updated Zoom MCP guidance for the seven-tool main server surface and cross-Zoom `search_zoom` workflows
- added the optional Team Chat MCP child skill while keeping `.mcp.json` limited to the existing bundled servers
- refreshed Video SDK Web references and Meeting SDK example updates from the canonical skills tree

## Earlier

- aligned the repository with the current Claude plugin structure around `.claude-plugin/plugin.json`, `skills/`, and `.mcp.json`
- added Claude-facing installation and connector documentation
- converted command-style workflows into `SKILL.md`-based workflows under `skills/`
- bundled Zoom MCP server configuration in `.mcp.json`
- tightened skill metadata and reduced maintainer-facing wording in user-facing docs
