# 工具参考

本文档描述已发布 `feed-all` 0.2.x 的工具面。运行时与本文档不一致时，
以 MCP `tools/list` 为准。规划工具单独列出，不代表当前可用。

## 来源管理

| 工具 | 作用 | 是否持久化 | 是否联网 |
| --- | --- | --- | --- |
| `rss_subscribe` | 订阅 RSS、Atom 或网页 | 来源和初始条目 | 是 |
| `rss_list_feeds` | 查看订阅和健康元数据 | 否 | 否 |
| `rss_update_feed` | 修改名称、分类、开关或标签 | 来源元数据 | 否 |
| `rss_unsubscribe` | 删除订阅及其条目 | 删除来源数据 | 否 |
| `rss_discover` | 从网页发现 Feed URL | 否 | 是 |

## 刷新和健康

| 工具 | 作用 | 是否持久化 | 是否联网 |
| --- | --- | --- | --- |
| `rss_refresh` | 刷新一个或全部可用来源 | 新条目元数据 | 是 |
| `rss_check_health` | 检查或重新验证来源健康状态 | 刷新时保存健康状态 | 可选 |

## 阅读和搜索

| 工具 | 作用 | 是否持久化 | Token 建议 |
| --- | --- | --- | --- |
| `rss_fetch` | 不订阅，只预览来源 | 否 | 受 count 限制 |
| `rss_list_entries` | 列出条目元数据 | 否 | 使用小 limit |
| `rss_search` | 搜索已订阅条目 | 否 | 返回紧凑条目 |
| `rss_get_entry` | 读取条目并按需抓取链接正文 | 标记已读；0.2.x 可能缓存正文 | 只读取选中条目 |
| `rss_mark_read` | 标记已读 | 阅读状态 | 小 |

## 迁移

| 工具 | 作用 |
| --- | --- |
| `rss_export_opml` | 导出 OPML |
| `rss_import_opml` | 从 OPML 导入订阅 |

## 签名

```python
rss_subscribe(url, name=None, category="uncategorized", feed_type="auto", requires_vpn=False)
rss_fetch(url, count=10, full_content=False)
rss_list_feeds()
rss_refresh(feed_id=None)
rss_list_entries(feed_id=None, limit=50, unread_only=False, query=None)
rss_search(query, limit=20)
rss_get_entry(entry_id, fetch_content=True)
rss_discover(url)
rss_check_health(feed_id=None, revalidate=False)
rss_mark_read(entry_id)
rss_unsubscribe(feed_id)
rss_update_feed(feed_id, name=None, category=None, refresh_enabled=None,
                check_interval_units=None, tags=None, requires_vpn=None)
rss_export_opml()
rss_import_opml(opml_content)
```

## 规划中，尚未发布

```text
rss_search_external
rss_save_discovered_link
rss_subscribe_discovered_source
rss_get_storage_stats
rss_cleanup
rss_update_retention_policy
```

## 使用规则

1. 对陌生来源先预览或发现，再订阅。
2. 刷新由 Host Agent 的用户定时任务调用；服务没有内置 scheduler 或 Digest 循环。
3. 先使用元数据工具，必要时再读取选中正文。
4. 设计文档中的规划工具不等于当前 MCP 工具。
