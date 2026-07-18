---
name: rss-plugin-installer
description: RSS MCP 插件安装与基础使用；兼容 GitHub 上游 skill 名称，实际能力由 feed-all MCP 服务提供
metadata:
  canonical_skill: feed-all
  mcp_server: feed_all
  copaw:
    requires:
      tools:
        - read_file
        - write_file
        - bash
---

# RSS MCP Plugin Installer - 兼容入口

本 skill 保留原有 `rss-plugin-installer` 名称，以兼容已有客户端配置。
规范能力名称为 `feed-all`，实际工具以 MCP `tools/list` 为准。

## 何时使用

当用户需要 **安装、配置或使用独立的 RSS MCP 插件** 时使用本技能：

| 用户意图 | 典型表达 |
|---------|---------|
| 安装RSS插件 | "安装RSS插件"、"设置RSS"、"配置RSS" |
| 订阅源 | "订阅一个RSS"、"添加订阅"、"追踪这个网站" |
| 查看订阅 | "我的订阅有哪些"、"列出所有订阅" |
| 刷新文章 | "刷新文章"、"获取最新内容" |
| 浏览文章 | "看文章"、"浏览内容"、"有什么新文章" |
| 检查健康 | "RSS源健康吗"、"检查订阅状态" |
| 被屏蔽的源 | "PubMed打不开"、"需要VPN的源"、"GFW阻止了" |
| 合规检查 | "这个源合规吗"、"国内能访问吗" |

**触发词**: RSS、订阅、feed、安装、配置、MCP 服务器、VPN、合规、屏蔽、GFW

## 安装流程

### 前提条件

- 系统已安装 `uv`（Python 包管理器）

### 步骤 1：配置到 MCP 客户端

如果目标版本已经发布到 PyPI，在 `~/.claude/settings.json` 中添加：

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

如果使用本地 checkout，改为：

```json
{
  "mcpServers": {
    "feed-all": {
      "command": "uv",
      "args": ["run", "--directory", "/ABSOLUTE/PATH/rss-plugin", "feed-all"]
    }
  }
}
```

Codex 使用等价的 TOML 配置：

```toml
[mcp_servers.feed_all]
command = "uvx"
args = ["feed-all"]
```

本地开发时将 `uvx` 配置替换为上面的 `uv run --directory` 配置。

### 步骤 2：验证连通性

```python
rss_list_feeds()
# → {"success": true, "count": 0, "feeds": []}
```

## 安装检查清单

参考 `references/installation-check.md` 逐项确认。

## 工具参考

所有工具的签名和参数说明见 `references/tools.md`。

## RSS 订阅源目录

Skill 附带了一个收录 **344+ 预配置订阅源**的目录（`references/capabilities.json`），涵盖学术、新闻、金融、医学、博客等多个领域。

### 通过 `read_file` 直接读取 JSON

Agent 可以直接读取 JSON 文件进行搜索分析：

```python
import json
data = json.loads(read_file("references/capabilities.json"))
caps = data["capabilities"]

# 搜索 AI 相关源
ai_sources = [c for c in caps if "AI" in c.get("name","") or "AI" in c.get("description","")]

# 按分类查找
medical = [c for c in caps if c.get("category") == "medical_news"]

# 只看可用源
available = [c for c in caps if c.get("subscription_method") != "unavailable"]
```

### 查询示例

```python
# 查找 arXiv 及其子主题
arxiv = next(c for c in caps if c["id"] == "arxiv.org")
print(arxiv["name"], arxiv["rss_config"]["direct_url"])
for topic in arxiv.get("topics", []):
    print(f"  ├─ {topic['name']}: {topic['full_url']}")

# 查找需要 VPN 的源
vpn_sources = [c for c in caps if c.get("metadata",{}).get("requires_vpn")]

# 查看源的可用性状态
for c in caps[:5]:
    health = c.get("health", {})
    print(f"  {c['name']}: {health.get('status')} ({health.get('success_rate_7d', 'N/A')})")
```

JSON 文件路径：`references/capabilities.json`

### 更新目录

`capabilities.json` 是静态文件。如需从上游同步最新版本：

```bash
# 替换为你的 EntroFeed 项目路径
cp /path/to/entrofeed/src/services/feed/rss_capability/capabilities.json \
   references/capabilities.json
```

也可用 Agent 直接执行：

```python
import shutil
shutil.copy(
    "src/services/feed/rss_capability/capabilities.json",
    "rss-plugin/rss_plugin/capabilities.json"
)
```

