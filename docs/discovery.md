# External Discovery

Subscription and discovery are separate behaviors.

```text
subscription = durable, user-confirmed source
discovery    = temporary, broad candidate retrieval
```

The planned flow is:

```text
query -> provider -> normalized candidate -> URL/title deduplication
      -> Agent relevance judgment -> user confirmation
      -> save link or subscribe
```

External results should contain only title, URL, source, publication time, and
snippet by default. They should not be silently inserted into the subscription
database or stored as full articles.

Planned provider adapters may include Google News RSS, Tavily, or NewsAPI. A
provider is an optional discovery adapter, not a replacement for trusted
official subscriptions.

Planned tool names are `rss_search_external`, `rss_save_discovered_link`, and
`rss_subscribe_discovered_source`. They are not released MCP tools in 0.2.x.
