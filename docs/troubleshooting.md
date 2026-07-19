# Troubleshooting

1. Restart the host after changing MCP configuration.
2. Confirm `uvx feed-all` starts without an immediate stderr failure.
3. Inspect MCP `tools/list`; it is the runtime contract.
4. Call `rss_list_feeds` to confirm the data directory and subscriptions.
5. Call `rss_check_health(revalidate=true)` for a failing source.
6. Treat 401/403 as authorization failures, 429 as rate limiting, 5xx as
   source-side failure, and challenge pages as unsupported.
7. Use `FEED_ALL_DATA_DIR` to avoid accidentally opening a different SQLite
   store from another host.

Do not enable private URLs outside controlled local testing.
