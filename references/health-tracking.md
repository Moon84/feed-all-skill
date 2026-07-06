# 健康追踪参考

## 存储结构

数据存储在 `~/.rss-plugin/data.db`（SQLite）：

```
feeds
  ├── 基本信息: id, name, url, category, type, refresh_enabled, ...
  ├── 网页配置: parser_config, tags, domains
  └── 健康追踪: last_polled_at, start_ts, consecutive_failures,
                 last_error, auto_disabled

entries
  ├── id, feed_id, title, url, published_at
  ├── content, preview, authors
  ├── is_read, entry_type (rss / webpage_snapshot / webpage_list)
  └── FOREIGN KEY feed_id → feeds.id

entry_content
  └── id, entry_id, url, content, summary, unretrievable
```

## 健康追踪字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `last_polled_at` | int | 最近一次成功刷新的 Unix 时间戳 |
| `consecutive_failures` | int | 连续失败次数，成功时归零 |
| `last_error` | str | 最后一次失败的错误信息 |
| `auto_disabled` | bool | 连续 10 次失败后自动设为 true |
| `refresh_enabled` | bool | auto_disabled 时同步设为 false |

## 自动禁用规则

```
每失败一次 → consecutive_failures += 1
每成功一次 → consecutive_failures = 0

当 consecutive_failures >= 10:
    auto_disabled = true
    refresh_enabled = false
    （该源不再参与自动刷新）
```

## 重新启用

```python
# 重置健康状态
rss_update_feed(feed_id="abc123", refresh_enabled=True)
# 这会清除 auto_disabled 标记，源重新参与刷新
```

## 网页快照去重

网页快照基于内容哈希判断是否变化：

```
entry_url = f"{url}#snapshot-{md5(cleaned_html)}"
```
内容未变 → URL 哈希相同 → 跳过入库 → 无重复条目