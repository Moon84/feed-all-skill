# Tool Reference

This document describes the released `feed-all` 0.2.x surface. The MCP
`tools/list` response is authoritative when the runtime and this document
differ. Planned tools are intentionally listed separately.

## Source Management

| Tool | Purpose | Persists | Network |
| --- | --- | --- | --- |
| `rss_subscribe` | Subscribe to RSS, Atom, or webpage source | Source and initial entries | Yes |
| `rss_list_feeds` | List subscriptions and health metadata | No | No |
| `rss_update_feed` | Update name, category, flags, or tags | Feed metadata | No |
| `rss_unsubscribe` | Remove a subscription and its entries | Deletes source data | No |
| `rss_discover` | Find feed URLs from a webpage | No | Yes |

## Refresh and Health

| Tool | Purpose | Persists | Network |
| --- | --- | --- | --- |
| `rss_refresh` | Refresh one source or all eligible sources | New entry metadata | Yes |
| `rss_check_health` | Inspect or revalidate source health | Health state on refresh | Optional |

## Reading and Search

| Tool | Purpose | Persists | Token note |
| --- | --- | --- | --- |
| `rss_fetch` | Preview a source without subscribing | No | Bound by count |
| `rss_list_entries` | List stored entry metadata | No | Prefer small limits |
| `rss_search` | Search subscribed entries | No | Returns compact entries |
| `rss_get_entry` | Read one entry and optionally fetch linked content | Read state; 0.2.x may cache content | Use only for selected entries |
| `rss_mark_read` | Mark an entry read | Read state | Small |

## Migration

| Tool | Purpose |
| --- | --- |
| `rss_export_opml` | Export subscriptions as OPML |
| `rss_import_opml` | Import subscriptions from OPML |

## Signatures

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

## Planned, Not Released

The following names describe the design roadmap and are not current MCP tools:

```text
rss_search_external
rss_save_discovered_link
rss_subscribe_discovered_source
rss_get_storage_stats
rss_cleanup
rss_update_retention_policy
```

## Usage Rules

1. Preview or discover before subscribing when the source is unfamiliar.
2. Refresh from the host Agent's user-configured task; the service has no
   built-in scheduler or digest loop.
3. Use metadata tools first and fetch selected content only when needed.
4. Do not treat a planned tool as available because it appears in a design
   document.
