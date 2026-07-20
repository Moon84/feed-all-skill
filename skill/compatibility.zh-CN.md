# Host 兼容性

Skill 与 Host 无关。每个 Host 都需要单独注册同一个 MCP 服务。传输方式是
stdio，工具和 Markdown 如何展示由 Host 决定。

| Host | 配置形式 | 注意事项 |
| --- | --- | --- |
| Codex | TOML | 修改 MCP 配置后重启或重新加载 |
| Claude Code | JSON/settings | 在 `mcpServers` 下注册 |
| Claude Desktop | JSON | 使用桌面端 MCP 配置 |
| Cline | JSON | 使用 MCP server 设置 |
| 其他 MCP Host | Host 自定义 | 使用相同命令和参数 |

可以用 PyPI 的 `uvx feed-all` 启动，也可以使用 checkout 的
`uv run --directory /绝对路径/rss-plugin feed-all`。用 MCP `tools/list` 验证
连接，不要仅根据 skill 文件判断能力是否可用。
