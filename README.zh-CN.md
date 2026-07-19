# feed-all

[![PyPI](https://img.shields.io/pypi/v/feed-all)](https://pypi.org/project/feed-all/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

`feed-all` 是一个本地优先的 RSS、Atom、网页追踪和 MCP 服务，可在
Codex、Claude 及其他 MCP Host 中用于长期跟踪信息。

它保存订阅元数据和链接，并将刷新时机、相关性判断、正文读取、总结和
提醒交给 Agent。

## 解决的问题

- 在不同 Host 之间维护稳定的 RSS/Atom 订阅。
- 对没有可用 Feed 的网站进行有限网页追踪。
- 保存来源健康状态、阅读状态并支持 OPML 迁移。
- 返回适合 Agent 处理的紧凑结构化结果。
- 按需读取文章，而不是把服务变成通用全文数据库。

## 核心能力

- RSS/Atom 订阅和刷新。
- 网页订阅快照。
- 从网页发现 Feed URL。
- 本地 SQLite 元数据存储。
- 条目搜索、阅读状态和来源健康检查。
- OPML 导入和导出。
- 通过 stdio 提供 MCP 服务。

## 明确不做的事情

- 不运行后台 scheduler。
- 不内置周期性 Digest。
- 不绕过 CAPTCHA、登录、付费墙或反爬挑战。
- 不自动订阅外部搜索结果。
- 不未经用户确认修改用户偏好。
- 当前 0.2.x 服务在按需读取时可能缓存文章正文；规划中的保留策略将
  默认改为链接和元数据优先。详见[数据生命周期](docs/data-lifecycle.zh-CN.md)。

## 快速开始

安装已发布的 MCP 服务：

```bash
uvx feed-all
```

Claude Code、Claude Desktop、Cline 和兼容 JSON MCP 的 Host：

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

Codex：

```toml
[mcp_servers.feed_all]
command = "uvx"
args = ["feed-all"]
```

本地 checkout 使用 `uv run --directory /绝对路径/rss-plugin feed-all`。如
有多个 Host 共用一台机器，建议显式设置数据目录：

```bash
FEED_ALL_DATA_DIR="$HOME/.feed-all" uvx feed-all
```

## Agent 工作流

```text
发现 -> 验证 -> 订阅 -> 刷新 -> 筛选
  -> 读取选中条目 -> 总结或提醒
```

运行时 `tools/list` 是权威能力来源。使用 [SKILL.md](SKILL.md) 和
[工具契约](references/tools.zh-CN.md) 了解当前版本。

## 存储和隐私

默认使用 `~/.feed-all` 下的本地 SQLite。当前发布服务保存来源和条目元
数据，并可能在请求时持久化读取到的正文。目标设计是链接优先：只保存有
上限的 preview 和元数据，用户或 Agent 明确要求时才读取正文。详见
[数据生命周期](docs/data-lifecycle.zh-CN.md)和[合规抓取](docs/compliant-fetching.zh-CN.md)。

## 文档

- [English README](README.md)
- [中文 Agent skill](skill/SKILL.zh-CN.md)
- [Host 配置](docs/host-configuration.zh-CN.md)
- [架构设计](docs/architecture.zh-CN.md)
- [数据生命周期](docs/data-lifecycle.zh-CN.md)
- [合规抓取](docs/compliant-fetching.zh-CN.md)
- [外部新闻发现](docs/discovery.zh-CN.md)
- [个性化](docs/personalization.zh-CN.md)
- [工具参考](references/tools.zh-CN.md)
- [响应契约](references/response-contract.zh-CN.md)
- [OpenSpec 设计索引](openspec/project.md)

## 版本规则

文档区分 `Current 0.2.x`、`Planned 0.3.x`、`Experimental` 和
`Out of scope`。规划工具不会被写成当前可用 MCP 工具。

## 故障排查与贡献

先阅读[故障排查](docs/troubleshooting.zh-CN.md)，再检查运行时
`tools/list` 和 `rss_check_health`。贡献文档时必须同步更新英文和中文，
并且不能声明发布服务尚未提供的能力。

## 许可证

MIT，详见 [LICENSE](LICENSE)。
