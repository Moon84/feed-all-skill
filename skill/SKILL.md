---
name: feed-all
description: Cross-host RSS, Atom, webpage subscription, refresh, search, and on-demand reading through the feed-all MCP service.
tags: [rss, atom, webpage, mcp, codex, claude]
metadata:
  canonical_skill: feed-all
  compatibility_alias: rss-plugin-installer
  mcp_server: feed-all
---

# feed-all Agent Skill

Use the `feed-all` MCP server for durable source subscriptions and compact
entry retrieval. The runtime `tools/list` response is authoritative.

## Intent Routing

| Intent | Tool |
| --- | --- |
| Find a feed | `rss_discover` or `rss_fetch` |
| Create a subscription | `rss_subscribe` |
| Refresh sources | `rss_refresh` |
| List or search entries | `rss_list_entries` or `rss_search` |
| Read selected content | `rss_get_entry` |
| Diagnose a source | `rss_check_health` |

## Default Workflow

```text
Discover -> Validate -> Subscribe -> Refresh -> Filter
        -> Read selected entries -> Summarize or remind
```

Use metadata first. Keep list limits small. Fetch linked content only for
entries selected for analysis. Mark entries read after successful review.

## Persistence and Safety

- Source metadata, entry metadata, links, and read state are persistent.
- The released 0.2.x service may cache fetched content.
- Full-text retention limits are planned; do not promise them as current API.
- Do not bypass CAPTCHA, authentication, paywalls, robots policies, or
  anti-bot challenges.
- Do not subscribe to private or unauthorized sources.

## Agent Responsibilities

The skill does not run a scheduler or digest loop. A host Agent may call
`rss_refresh` from a user-configured scheduled task, then filter, summarize,
and remind. A profile may guide ranking, but changing a profile requires user
confirmation.

The old `rss-plugin-installer` name is a compatibility alias for this skill.
