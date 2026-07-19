# 故障排查

1. 修改 MCP 配置后重启 Host。
2. 确认 `uvx feed-all` 能启动且 stderr 没有立即失败。
3. 检查 MCP `tools/list`，它是运行时契约。
4. 调用 `rss_list_feeds`，确认数据目录和订阅来源。
5. 对失败来源调用 `rss_check_health(revalidate=true)`。
6. 将 401/403 视为授权失败，429 视为限流，5xx 视为来源侧故障，挑战页视为
   不支持。
7. 使用 `FEED_ALL_DATA_DIR`，避免不同 Host 意外打开不同 SQLite 数据库。

除受控本地测试外，不要启用私有 URL。
