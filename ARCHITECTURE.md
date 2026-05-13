# cc-plugin-marketplace — 目录结构与分工

本文档说明 cc-plugin-marketplace 仓库的目录层级、各文件的职责，以及从上游同步到用户安装的完整链路。

## 目录总览

```
cc-plugin-marketplace/          ← Git 仓库根目录
│
├── .claude-plugin/             ← 市场自身配置
│   └── marketplace.json        ← Claude Code 市场清单（自动生成）
│
├── plugins/                    ← 插件存放目录（61 个）
│   ├── {plugin-name}/          ← 单个插件的根目录
│   │   ├── .claude-plugin/     ← 插件元数据
│   │   │   └── plugin.json     ← 插件声明文件
│   │   ├── source.json         ← 该插件的上游来源记录
│   │   ├── skills/             ← 技能目录（Markdown 指令文件）
│   │   ├── commands/           ← 斜杠命令目录
│   │   ├── agents/             ← 子 Agent 目录
│   │   ├── hooks/              ← 事件钩子目录
│   │   ├── rules/              ← 项目规则目录
│   │   └── .mcp.json           ← MCP 服务配置（可选）
│   └── ...
│
├── sources.json                ← 全局来源注册中心
├── README.md                   ← 用户使用手册
├── ARCHITECTURE.md             ← 本文件 — 目录结构与分工
└── LICENSE                     ← MIT License
```

---

## 核心文件

### `.claude-plugin/marketplace.json`

**角色**：Claude Code 市场清单。用户执行 `/plugin marketplace add <url>` 后，Claude Code 读取此文件来获知市场包含哪些插件。

**生成方式**：由 `sync_plugins.py` 自动生成，每次同步后重建。不以手动编辑。

**字段说明**：

| 字段 | 说明 |
|------|------|
| `name` | 市场名称，固定为 `"cc-plugin-marketplace"` |
| `description` | 市场简介 |
| `owner` | 维护者信息 |
| `plugins[].name` | 插件标识符（安装名） |
| `plugins[].description` | 插件简介 |
| `plugins[].category` | 分类：`development` / `productivity` / `community` / `database` 等 |
| `plugins[].source` | 插件在仓库内的相对路径，指向该插件的 `.claude-plugin/` 目录 |

### `sources.json`

**角色**：插件来源全局注册中心。记录每个插件的上游仓库、同步状态、commit SHA，是市场内容的可追溯性基础。

**字段说明**：

| 字段 | 说明 |
|------|------|
| `version` | 注册中心格式版本 |
| `last_updated` | 上次同步时间 |
| `plugins[].name` | 插件标识符 |
| `plugins[].source.type` | 来源类型：`github_single`（独立仓库）/ `github_marketplace`（市场打包）/ `manual`（手动收录） |
| `plugins[].source.repo_url` | 上游 Git 仓库地址 |
| `plugins[].source.repo_ref` | 跟踪的分支（通常为 `main`） |
| `plugins[].source.commit_sha` | 上次同步时的上游 commit SHA |
| `plugins[].source.last_sync_status` | 同步状态：`success` / `pending` / `failed` / `unchanged` |
| `remotes[]` | 推送远端配置（GitHub + Gitee） |

---

## 插件目录结构

每个插件位于 `plugins/{name}/`，遵循 Claude Code 插件规范。

### `plugins/{name}/.claude-plugin/plugin.json`

**角色**：插件声明文件，Claude Code 发现并加载插件的入口。

**必要字段**：

| 字段 | 类型 | 说明 |
|------|------|------|
| `name` | string | 插件标识符 |
| `description` | string | 插件功能介绍 |
| `version` | string | 语义化版本号（推荐） |
| `author` | object | 作者信息（name, email） |

**路径声明字段**（告诉 Claude Code 到哪里找内容）：

| 字段 | 类型 | 说明 | 例 |
|------|------|------|----|
| `skills` | string[] 或 string | 技能目录路径 | `["./skills/foo", "./skills/bar"]` |
| `commands` | string[] 或 string | 斜杠命令目录 | `"./commands/"` |
| `mcpServers` | string 或 object | MCP 服务配置 | `"./.mcp.json"` 或内联 JSON |

> **注意**：`agents/`、`hooks/`、`rules/` 目录无需在 plugin.json 中声明 —— Claude Code v2.1+ 按约定自动发现。

### `plugins/{name}/source.json`

**角色**：单插件级别的来源快照。记录该插件从哪个仓库、哪个 commit 同步而来。

与全局 `sources.json` 的区别：
- `sources.json` — 全局注册表，供同步脚本读取和更新
- `source.json` — 插件级快照，随插件内容一起推送，供消费者追溯

### `plugins/{name}/skills/`

**角色**：存放技能文件（`SKILL.md`）。每个子目录为一个独立技能。

