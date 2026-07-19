---
name: feed-all
description: Use the feed-all MCP service for RSS, Atom, webpage subscriptions, refresh, search, and on-demand article reading.
tags: [rss, atom, webpage, feed, mcp, codex, claude]
metadata:
  canonical_skill: feed-all
  compatibility_alias: rss-plugin-installer
  mcp_server: feed-all
---

# feed-all

Use the `feed-all` MCP server for durable subscriptions, source refresh,
entry search, reading state, and on-demand article reading.

## Tool Selection

| Intent | Tool | Default behavior |
| --- | --- | --- |
| Discover a feed | `rss_discover` | Reads a webpage and returns feed candidates |
| Preview a source | `rss_fetch` | Does not create a subscription |
| Subscribe | `rss_subscribe` | Persists source metadata |
| Refresh | `rss_refresh` | Agent-triggered network refresh |
| List entries | `rss_list_entries` | Returns compact entry metadata |
| Search stored entries | `rss_search` | Searches subscribed entries |
| Read selected content | `rss_get_entry` | May fetch linked content when requested |
| Diagnose a source | `rss_check_health` | Inspects or revalidates health |

Use `rss_list_feeds`, `rss_update_feed`, `rss_mark_read`, `rss_unsubscribe`,
`rss_export_opml`, and `rss_import_opml` for source and state management.

## Workflow

```text
Discover -> Validate -> Subscribe -> Refresh -> Filter
        -> Read selected entries -> Summarize or remind
```

Start with feed metadata and previews. Fetch full content only for selected
entries. Keep returned lists small and use source, time, category, and unread
filters when available.

## Persistence Rules

- Subscription and entry metadata are persistent.
- The released 0.2.x server may cache fetched article content.
- The planned retention policy is link-first and does not persist full text by
  default.
- External search tools are planned and must remain ephemeral until a user
  confirms a link or subscription.

## Scheduling and Personalization

Do not create a scheduler inside the skill. A host Agent may invoke
`rss_refresh` from a user-configured scheduled task, then summarize or remind
the user. Read a user-provided profile for ranking and discovery preferences;
never modify it without user confirmation.

## Safety

Do not bypass CAPTCHA, login, paywalls, robots policies, or anti-bot
challenges. Do not subscribe to private or unauthorized content. Local and
private URLs are disabled by default by the service.

The MCP `tools/list` response is the authoritative runtime contract. The
compatibility name `rss-plugin-installer` points to this canonical skill.
