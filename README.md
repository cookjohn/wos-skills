# Web of Science Skills for Claude Code

[English](#english) | [中文](#中文)

| WeChat Official Account (公众号) | WeChat Group (微信群) | Discord |
|:---:|:---:|:---:|
| <img src="qrcode_for_gh_a1c14419b847_258.jpg" width="200"> | <img src="0320.jpg" width="200"> | [Join Discord](https://discord.gg/tGd5vTDASg) |
| 未来论文实验室 | 扫码加入交流群 | English & Chinese |

---

<a id="english"></a>

## English

[Claude Code](https://docs.anthropic.com/en/docs/claude-code) skills that let Claude interact with [Web of Science (WoS)](https://www.webofscience.com) through Chrome DevTools MCP.

Search papers, extract metadata, navigate results, export citations, download PDFs, and push to Zotero — all from the Claude Code CLI.

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI installed
- Chrome browser (institutional login for WoS access)
- [Zotero](https://www.zotero.org/) desktop app (optional, for citation export)
- Python 3 (optional, for Zotero push script)

### Skills

| Skill | Description | Invocation |
|-------|-------------|------------|
| `wos-search` | Search WoS by topic, author, title, DOI. Supports edition/database filtering and sort | `/wos-search value co-creation --edition SSCI --sort citations` |
| `wos-paper-detail` | Extract full record metadata (abstract, JIF, keywords, citations, etc.) | `/wos-paper-detail WOS:000295471900004` |
| `wos-navigate-pages` | Paginate through search results via API or URL | `/wos-navigate-pages 2` |
| `wos-parse-results` | Parse search results from API response or DOM | _(internal)_ |
| `wos-download` | Download article PDFs via publisher links | `/wos-download WOS:000295471900004` |
| `wos-export` | Export citations to Zotero, RIS, BibTeX, or Excel | `/wos-export zotero` |

### Agent

**`wos-researcher`** — orchestrates all skills. Supports multi-step workflows like "search → detail → export to Zotero → download PDF". API-first approach with DOM fallback.

### Key Features

- **API-first**: Uses WoS internal API (`runQuerySearch`) for search and pagination — 1 tool call, structured JSON, no DOM fragility
- **Edition filtering**: Search within SCI, SSCI, CPCI, or any combination
- **Sort control**: By citations, date, relevance, or usage
- **Multiple databases**: Core Collection (default), All Databases, MEDLINE, Preprint, SciELO, etc.
- **JIF & JCR extraction**: Impact Factor, Five-Year IF, and JCR Quartile from full record pages
- **Zotero integration**: Direct push via Connector API with deterministic session IDs (no duplicates)
- **Chinese keyword translation**: Auto-translates Chinese search terms to English for SCI/SSCI

### Installation

#### 1. Install Chrome DevTools MCP server

```bash
claude mcp add chrome-devtools -- npx -y chrome-devtools-mcp@latest
```

#### 2. Install WoS skills

```bash
git clone https://github.com/cookjohn/wos-skills.git
cd wos-skills
cp -r skills/ agents/ .claude/
```

Or add to an existing project:

```bash
git clone https://github.com/cookjohn/wos-skills.git /tmp/wos-skills
cp -r /tmp/wos-skills/skills/ your-project/.claude/skills/
cp -r /tmp/wos-skills/agents/ your-project/.claude/agents/
```

#### 3. Configure anti-detection (recommended)

Add to your `.claude.json` to prevent bot detection:

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": [
        "-y",
        "chrome-devtools-mcp@latest",
        "--ignoreDefaultChromeArg=--enable-automation",
        "--ignoreDefaultChromeArg=--disable-infobars",
        "--chromeArg=--disable-blink-features=AutomationControlled"
      ]
    }
  }
}
```

#### 4. Log in to WoS

Before using the skills, open Chrome and log in to Web of Science with your institutional account. The skills require an active WoS session.

### Usage Examples

```
# Simple topic search
/wos-search deep learning

# Search SSCI for value co-creation, sorted by citations
/wos-search value co-creation --edition SSCI --sort citations

# Multi-field advanced search
/wos-search (builds: TS="AI" AND AU="Smith" AND PY=2020-2025)

# View paper details with JIF and JCR quartile
/wos-paper-detail WOS:000295471900004

