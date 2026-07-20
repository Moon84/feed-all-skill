# feed-all

[![PyPI](https://img.shields.io/pypi/v/feed-all)](https://pypi.org/project/feed-all/)
[![Python](https://img.shields.io/badge/python-3.11%2B-blue)](https://www.python.org/)
[![MCP](https://img.shields.io/badge/MCP-compatible-5b5bd6)](https://modelcontextprotocol.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

**English | [简体中文](README.zh-CN.md)**

`feed-all` is a local-first RSS, Atom, webpage tracking, and MCP service for
long-term information follow-up in Codex, Claude, and other MCP hosts.

It keeps subscription metadata and links available to an Agent. The Agent
decides when to refresh, what is relevant, whether to read selected content,
and whether a user should be reminded.

## Contents

- [Why feed-all](#why-feed-all)
- [Capabilities](#capabilities)
- [Architecture](#architecture)
- [Quick start](#quick-start)
- [Agent workflow](#agent-workflow)
- [Data and privacy](#data-and-privacy)
- [Compliant fetching](#compliant-fetching)
- [Discovery and personalization](#discovery-and-personalization)
- [MCP tools](#mcp-tools)
- [Repository guide](#repository-guide)
- [Version policy](#version-policy)
- [Troubleshooting](#troubleshooting)

## Why feed-all

Information tracking has two different jobs: collecting stable sources and
deciding what deserves attention. `feed-all` handles the first job through a
small, portable MCP surface. The host Agent handles ranking, event
deduplication, summarization, and reminders.

This keeps the service useful for research, regulatory monitoring, financial
events, company news, and any workflow where a user needs to follow signals
over time without building a second full-text archive.

## Capabilities

- RSS and Atom subscription and refresh.
- Webpage subscription snapshots when no usable feed exists.
- Feed discovery from a webpage.
- Local SQLite metadata storage.
- Entry search, reading state, and source health checks.
- OPML import and export.
- Structured JSON plus Markdown fallback over MCP stdio.

### Explicit non-goals

- No background scheduler or built-in periodic digest.
- No CAPTCHA, login, paywall, or anti-bot challenge bypass.
- No automatic subscription of external search results.
- No silent changes to a user's profile.
- No default promise of permanent full-text storage.

The released 0.2.x service may cache fetched article content when requested.
The target retention direction is link-first; see [Data and privacy](#data-and-privacy).

## Architecture

```text
MCP Host (Codex / Claude / other)
  -> feed-all MCP service
      -> subscription manager
      -> refresh coordinator
      -> compliant fetch layer
      -> local metadata store

Host Agent
  -> filter -> deduplicate events -> summarize -> remind
  -> request selected article content when needed

User / upstream application
  -> confirm subscriptions and saves
  -> maintain profile and long-term knowledge artifacts
```

| Layer | Responsibility |
| --- | --- |
| `feed-all` | Sources, subscriptions, refresh, links, metadata, read state |
| Agent | Relevance, event grouping, summaries, reminders, scheduling |
| User | Confirm subscriptions, saves, and profile changes |
| Upstream application | Long-term documents, analysis artifacts, ontology, profile |

`feed-all` has no scheduler. A host Agent may call `rss_refresh` from a
user-configured scheduled task, then summarize or remind the user.

## Quick start

### Install from PyPI

```bash
uvx feed-all
```

### Claude Code, Claude Desktop, Cline, and JSON MCP hosts

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

### Codex

```toml
[mcp_servers.feed_all]
command = "uvx"
args = ["feed-all"]
```

### Local checkout

```json
{
  "command": "uv",
  "args": ["run", "--directory", "/ABSOLUTE/PATH/rss-plugin", "feed-all"]
}
```

Set an explicit local data directory when multiple hosts share a machine:

```bash
FEED_ALL_DATA_DIR="$HOME/.feed-all" uvx feed-all
```

After configuration, verify the server with MCP `tools/list`, then call
`rss_list_feeds` or `rss_check_health`.

## Agent workflow

```text
Discover -> Validate -> Subscribe -> Refresh -> Filter
        -> Read selected entries -> Summarize or remind
```

Use metadata and previews first. Keep list limits small. Fetch linked content
only for entries selected for analysis. Read a user-owned profile for ranking,
but require confirmation before changing it.

## Data and privacy

The default store is local SQLite under `~/.feed-all`, configurable with
`FEED_ALL_DATA_DIR`.

### Persistent metadata

- Source URL, title, category, and health state;
- entry title, URL, author, and publication time;
- bounded preview, read state, and entry type;
- OPML subscription metadata.

### Current 0.2.x behavior

`rss_get_entry(fetch_content=true)` may retrieve and persist linked article
content. This is the released behavior and is documented explicitly so it is
not confused with the target retention policy.

### Target link-first policy

The planned direction is to persist links, bounded previews, hashes, and state.
Full text, images, attachments, and external search bodies should be
ephemeral unless a user-confirmed upstream workflow stores them. Planned
retention settings are not current 0.2.x environment variables.

## Compliant fetching

The service follows a conservative RSS-first policy:

- Prefer RSS/Atom over HTML scraping.
- Use an identifiable User-Agent.
- Respect robots.txt, site terms, access controls, and rate limits.
- Use conditional requests and honor `Retry-After` where supported.
- Validate redirect targets and enforce response-size and SSRF limits.
- Stop at CAPTCHA, login, paywall, or anti-bot challenge pages.
- Treat 401/403 as authorization failures, not bypass prompts.
- Classify 429 and 5xx responses without repeatedly hammering a source.

Stealth crawling, CAPTCHA solving, credential reuse, evasive proxy rotation,
and unauthorized private or paid-content access are out of scope.

## Discovery and personalization

Subscription and external discovery are deliberately separate:

```text
subscription = durable, user-confirmed source
discovery    = temporary, broad candidate retrieval
```

The planned discovery flow is:

```text
query -> provider -> normalized candidate -> URL/title deduplication
      -> Agent relevance judgment -> user confirmation
      -> save link or subscribe
```

External candidates should not silently enter the subscription database or be
stored as full articles. Planned provider adapters and tools are not part of
the released 0.2.x MCP surface.

A profile may contain `Role`, `Tracking Goals`, `High-Value Signals`,
`Preferred Sources`, `Excluded Topics`, `Discovery Policy`, and `Output
Preference`. The safe loop is:

```text
Agent proposes change -> user confirms -> host updates profile
                      -> future ranking uses confirmed profile
```

Use the bilingual [user profile template](templates/user-profile.md).

## MCP tools

The runtime `tools/list` response is authoritative. The released 0.2.x tools
are grouped in [references/tools.md](references/tools.md), with a Chinese
version at [references/tools.zh-CN.md](references/tools.zh-CN.md).

| Intent | Tool |
| --- | --- |
| Discover or preview | `rss_discover`, `rss_fetch` |
| Subscribe and manage | `rss_subscribe`, `rss_list_feeds`, `rss_update_feed`, `rss_unsubscribe` |
| Refresh and diagnose | `rss_refresh`, `rss_check_health` |
| Search and read | `rss_list_entries`, `rss_search`, `rss_get_entry`, `rss_mark_read` |
| Migrate | `rss_export_opml`, `rss_import_opml` |

The response envelope and Markdown fallback are described in
[references/response-contract.md](references/response-contract.md).

## Repository guide

```text
SKILL.md                         Host-readable canonical skill entry
skill/SKILL.md                   Expanded English Agent skill
skill/SKILL.zh-CN.md             中文 Agent skill
skill/compatibility.md           MCP Host compatibility
skill/compatibility.zh-CN.md     MCP Host 兼容性
references/tools.md              Released tool contract
references/response-contract.md  JSON and Markdown response contract
references/capabilities.json     Source catalog
templates/user-profile.md        User profile template
```

The compatibility name `rss-plugin-installer` is retained for existing Host
configurations and points to the canonical `feed-all` skill.

## Version policy

Documentation distinguishes:

- **Current 0.2.x**: released MCP tools and behavior;
- **Planned 0.3.x**: design direction, not an available API;
- **Experimental**: explicitly labeled experiments;
- **Out of scope**: behavior the skill must not perform.

Planned tools must never be presented as currently available MCP tools.

## Troubleshooting

1. Restart the Host after changing MCP configuration.
2. Confirm `uvx feed-all` starts without an immediate stderr failure.
3. Inspect MCP `tools/list`; it is the runtime contract.
4. Call `rss_list_feeds` to confirm the data directory and subscriptions.
5. Call `rss_check_health(revalidate=true)` for a failing source.
6. Treat 401/403 as authorization failures, 429 as rate limiting, 5xx as
   source-side failure, and challenge pages as unsupported.

See [references/troubleshooting.md](references/troubleshooting.md) for the
legacy compact checklist.

## Contributing

Documentation changes must update the English and Chinese README with matching
section structure. Do not claim capabilities absent from the released MCP
server. Use `uv` for Python environment commands and keep the skill portable
across MCP hosts.

## License

MIT. See [LICENSE](LICENSE).
