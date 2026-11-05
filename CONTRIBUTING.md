# Contributing

This plugin bundles the Zoom MCP Server and two skills, `zoom-flagship-mcp` (the entry point) and `zoom-search`. Contributions that keep it accurate are welcome:

- A tool was added, renamed or removed on the server.
- A scope, endpoint or rate-limit tier in [`TOOLS.md`](TOOLS.md) is out of date.
- The setup instructions in the [README](README.md) are wrong or unclear.

Out of scope: other skills, slash commands, agents, hooks, other Zoom MCP servers, and developer content such as SDK, REST API, webhook or OAuth-implementation guidance. Developer content belongs in https://github.com/zoom/skills. `skills/zoom-search/` is Zoom's `zoom-search` skill with four local edits listed in the changelog; other changes to it belong with Zoom.

## Making a change

1. Open an issue first for anything larger than a typo.
2. Check the change against https://developers.zoom.us/docs/mcp/zoom/.
3. Keep `TOOLS.md` in step with the server, add a [`CHANGELOG.md`](CHANGELOG.md) entry, and bump the version in `.claude-plugin/plugin.json` when behavior changes.
4. Run `claude plugin validate .` (see [`AGENTS.md`](AGENTS.md)) and open a pull request.

Never commit access tokens, client secrets or machine-local paths.
