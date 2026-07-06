# Feed All - RSS/Feed MCP Skill

[![PyPI](https://img.shields.io/pypi/v/feed-all)](https://pypi.org/project/feed-all/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

用于安装和配置 [feed-all](https://pypi.org/project/feed-all/) MCP 插件的 Skill 文档。

**feed-all** 是一个全功能的 RSS/Feed MCP 插件，支持：
- RSS/Atom 订阅、刷新、浏览
- 网页追踪（快照、列表提取、站点解析器）
- 健康追踪 + 自动禁用（10 次失败后自动停止）
- 持久化 SQLite 存储
- OPML 导入/导出
- 344+ 预配置订阅源目录

## 快速开始

### 1. 安装 MCP 插件

```bash
uvx feed-all
```

### 2. 配置 Claude Code

在 `~/.claude/settings.json` 中添加：

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

### 3. 使用

```python
# 订阅一个源
rss_subscribe(url="https://hnrss.org/frontpage", name="Hacker News", category="tech")

# 查看订阅
rss_list_feeds()

# 浏览文章
rss_list_entries(limit=20)

# 获取全文
rss_get_entry(entry_id="abc123")

# 检查健康
rss_check_health(revalidate=True)
```

## 文档结构

```
feed-all-skill/
├── SKILL.md                          # 主技能文档（安装 + 使用场景 + 合规）
├── README.md                         # 本文件
├── LICENSE                           # MIT 许可证
├── references/
│   ├── capabilities.json             # 344+ 预配置订阅源目录
│   ├── tools.md                      # 13 个工具的完整签名
│   ├── health-tracking.md            # 健康追踪说明
│   ├── installation-check.md         # 安装检查清单
│   └── troubleshooting.md            # 故障排查
└── templates/
    └── subscription_report.md        # 订阅报告模板
```

## 与 Claude Code / Cline / Codex 配合

本 Skill 适用于任何支持 MCP 协议和 Skill 文档的 AI 客户端：

- **Claude Code** — 配置 `mcpServers` 后可用
- **Cline** — 同上
- **Codex / OpenClaw** — 配置 MCP 服务器后，Agent 可读取 `SKILL.md` 学习使用方法

## 维护

### 更新 capabilities.json

```bash
# 从 EntroFeed 项目同步
cp path/to/entrofeed/src/services/feed/rss_capability/capabilities.json \
   references/capabilities.json
```

## License

MIT