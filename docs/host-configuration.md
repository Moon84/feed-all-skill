# Host Configuration

## PyPI

```bash
uvx feed-all
```

## Codex

```toml
[mcp_servers.feed_all]
command = "uvx"
args = ["feed-all"]
```

## Claude Code, Claude Desktop, and Cline

Use the host's JSON MCP configuration:

```json
{
  "mcpServers": {
    "feed-all": {
      "command": "uvx",
      "args": ["feed-all"]
    }
  }
}
```

## Local Checkout

```json
{
  "command": "uv",
  "args": ["run", "--directory", "/ABSOLUTE/PATH/rss-plugin", "feed-all"]
}
```

Set `FEED_ALL_DATA_DIR` when a host needs an explicit data location. Verify
startup with MCP `tools/list`; then call `rss_list_feeds` or `rss_check_health`.
