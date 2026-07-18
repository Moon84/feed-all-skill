# feed-all MCP 工具参考

MCP `tools/list` 是运行时权威契约。以下为 `feed-all 0.2.x` 的 14 个工具：

| 工具 | 功能 |
|------|------|
| `rss_subscribe` | 订阅 RSS、Atom 或网页快照 |
| `rss_fetch` | 不保存订阅，预览 RSS 或网页 |
| `rss_list_feeds` | 列出所有订阅 |
| `rss_refresh` | 刷新一个或全部订阅 |
| `rss_list_entries` | 列出已保存文章 |
| `rss_search` | 搜索标题、摘要和正文 |
| `rss_get_entry` | 读取文章，可抓取并缓存正文 |
| `rss_discover` | 从网页发现 RSS/Atom 地址 |
| `rss_check_health` | 检查或重新验证订阅健康状态 |
| `rss_mark_read` | 标记文章已读 |
| `rss_unsubscribe` | 删除订阅 |
| `rss_update_feed` | 更新订阅元数据 |
| `rss_export_opml` | 导出 OPML |
| `rss_import_opml` | 导入 OPML |

## 常用签名

```python
rss_subscribe(url, name=None, category="uncategorized",
              feed_type="auto", requires_vpn=False)
rss_fetch(url, count=10, full_content=False)
rss_refresh(feed_id=None)
rss_list_entries(feed_id=None, limit=50, unread_only=False, query=None)
rss_search(query, limit=20)
rss_get_entry(entry_id, fetch_content=True)
rss_check_health(feed_id=None, revalidate=False)
rss_import_opml(opml_content)
```

`feed_type` 支持 `auto`、`rss`、`atom` 和 `webpage`。本地/private URL
默认禁用，仅在受控测试中设置 `FEED_ALL_ALLOW_PRIVATE_URLS=1`。
