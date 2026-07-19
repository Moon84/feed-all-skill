# Architecture

## Positioning

`feed-all` is the source and metadata layer for an Agentic information-follow-
up system. It is not a crawler fleet, a scheduler, a news editor, or a full-
text knowledge base.

## Context

```text
MCP Host (Codex / Claude / other)
  -> feed-all MCP service
      -> subscription manager
      -> refresh coordinator
      -> compliant fetch layer
      -> metadata store
      -> retention manager (planned)

Agent
  -> filter -> summarize -> remind -> request selected content

User / EntroFeed
  -> confirm subscriptions -> update profile -> persist selected artifacts
```

## Responsibility Boundaries

| Module | Responsibility |
| --- | --- |
| feed-all | Sources, subscriptions, refresh, metadata, links, read state |
| Agent | Relevance, event deduplication, summaries, reminders |
| User | Confirm subscriptions, saves, and profile changes |
| EntroFeed | Long-term knowledge, analysis artifacts, ontology, user profile |

## Data Flow

```text
Discover -> Validate -> Subscribe -> Refresh -> Store metadata
                                      -> Agent filters candidates
                                      -> On-demand content read
                                      -> Optional upstream persistence
```

The service has no built-in periodic scheduler or digest. A host Agent may
invoke refresh from a user-configured scheduled task.

## Version Boundary

This repository documents the released 0.2.x contract. Conditional HTTP,
bounded retention, external discovery, and additional storage tools are
planned capabilities and must not be presented as current tools.
