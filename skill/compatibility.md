# Host Compatibility

The skill is host-agnostic. Each host registers the same MCP server separately.
The transport is stdio; the host decides how tools and Markdown are rendered.

| Host | Configuration style | Notes |
| --- | --- | --- |
| Codex | TOML | Restart or reload the MCP configuration |
| Claude Code | JSON/settings | Register under `mcpServers` |
| Claude Desktop | JSON | Register under the desktop MCP configuration |
| Cline | JSON | Register under the MCP server settings |
| Other MCP hosts | Host-specific | Use the same command and arguments |

The server can be started from PyPI with `uvx feed-all` or from a checkout with
`uv run --directory /ABSOLUTE/PATH/rss-plugin feed-all`. Verify the connection
with MCP `tools/list`; do not infer availability from the skill file alone.
