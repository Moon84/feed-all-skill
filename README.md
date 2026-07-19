# feed-all

[![PyPI](https://img.shields.io/pypi/v/feed-all)](https://pypi.org/project/feed-all/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

`feed-all` is a local-first RSS, Atom, webpage tracking, and MCP service for
long-term information follow-up in Codex, Claude, and other MCP hosts.

It keeps subscription metadata and links available to an Agent. The Agent
decides when to refresh, what is relevant, whether to read selected content,
and whether a user should be reminded.

## What It Solves

- Maintain durable RSS and Atom subscriptions across supported hosts.
- Track selected webpages when a source has no usable feed.
- Keep source health, reading state, and OPML portability.
- Return compact structured results suitable for Agent workflows.
- Support on-demand article retrieval without turning the service into a
  general-purpose archive.

## What It Does

- RSS and Atom subscription and refresh.
- Webpage subscription snapshots.
- Feed discovery from a webpage.
- Local SQLite metadata storage.
- Entry search, reading state, and source health checks.
- OPML import and export.
- MCP integration through stdio.

## What It Does Not Do

- It does not run a background scheduler.
- It does not provide a built-in periodic digest.
- It does not bypass CAPTCHA, login, paywalls, or anti-bot challenges.
- It does not automatically subscribe external search results.
- It does not change a user's profile without confirmation.
- The 0.2.x service may cache fetched article content; the planned retention
  policy makes link and metadata storage the default. See
  [Data Lifecycle](docs/data-lifecycle.md).

## Quick Start

Install the published MCP server:

```bash
uvx feed-all
```

For a local checkout:

```bash
uv run --directory /ABSOLUTE/PATH/rss-plugin feed-all
```

Claude Code, Claude Desktop, Cline, and compatible JSON MCP hosts:

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

Codex:

```toml
[mcp_servers.feed_all]
command = "uvx"
args = ["feed-all"]
```

Keep the data directory explicit when multiple hosts share a machine:

```bash
FEED_ALL_DATA_DIR="$HOME/.feed-all" uvx feed-all
```

## Agent Workflow

```text
Discover -> Validate -> Subscribe -> Refresh -> Filter
        -> Read selected entries -> Summarize or remind
```

The runtime `tools/list` response is authoritative. Use the workflow in
[`SKILL.md`](SKILL.md) and the released tool contract in
[`references/tools.md`](references/tools.md).

## Storage and Privacy

The default store is local SQLite under `~/.feed-all`. The current released
service stores source and entry metadata and may persist fetched content when
requested. The target design is link-first: bounded previews and metadata are
kept locally, while full text is fetched only when the user or Agent asks for
it. See [Data Lifecycle](docs/data-lifecycle.md) and
[Compliant Fetching](docs/compliant-fetching.md).

## Documentation

- [中文说明](README.zh-CN.md)
- [Agent skill](skill/SKILL.md)
- [Host configuration](docs/host-configuration.md)
- [Architecture](docs/architecture.md)
- [Data lifecycle](docs/data-lifecycle.md)
- [Compliant fetching](docs/compliant-fetching.md)
- [External discovery](docs/discovery.md)
- [Personalization](docs/personalization.md)
- [Tool reference](references/tools.md)
- [Response contract](references/response-contract.md)
- [OpenSpec design index](openspec/project.md)

## Version Policy

Documentation distinguishes `Current 0.2.x`, `Planned 0.3.x`, `Experimental`,
and `Out of scope`. Planned tools are never presented as available MCP tools.

## Troubleshooting and Contribution

See [Troubleshooting](docs/troubleshooting.md), then inspect the runtime
`tools/list` result and `rss_check_health` output. Contributions should update
the English and Chinese document with the same section structure and must not
claim capabilities that are absent from the released server.

## License

MIT. See [LICENSE](LICENSE).
