# RSS 插件安装检查清单

## 前置检查
- [ ] `uv` 已安装: `which uv`
- [ ] Python 3.11+: `uv run python --version`

## 安装步骤
- [ ] 通过 `uvx` 安装: `uvx feed-all`（首次会自动下载，约 35 个依赖包）
- [ ] 验证启动: Host 能完成 MCP 初始化并看到 `tools/list`

## 配置
- [ ] Claude Code 配置已添加（`~/.claude/settings.json`）：
      ```json
      {"mcpServers": {"feed-all": {"command": "uvx", "args": ["feed-all"]}}}
      ```
- [ ] 验证连通性: 调用 `rss_list_feeds()` 返回 `{"success": true}`

## 功能验证
- [ ] 订阅测试: `rss_subscribe(url="https://hnrss.org/frontpage")`
- [ ] 预览测试: `rss_fetch(url="https://hnrss.org/frontpage", count=3)`
- [ ] 列表测试: `rss_list_feeds()`
- [ ] 刷新测试: `rss_refresh()`
- [ ] 健康检查: `rss_check_health()`
- [ ] 导出测试: `rss_export_opml()`
- [ ] 清理: `rss_unsubscribe(feed_id="...")`
