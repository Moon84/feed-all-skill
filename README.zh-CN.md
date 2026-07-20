# feed-all

[![PyPI](https://img.shields.io/pypi/v/feed-all)](https://pypi.org/project/feed-all/)
[![Python](https://img.shields.io/badge/python-3.11%2B-blue)](https://www.python.org/)
[![MCP](https://img.shields.io/badge/MCP-compatible-5b5bd6)](https://modelcontextprotocol.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

**[English](README.md) | 简体中文**

`feed-all` 是一个本地优先的 RSS、Atom、网页追踪和 MCP 服务，可在
Codex、Claude 及其他 MCP Host 中用于长期跟踪信息。

它保存订阅元数据和链接，并将刷新时机、相关性判断、正文读取、总结和提醒
交给 Agent。

## 目录

- [为什么使用 feed-all](#为什么使用-feed-all)
- [核心能力](#核心能力)
- [架构](#架构)
- [快速开始](#快速开始)
- [Agent 工作流](#agent-工作流)
- [数据和隐私](#数据和隐私)
- [合规抓取](#合规抓取)
- [发现和个性化](#发现和个性化)
- [MCP 工具](#mcp-工具)
- [仓库说明](#仓库说明)
- [版本规则](#版本规则)
- [故障排查](#故障排查)

## 为什么使用 feed-all

长期信息跟踪包含两个不同问题：维护稳定来源，以及判断什么值得关注。
`feed-all` 通过小而便携的 MCP 工具面处理前者，Host Agent 负责排序、事件
去重、总结和提醒。

因此它适合学术研究、监管跟踪、金融事件、公司新闻以及其他需要长期追踪
变化信号、但不希望建立第二个全文数据库的工作流。

## 核心能力

- RSS 和 Atom 订阅与刷新。
- 没有可用 Feed 时的网页订阅快照。
- 从网页发现 Feed。
- 本地 SQLite 元数据存储。
- 条目搜索、阅读状态和来源健康检查。
- OPML 导入和导出。
- 通过 MCP stdio 返回结构化 JSON 和 Markdown 回退内容。

### 明确不做的事情

- 不运行后台 scheduler 或内置周期性 Digest。
- 不绕过 CAPTCHA、登录、付费墙或反爬挑战。
- 不自动订阅外部搜索结果。
- 不静默修改用户 profile。
- 不默认承诺永久保存全文。

已发布的 0.2.x 服务在请求时可能缓存文章正文。目标策略是链接优先，详见
[数据和隐私](#数据和隐私)。

## 架构

```text
MCP Host（Codex / Claude / 其他）
  -> feed-all MCP 服务
      -> 订阅管理
      -> 刷新协调
      -> 合规抓取层
      -> 本地元数据存储

Host Agent
  -> 筛选 -> 事件去重 -> 总结 -> 提醒
  -> 必要时请求选中正文

用户 / 上层应用
  -> 确认订阅和保存
  -> 维护 profile 和长期知识产物
```

| 层 | 职责 |
| --- | --- |
| `feed-all` | 来源、订阅、刷新、链接、元数据、阅读状态 |
| Agent | 相关性、事件归并、总结、提醒和调度 |
| 用户 | 确认订阅、保存和 profile 修改 |
| 上层应用 | 长期文档、分析产物、本体和用户画像 |

`feed-all` 没有 scheduler。Host Agent 可以在用户配置的定时任务中调用
`rss_refresh`，之后总结或提醒用户。

## 快速开始

### 从 PyPI 安装

```bash
uvx feed-all
```

### Claude Code、Claude Desktop、Cline 和 JSON MCP Host

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

### 本地 checkout

```json
{
  "command": "uv",
  "args": ["run", "--directory", "/绝对路径/rss-plugin", "feed-all"]
}
```

多个 Host 共用一台机器时，建议显式设置本地数据目录：

```bash
FEED_ALL_DATA_DIR="$HOME/.feed-all" uvx feed-all
```

配置后使用 MCP `tools/list` 验证服务，再调用 `rss_list_feeds` 或
`rss_check_health`。

## Agent 工作流

```text
发现 -> 验证 -> 订阅 -> 刷新 -> 筛选
  -> 读取选中条目 -> 总结或提醒
```

先使用元数据和 preview，保持列表 limit 较小。只对需要分析的选中条目读取
链接正文。可以读取用户 profile 进行排序，但修改前必须确认。

## 数据和隐私

默认使用 `~/.feed-all` 下的本地 SQLite，可通过 `FEED_ALL_DATA_DIR` 配置。

### 默认持久化元数据

- 来源 URL、标题、分类和健康状态；
- 条目标题、URL、作者和发布时间；
- 有上限的 preview、阅读状态和条目类型；
- OPML 订阅元数据。

### 当前 0.2.x 行为

`rss_get_entry(fetch_content=true)` 可能抓取并持久化链接正文。这是已发布行为，
文档明确说明它，以免与目标保留策略混淆。

### 目标链接优先策略

规划方向是持久化链接、有上限的 preview、hash 和状态。正文、图片、附件和
外部搜索正文默认应是临时内容，除非用户确认由上层流程保存。规划中的保留
设置不是当前 0.2.x 环境变量。

## 合规抓取

服务采用保守的 RSS-first 策略：

- 优先 RSS/Atom，而不是 HTML 抓取；
- 使用可识别的 User-Agent；
- 遵守 robots.txt、站点条款、访问控制和频率限制；
- 在支持时使用条件请求并遵守 `Retry-After`；
- 校验重定向目标，执行响应大小和 SSRF 限制；
- 遇到 CAPTCHA、登录、付费墙或反爬挑战页时停止；
- 将 401/403 视为授权失败，而不是绕过提示；
- 对 429 和 5xx 分类处理，避免反复请求来源。

隐身爬取、破解 CAPTCHA、复用凭证、为规避限制而轮换代理，以及未经授权
访问私有或付费内容均不在范围内。

## 发现和个性化

订阅和外部发现是两种不同的行为：

```text
订阅 = 长期、稳定、用户确认的来源
发现 = 临时、广泛、用于补充召回的候选
```

规划中的发现流程：

```text
查询 -> provider -> 标准化候选 -> URL/标题去重
     -> Agent 相关性判断 -> 用户确认
     -> 保存链接或建立订阅
```

外部候选不应静默进入订阅数据库，也不应保存为全文文章。规划中的 provider
和工具不是已发布 0.2.x MCP 工具。

Profile 可以包含 `Role`、`Tracking Goals`、`High-Value Signals`、
`Preferred Sources`、`Excluded Topics`、`Discovery Policy` 和
`Output Preference`。安全流程是：

```text
Agent 提议变化 -> 用户确认 -> Host 更新 profile
                -> 后续排序使用已确认 profile
```

使用双语的[用户偏好模板](templates/user-profile.zh-CN.md)。

## MCP 工具

运行时 `tools/list` 是权威来源。已发布 0.2.x 工具按任务分类如下，完整说明
见[工具参考](references/tools.zh-CN.md)，英文版见
[references/tools.md](references/tools.md)。

| 意图 | 工具 |
| --- | --- |
| 发现或预览 | `rss_discover`、`rss_fetch` |
| 订阅和管理 | `rss_subscribe`、`rss_list_feeds`、`rss_update_feed`、`rss_unsubscribe` |
| 刷新和诊断 | `rss_refresh`、`rss_check_health` |
| 搜索和阅读 | `rss_list_entries`、`rss_search`、`rss_get_entry`、`rss_mark_read` |
| 迁移 | `rss_export_opml`、`rss_import_opml` |

响应封装和 Markdown 回退内容见[响应契约](references/response-contract.zh-CN.md)。

## 仓库说明

```text
SKILL.md                         Host 可读取的 canonical skill 入口
skill/SKILL.md                   English Agent skill
skill/SKILL.zh-CN.md             中文 Agent skill
skill/compatibility.md           MCP Host 兼容性
skill/compatibility.zh-CN.md     MCP Host 兼容性（中文）
references/tools.md              已发布工具契约
references/response-contract.md  JSON 和 Markdown 响应契约
references/capabilities.json     来源目录
templates/user-profile.md        用户偏好模板
```

`rss-plugin-installer` 兼容名称继续保留，并指向 canonical `feed-all` skill。

## 版本规则

文档区分：

- **Current 0.2.x**：已发布的 MCP 工具和行为；
- **Planned 0.3.x**：设计方向，不代表可用 API；
- **Experimental**：明确标注的实验能力；
- **Out of scope**：skill 不应执行的行为。

规划工具不能被写成当前可用的 MCP 工具。

## 故障排查

1. 修改 MCP 配置后重启 Host。
2. 确认 `uvx feed-all` 启动时没有立即输出错误。
3. 检查 MCP `tools/list`，它是运行时契约。
4. 调用 `rss_list_feeds` 确认数据目录和订阅来源。
5. 对失败来源调用 `rss_check_health(revalidate=true)`。
6. 将 401/403 视为授权失败，429 视为限流，5xx 视为来源侧故障，挑战页视为
   不支持。

详见[故障排查参考](references/troubleshooting.md)。

## 贡献

文档修改必须同步更新中英文 README，并保持章节结构对应。不要声明已发布
MCP 服务不存在的能力。Python 环境命令统一使用 `uv`，skill 必须保持跨
MCP Host 可用。

## 许可证

MIT，详见 [LICENSE](LICENSE)。
