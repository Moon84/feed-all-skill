# Data Lifecycle

## Persistent by Default

The service keeps subscription and entry metadata: source URL, title, source
name, publication time, author, bounded preview, read state, and health state.
The default data directory is `~/.feed-all` and can be changed with
`FEED_ALL_DATA_DIR`.

## Current 0.2.x Behavior

`rss_get_entry(fetch_content=true)` may retrieve and persist linked article
content in the released service. This is current behavior, not the target
retention policy.

## Planned Link-First Policy

The planned policy keeps links, bounded previews, hashes, and state. Full text,
images, attachments, and external search bodies are ephemeral unless the user
explicitly asks an upstream application to persist them.

Planned policy names include `FEED_ALL_MAX_ENTRIES`,
`FEED_ALL_RETENTION_DAYS`, and `FEED_ALL_MAX_PREVIEW_CHARS`. They are design
inputs, not released 0.2.x environment variables.

## Lifecycle

```text
discovered -> subscribed -> metadata stored -> selected for reading
           -> ephemeral content fetch -> optional user-confirmed persistence
```

No content should be silently copied into a larger knowledge base just because
it appeared in a subscription.