# Export to Zotero
/wos-export zotero

# Navigate to page 2
/wos-navigate-pages 2
```

### Architecture

```
wos-skills/
├── skills/
│   ├── wos-search/SKILL.md          # API-based search (1 tool call)
│   ├── wos-paper-detail/SKILL.md    # Full record extraction (2 tool calls)
│   ├── wos-navigate-pages/SKILL.md  # API pagination (1 tool call)
│   ├── wos-parse-results/SKILL.md   # Internal result parser
│   ├── wos-download/SKILL.md        # PDF download via publisher
│   ├── wos-export/
│   │   ├── SKILL.md                 # Export & Zotero push
│   │   └── scripts/
│   │       └── push_to_zotero.py    # Zotero Connector API script
│   └── wos-dom.md                   # DOM & API reference document
└── agents/
    └── wos-researcher.md            # Orchestrator agent
```

### Related Projects

- [sd-skills](https://github.com/cookjohn/sd-skills) — ScienceDirect skills for Claude Code

---

<a id="中文"></a>

## 中文

[Claude Code](https://docs.anthropic.com/en/docs/claude-code) 技能，通过 Chrome DevTools MCP 让 Claude 与 [Web of Science (WoS)](https://www.webofscience.com) 交互。

搜索论文、提取元数据、浏览结果、导出引用、下载 PDF、推送到 Zotero —— 全部通过 Claude Code CLI 完成。

### 前提条件

- 已安装 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- Chrome 浏览器（需要机构登录访问 WoS）
- [Zotero](https://www.zotero.org/) 桌面版（可选，用于引用导出）
- Python 3（可选，用于 Zotero 推送脚本）

### 技能列表

| 技能 | 描述 | 调用方式 |
|------|------|----------|
| `wos-search` | 按主题、作者、标题、DOI 搜索，支持版本/数据库筛选和排序 | `/wos-search 价值共创 --edition SSCI --sort citations` |
| `wos-paper-detail` | 提取论文详细信息（摘要、JIF、关键词、引用数等） | `/wos-paper-detail WOS:000295471900004` |
| `wos-navigate-pages` | 通过 API 或 URL 翻页 | `/wos-navigate-pages 2` |
| `wos-parse-results` | 解析搜索结果（内部技能） | _（内部使用）_ |
| `wos-download` | 通过出版商链接下载 PDF | `/wos-download WOS:000295471900004` |
| `wos-export` | 导出引用到 Zotero、RIS、BibTeX 或 Excel | `/wos-export zotero` |

### 核心特性

- **API 优先**：使用 WoS 内部 API 搜索和翻页 —— 1 次工具调用，结构化 JSON，无 DOM 脆弱性
- **版本筛选**：在 SCI、SSCI、CPCI 或任意组合中搜索
- **排序控制**：按引用数、日期、相关性或使用量排序
- **多数据库**：核心合集（默认）、所有数据库、MEDLINE、预印本、SciELO 等
- **影响因子提取**：从全记录页面提取 JIF、五年 IF 和 JCR 分区
- **Zotero 集成**：通过 Connector API 直接推送，确定性会话 ID 防止重复
- **中文关键词翻译**：自动将中文搜索词翻译为英文用于 SCI/SSCI 搜索

### 安装

#### 1. 安装 Chrome DevTools MCP 服务器

```bash
claude mcp add chrome-devtools -- npx -y chrome-devtools-mcp@latest
```

#### 2. 安装 WoS 技能

```bash
git clone https://github.com/cookjohn/wos-skills.git
cd wos-skills
cp -r skills/ agents/ .claude/
```

#### 3. 配置反检测（推荐）

在 `.claude.json` 中添加以下配置：

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": [
        "-y",
        "chrome-devtools-mcp@latest",
        "--ignoreDefaultChromeArg=--enable-automation",
        "--ignoreDefaultChromeArg=--disable-infobars",
        "--chromeArg=--disable-blink-features=AutomationControlled"
      ]
    }
  }
}
```

#### 4. 登录 WoS

使用前请先在 Chrome 中用机构账号登录 Web of Science。

### 相关项目

- [sd-skills](https://github.com/cookjohn/sd-skills) — ScienceDirect 学术网站 Claude Code 技能