---

## 基础使用流程

### 场景一：订阅一个 RSS 源

```python
rss_subscribe(
    url="https://hnrss.org/frontpage",
    name="Hacker News",
    category="tech"
)
```

返回 feed 信息和初始文章数。订阅时自动验证 URL 的有效性。

### 场景二：预览再订阅

```python
# 先预览（不存储，不消耗 Token）
rss_fetch(url="https://hnrss.org/frontpage", count=5)

# 确认后再订阅
rss_subscribe(url="https://hnrss.org/frontpage", category="tech")
```

### 场景三：浏览文章

```python
rss_list_feeds()                         # 查看所有订阅（含健康信息）
rss_list_entries(limit=20)               # 最新文章
rss_list_entries(unread_only=True)       # 仅未读
rss_list_entries(feed_id="abc123")       # 只看某个源
rss_get_entry(entry_id="abc123")         # 获取全文（自动标记已读）
```

### 场景四：刷新订阅

```python
rss_refresh()                            # 刷新全部
rss_refresh(feed_id="abc123")            # 刷新单个
```

刷新时自动追踪健康状态：失败自增计数器，成功归零，10 次连续失败后自动禁用。

### 场景五：检查健康状态

```python
rss_check_health()                       # 检查所有源
rss_check_health(feed_id="abc123", revalidate=True)  # 实时验证
```

### 场景六：导入导出

```python
rss_export_opml()                        # 导出为 OPML
rss_import_opml(opml_content="...")      # 从 OPML 导入
```

### 场景七：网页追踪

```python
# 快照模式 — 追踪单页内容变化
rss_subscribe(url="https://example.com/news", feed_type="webpage")

# 列表提取模式 — 使用 CSS 选择器提取文章列表
rss_subscribe(url="https://example.com/news", feed_type="webpage",
              name="Example News")
```

---

## 禁用与合规场景

### 场景八：被屏蔽/需要 VPN 的源

部分学术和社交网站在中国境内无法直接访问，需要在订阅时标记并要求 VPN。

**判断一个源是否需要 VPN：**

| 源 | 可访问性 | 建议 |
|----|---------|------|
| PubMed (`pubmed.ncbi.nlm.nih.gov`) | ⚠️ 需 VPN | 标记 `requires_vpn=True` |
| 谷歌学术 (`scholar.google.com`) | ⚠️ 需 VPN | 标记 `requires_vpn=True` |
| Twitter/X (`x.com`, `twitter.com`) | ⚠️ 需 VPN | 标记 `requires_vpn=True` |
| YouTube | ⚠️ 需 VPN | 标记 `requires_vpn=True` |
| Reddit | ⚠️ 需 VPN | 标记 `requires_vpn=True` |
| 国内站点 | ✅ 正常 | `requires_vpn=False`（默认） |

**订阅时标记 VPN 需求：**

```python
rss_subscribe(
    url="https://pubmed.ncbi.nlm.nih.gov/rss/search/...",
    name="PubMed AI Research",
    category="academic",
    requires_vpn=True
)
```

**后续修改 VPN 标记：**

```python
rss_update_feed(feed_id="abc123", requires_vpn=True)    # 标记为需 VPN
rss_update_feed(feed_id="abc123", requires_vpn=False)   # 取消 VPN 标记
```

**查看哪些源标记了 VPN：**

```python
rss_list_feeds()
# 返回中包含 requires_vpn 字段
```

**刷新时 VPN 保护机制：**

插件本身不检测 VPN 状态。Agent 侧在调用 `rss_refresh` 前应：

1. 检查要刷新的源中是否有 `requires_vpn=True` 的源
2. 如果有，先确认 VPN 是否已连接
3. 如果 VPN 未连接，跳过这些源或提示用户先连接 VPN

```python
# Agent 侧的检查逻辑（伪代码）
feeds = rss_list_feeds()
vpn_feeds = [f for f in feeds if f.get("requires_vpn")]
non_vpn_feeds = [f for f in feeds if not f.get("requires_vpn")]

# 如果 VPN 未连通，只刷新不需要 VPN 的源
if not vpn_connected and vpn_feeds:
    print(f"⚠️ VPN 未连接，跳过 {len(vpn_feeds)} 个需要 VPN 的源")
    rss_refresh()  # 只刷新 non_vpn_feeds
```

### 场景九：源被屏蔽/过载的识别与处理

