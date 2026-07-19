# RSS 插件故障排查

## 插件无法启动

```
Error: MCP server not responding
```
**解决**: 检查 `~/.claude/settings.json` 中的配置是否正确：
```json
{"mcpServers": {"feed-all": {"command": "uvx", "args": ["feed-all"]}}}
```

```
Error: command not found: feed-all
```
**解决**: 使用 `uvx feed-all`。本项目统一使用 `uv`，不要使用 `pip` 或 `pip3`。

## 订阅失败

| 错误 | 可能原因 | 解决 |
|------|---------|------|
| `Request timed out after 30s` | 源不可达 | 检查 URL 或网络 |
| `HTTP 404` | 源不存在 | 用 `rss_discover` 寻找正确 URL |
| `Invalid feed: ...` | 不是有效 RSS/Atom | 用 `rss_fetch` 验证 |
| `Feed URL is not valid` | 订阅时验证失败 | 确认 URL 是标准 RSS/Atom |

## 健康问题

| 现象 | 含义 | 处理 |
|------|------|------|
| `auto_disabled: true` | 当前实现连续 10 次失败后自动禁用 | `rss_update_feed(feed_id, refresh_enabled=True)` |
| 刷新不返回新条目 | 源没有新内容 | 正常，等下次刷新 |
| 网页快照不更新 | 内容没有变化 | 正常，内容哈希未变 |

## 存储问题

```
Error: database is locked
```
**解决**: 等待其他操作完成，或删除 `~/.feed-all/data.db` 重建

## 配置 Claude Code

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
