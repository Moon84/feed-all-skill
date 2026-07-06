# RSS 插件工具参考

## 工具总览

| 工具 | 功能 | 分类 |
|------|------|------|
| `rss_subscribe` | 订阅 RSS/Atom 源或网页源 | **核心** |
| `rss_fetch` | 预览源（不订阅，两遍式） | 可选 |
| `rss_list_feeds` | 列出所有订阅及健康信息 | **核心** |
| `rss_unsubscribe` | 取消订阅 | 偶尔 |
| `rss_refresh` | 刷新源（自动健康追踪） | **核心** |
| `rss_list_entries` | 读取文章列表 | **核心** |
| `rss_get_entry` | 获取全文（自动标记已读） | 按需 |
| `rss_discover` | 从 URL 发现 RSS | 查找时 |
| `rss_check_health` | 检查源健康状况 | 诊断时 |
| `rss_export_opml` | 导出为 OPML | 迁移时 |
| `rss_import_opml` | 从 OPML 导入 | 迁移时 |
| `rss_mark_read` | 标记已读 | 偶尔 |
| `rss_update_feed` | 修改订阅设置 | 偶尔 |

## 工具签名

### rss_subscribe
```python
rss_subscribe(
    url: str,                          # RSS/网页 URL
    name: Optional[str] = None,        # 显示名（默认自动发现）
    category: str = "uncategorized",   # 分类
    feed_type: str = "auto",           # "auto" | "rss" | "webpage" | "crawl4ai"
    requires_vpn: bool = False,        # 需要 VPN 才能访问
) -> dict
```

### rss_fetch
```python
rss_fetch(
    url: str,                          # Feed URL
    count: int = 10,                   # 返回条数（1-50）
    full_content: bool = False,        # 是否包含全文
) -> dict
```

### rss_list_feeds
```python
rss_list_feeds() -> dict
# 返回：count, feeds[{id, name, url, category, type, refresh_enabled,
#                    auto_disabled, last_error, consecutive_failures,
#                    requires_vpn, entry_count}]
```

### rss_refresh
```python
rss_refresh(
    feed_id: Optional[str] = None,     # 指定源ID，不传则刷新所有
) -> dict
```

### rss_list_entries
```python
rss_list_entries(
    feed_id: Optional[str] = None,     # 筛选源
    limit: int = 50,                   # 条数（1-200）
    unread_only: bool = False,         # 仅未读
) -> dict
```

### rss_get_entry
```python
rss_get_entry(
    entry_id: str,                     # 条目 ID
) -> dict
```

### rss_discover
```python
rss_discover(
    url: str,                          # 网站 URL
) -> dict
```

### rss_check_health
```python
rss_check_health(
    feed_id: Optional[str] = None,     # 指定源
    revalidate: bool = False,          # 是否实时验证
) -> dict
```

### rss_export_opml / rss_import_opml
```python
rss_export_opml() -> dict             # {opml_content, feed_count}
rss_import_opml(opml_content: str) -> dict  # {imported, skipped}
```

### rss_mark_read
```python
rss_mark_read(entry_id: str) -> dict
```

### rss_update_feed
```python
rss_update_feed(
    feed_id: str,
    name: Optional[str] = None,
    category: Optional[str] = None,
    refresh_enabled: Optional[bool] = None,
    check_interval_units: Optional[int] = None,
    tags: Optional[list[str]] = None,
    requires_vpn: Optional[bool] = None,
) -> dict
```