Skill 文件是 Markdown 格式的指令文档，告诉 Claude 在特定场景下如何行为。Claude 在对话中根据上下文按需加载相关 Skill。

典型结构：
```
skills/
├── code-review/
│   └── SKILL.md          ← 技能指令文件
├── debugging/
│   ├── SKILL.md
│   └── examples.md       ← 辅助参考文件
```

### `plugins/{name}/commands/`

**角色**：存放斜杠命令文件（`.md`），每个文件对应一个 `/` 命令。

命令被 Claude Code 读取为 prompt 模板，用户输入 `/命令名` 时触发：
```
commands/
├── ask.md        →  /ask
├── review.md     →  /review
└── setup.md      →  /setup
```

### `plugins/{name}/agents/`

**角色**：存放子 Agent 定义文件（`.md`）。每个文件定义一个可由 Claude 调度的子 Agent。

Agent 与 Skill 的区别：
- **Skill** — 提供给 Claude 自身的行为指引
- **Agent** — 定义一个独立子任务执行者，Claude 可将复杂任务委托给它

### `plugins/{name}/hooks/`

**角色**：存放事件钩子脚本，在 Claude Code 的特定生命周期事件触发时执行。

常见钩子：`session-start`（会话开始时执行）、`post-tool-use`（工具调用后触发）。

### `plugins/{name}/.mcp.json`

**角色**：MCP（Model Context Protocol）服务配置。告诉 Claude Code 启动哪些外部进程作为工具提供者。

两种模式：

**本地进程（内网可用）**：
```json
{
  "my-server": {
    "command": "node",
    "args": ["${CLAUDE_PLUGIN_ROOT}/bridge/mcp-server.cjs"]
  }
}
```

**远程 HTTP（需联网）**：
```json
{
  "my-server": {
    "type": "http",
    "url": "https://api.example.com/mcp"
  }
}
```

---

## 同步链路

```
sources.json                  ← 定义要同步哪些插件、从哪同步
       │
       ▼
  sync_plugins.py             ← 同步引擎
       │
       ├─ 从上游 git clone → 提取 plugin.json + 内容文件
       ├─ 存入 plugins/{name}/
       ├─ 更新 sources.json commit SHA
       └─ 重新生成 .claude-plugin/marketplace.json
       │
       ▼
  git push → GitHub + Gitee   ← 双远端推送
       │
       ▼
  用户 /plugin marketplace add <url>
       │
       ▼
  /plugin install <name>       ← Claude Code 从 Git 读取，无需网络
```

### 同步脚本工作流

1. **读 sources.json**：遍历所有插件条目
2. **检查更新**：`git ls-remote` 对比上游 commit SHA
3. **两阶段克隆**（`github_single` 类型）：
   - Phase 1：稀疏检出 `.claude-plugin/` → 读取 plugin.json 获取内容路径
   - Phase 2：展开稀疏检出 → 拉取 skills/、commands/、agents/ 等
4. **内容提取**：扫描 CLAUDE_PLUGIN_ROOT 引用链，确保间接依赖目录（如 bridge/、scripts/、templates/）也被纳入
5. **写入本地**：copy 到 `plugins/{name}/`
6. **更新注册表**：更新 sources.json 的 commit SHA 和同步状态
7. **生成市场清单**：重建 marketplace.json
8. **Git 提交 + 推送**：提交并推送到 GitHub + Gitee

### 同步模式

| 模式 | 适用场景 | 来源识别方式 |
|------|---------|------------|
| `github_single` | 独立插件仓库（如 superpowers、oh-my-claudecode） | `repo_url` + `repo_ref` |
| `github_marketplace` | 市场打包型插件（如 document-skills） | `repo_url` + `marketplace_plugin_name` |
| `manual` | 从 Anthropic 官方市场直接收录，无独立上游 | 来源记录为官方市场路径 |

---

## 市场与单仓库的关系

```
internal-plugin-marketplace (开发项目)
│
├── scripts/sync_plugins.py   ← 同步引擎（不在此仓库）
├── backend/                  ← Web 管理后台（不在此仓库）
├── frontend/                 ← Web 前端（不在此仓库）
│
└── plugins-repo/ ───────────────────────────────┐
    │                                             │
    │ git init; push → GitHub + Gitee             │
    │                                             │
    └─────────────────────────────────────────────┘
                    │
                    ▼
         cc-plugin-marketplace (发布仓库)
         用户 /plugin marketplace add 的目标
```

- **`internal-plugin-marketplace`** 是开发项目，包含同步脚本、Web 后台等工具
- **`cc-plugin-marketplace`**（即 `plugins-repo/`）是发布产物——一个纯 Git 仓库，只包含插件内容和元数据

## 相关文档

- [README.md](README.md) — 用户使用指南
- [sources.json](sources.json) — 插件来源注册中心（数据文件）
- [.claude-plugin/marketplace.json](.claude-plugin/marketplace.json) — 市场清单（自动生成）
