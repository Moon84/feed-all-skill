# feed-all Documentation Specifications

This directory records the design contract for the GitHub skill and its
cross-host documentation. It does not change the Python package or MCP
implementation.

## Current Truth

- Released contract: `feed-all` 0.2.x.
- Runtime `tools/list` is authoritative.
- The skill has no built-in scheduler or digest loop.
- Current 0.2.x may persist fetched content when `rss_get_entry` requests it.

## Planned Changes

Planned changes are kept under `changes/` until the corresponding code and
release exist. A planned tool must not be documented as currently available.

## Review Rule

Every English design document must have a matching Chinese document with the
same section structure. Documentation changes should be reviewed for version,
tool-name, persistence, and host-configuration consistency.
