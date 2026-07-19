# Host 配置

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

## Claude Code、Claude Desktop 和 Cline

使用 Host 自己的 JSON MCP 配置：

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

## 本地 Checkout

```json
{
  "command": "uv",
  "args": ["run", "--directory", "/绝对路径/rss-plugin", "feed-all"]
}
```

Host 需要显式数据目录时设置 `FEED_ALL_DATA_DIR`。用 MCP `tools/list` 验证
启动，再调用 `rss_list_feeds` 或 `rss_check_health`。