当刷新源失败时，从错误信息判断原因：

| 错误特征 | 可能原因 | 处理 |
|---------|---------|------|
| `connect: Connection refused` / `No route to host` | GFW 屏蔽 / 网络隔离 | 标记 `requires_vpn=True` |
| `HTTP 403` / `HTTP 429` | 服务器拒绝 / 限流 | 增加 `check_interval_units` 降低频率 |
| `HTTP 5xx` | 服务器故障 | 临时性，下次刷新可能恢复 |
| `challenge` | Cloudflare 防护 | 无法绕过，考虑替代源 |
| `Read timed out` | 网络超时 | 可能是 VPN 断开或源不稳定 |
| `Invalid feed: ...` | RSS XML 损坏 | 源可能更换了格式 |

```python
# 诊断流程
health = rss_check_health(feed_id="abc123", revalidate=True)
error = health["feeds"][0].get("revalidate_error")

if error and any(kw in error for kw in ["refused", "route", "timeout"]):
    rss_update_feed(feed_id="abc123", requires_vpn=True)
    print("建议开启 VPN 后重试")

if error and ("429" in error or "Too Many" in error):
    rss_update_feed(feed_id="abc123", check_interval_units=12)
    print("降低刷新频率，已调整为每小时一次")
```

### 场景十：合规检查清单

订阅外部源时应考虑：

```
□ 源是否在中国境内可访问？
   ├── 否 → 标记 requires_vpn=True
   └── 是 → 正常订阅

□ 源的内容是否符合使用场景？
   ├── 学术/公开信息 → 通常合规
   ├── 内部/付费内容 → 确认有权访问
   └── 个人隐私数据 → ⚠️ 不应订阅

□ 源的 robots.txt 是否允许爬取？
   ├── 允许 → 正常
   └── 禁止 → 考虑使用官方 API 代替

□ 源的刷新频率是否合理？
   ├── 新闻/博客 → 15-30 分钟
   ├── 学术 RSS → 每小时
   └── 网页快照 → 每小时（避免被 ban）
```

## 配置管理

### 修改订阅设置

```python
# 禁用刷新（保留订阅和文章）
rss_update_feed(feed_id="abc123", refresh_enabled=False)

# 重新启用
rss_update_feed(feed_id="abc123", refresh_enabled=True)

# 修改分类
rss_update_feed(feed_id="abc123", category="academic")

# 修改标签
rss_update_feed(feed_id="abc123", tags=["AI", "bio", "china"])
```

## 存储与健康追踪

存储结构、健康字段说明、自动禁用规则见 `references/health-tracking.md`。

## 故障排查

常见问题及解决方法见 `references/troubleshooting.md`。

## 常见问题

### Q: 源被屏蔽了怎么办？
1. 用 `rss_check_health` 检查具体错误
2. 如果是连接拒绝/超时，设置 `requires_vpn=True`
3. 连接 VPN 后重新刷新

### Q: 如何重新启用自动禁用的源？
```python
rss_update_feed(feed_id="abc123", refresh_enabled=True)
```

### Q: 如何修改刷新间隔？
```python
rss_update_feed(feed_id="abc123", check_interval_units=12)
# 12 单位 ≈ 1 小时
```

### Q: 如何迁移订阅？
```python
rss_export_opml()  # 导出
rss_import_opml(opml_content="...")  # 导入
```

## 与其他 SKILL 的关系

| SKILL | 关系 | 说明 |
|-------|------|------|
| `rss-hunter` | 互补 | rss-hunter 发现源，本 skill 安装插件和管理源 |
| `rss-daily-digest` | 下游 | digest 利用已订阅的源生成摘要 |
| `pipeline-orchestrator` | 配合 | 可配置定时 pipeline 执行 rss_refresh |

## 避免事项

- ❌ 不要重复安装 — 检查 `~/.rss-plugin/data.db` 是否已存在
- ❌ 不要手动修改 SQLite 文件 — 始终使用 MCP 工具操作
- ❌ 不要订阅未验证的 URL — 先用 `rss_discover` 或 `rss_fetch` 验证
- ❌ 不要忽视健康状态 — `auto_disabled` 的源不会自动刷新
- ❌ 不要在 VPN 断开时刷新需要 VPN 的源 — 先确认 VPN 连通
- ❌ 不要高频刷新网页源 — 可能被源站 ban IP
- ❌ 不要订阅需要付费/登录才能访问的内容 — 无法正常获取
