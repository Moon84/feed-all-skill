# Response Contract

Every released tool returns a JSON-compatible object. Successful responses
use the portable envelope below when supported:

```json
{
  "success": true,
  "render_type": "rss.entry_list",
  "data": {},
  "markdown": ""
}
```

Failures use:

```json
{
  "success": false,
  "error": "human-readable error"
}
```

The `data` object is the machine-readable contract. `markdown` is a fallback
for hosts that do not provide a specialized renderer. Agents must not assume
that every host renders Markdown or that every future tool uses the same
render type.

## Render Types

Current common values include `rss.feed`, `rss.feed_list`,
`rss.entry_list`, `rss.entry_detail`, `rss.discovery`,
`rss.refresh_result`, `rss.source_health`, `rss.entry_state`,
`rss.opml_result`, and `rss.op_result`.

## Compactness

List responses should be preferred over entry detail responses. A host Agent
should request a small limit, filter by time or source, and only request full
content for entries selected for analysis.
