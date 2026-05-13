# cc-plugin-marketplace

**Claude Code 插件聚合市场** — 61 个热门插件，内容本地化存储，安装无需访问外网。

## 为什么需要它

国内开发者在公司网络环境下经常无法访问 GitHub（防火墙限制、纯内网隔离）。Claude Code 的官方插件市场托管在 GitHub，直接访问会失败。

本仓库将所有插件内容 **Vendored（预下载）** 到仓库内部，Claude Code 安装插件时不需要任何外部网络请求。

| 你的网络环境 | 使用方式 |
|-------------|---------|
| 能访问 GitHub | 添加 GitHub 仓库地址 |
| GitHub 被墙 | 添加 Gitee 镜像地址 |
| 完全内网隔离 | 下载后上传到内部 GitLab，再添加内部地址 |

## 仓库地址

| 平台 | URL |
|------|-----|
| **GitHub** | `https://github.com/lizz0725/cc-plugin-marketplace.git` |
| **Gitee**（国内镜像） | `https://gitee.com/lizz0725/cc-plugin-marketplace.git` |

默认分支：`master`

## 快速开始

### 1. 添加市场

在 Claude Code 终端中执行：

```bash
# 从 GitHub（可访问外网时）
/plugin marketplace add https://github.com/lizz0725/cc-plugin-marketplace.git

# 从 Gitee（GitHub 不可达时）
/plugin marketplace add https://gitee.com/lizz0725/cc-plugin-marketplace.git
```

### 2. 安装插件

```bash
/plugin install superpowers          # 核心技能库：TDD、调试、子 Agent 开发
/plugin install code-review          # 自动化 PR Review
/plugin install ui-ux-pro-max        # UI/UX 设计智能体
/plugin install ecc                  # 58 Agent + 220 Skill 综合工具包
/plugin install oh-my-claudecode     # 多 Agent 协调系统
/plugin install plugin-dev           # 插件开发工具包
```

### 3. 查看已安装

```bash
/plugin list
```

## 离线 / 内网部署

适用于完全隔离的内网开发环境（无任何互联网访问）。

### 第一步：在有网的机器上下载仓库

```bash
git clone https://gitee.com/lizz0725/cc-plugin-marketplace.git
```

### 第二步：上传到内部 Git 仓库

```bash
cd cc-plugin-marketplace
git remote add internal https://gitlab.internal.company.com/your-team/cc-plugin-marketplace.git
git push internal master
```

支持任意 Git 托管平台：**GitLab、Gitea、Gogs、Gitee 企业版** 等。

### 第三步：在内网机器上添加市场

```bash
/plugin marketplace add https://gitlab.internal.company.com/your-team/cc-plugin-marketplace.git
```

然后照常使用 `/plugin install <name>` 安装。所有插件内容已在仓库内，**无需任何 Internet 访问**。

## 插件目录

当前收录 **61 个插件**，按类别分组：

### 开发工具（29）

`agent-sdk-dev` `ai` `base44` `chrome-devtools-mcp` `code-modernization` `context7` `expo` `feature-dev` `firecrawl` `frontend-design` `greptile` `laravel-boost` `liquid-lsp` `liquid-skills` `mcp-server-dev` `microsoft-docs` `mintlify` `oracle-ai-data-platform-workbench-spark-connectors` `outputai` `playground` `plugin-dev` `qodo-skills` `qt-development-skills` `quarkus-agent` `ralph-loop` `serena` `skill-creator` `superpowers` `terraform`

### 效率工具（15）

`agent-skills` `atlassian` `box` `claude-code-setup` `claude-md-management` `code-review` `code-simplifier` `coderabbit` `commit-commands` `cwc-makers` `desktop-commander` `gitlab` `hookify` `pr-review-toolkit` `windsor-ai`

### 社区热门（8）

`andrej-karpathy-skills` `caveman` `document-skills` `ecc` `example-skills` `oh-my-claudecode` `remember` `ui-ux-pro-max`

### 数据库（4）

`clickhouse` `cockroachdb` `mongodb` `qdrant`

### 其他（5）

`explanatory-output-style` `learning-output-style` `math-olympiad` `playwright` `security-guidance`

---

### 重点推荐

| 插件 | 简介 | 规模 |
|------|------|:--:|
| `ecc` | Everything Claude Code：全部功能集成 | 58 Agent / 220 Skill |
| `oh-my-claudecode` | 多 Agent 协调系统，自带本地 MCP | 38 Skill / 27 Cmd / 19 Agent |
| `ui-ux-pro-max` | UI/UX 设计智能体 | 67 风格 / 161 调色板 |
| `superpowers` | TDD / 调试 / 子 Agent 驱动开发 | 14 技能 |
| `code-review` | 自动化 PR Review，多 Agent 协同 | 评分 + 反馈 |
| `plugin-dev` | 插件开发工具包（Hooks/MCP/Commands） | 7 专家 Skill |
| `playwright` | 微软浏览器自动化测试 MCP | E2E 测试 |
| `document-skills` | Excel / Word / PPT / PDF 处理 | 4 文档技能 |

## 工作原理

```
上游 GitHub 插件仓库
        │
        ▼
  sync_plugins.py    ← 克隆 → 提取内容 → 存入 plugins-repo
        │
        ▼
  git push ──────►  GitHub (origin)  +  Gitee (gitee)
        │
        ▼
  用户  /plugin marketplace add <url>
        │
        ▼
  /plugin install <name>    ← 直接从 Git 仓库读取，不需要网络
```

- **内容 Vendored**：所有插件的 skill、command、agent、hook、MCP 配置文件直接存在仓库中
- **双远程推送**：每次同步自动推送到 GitHub + Gitee，两边内容一致
- **来源可追溯**：`sources.json` 记录每个插件的上游仓库地址、commit SHA 和同步时间

## 插件来源

本仓库所有插件均收录自开源社区，以下是每个插件的原始仓库地址。

> 标注「Anthropic 官方市场」的插件来自 [anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official)，由官方团队维护并直接收录。

### 社区热门

| 插件 | 源仓库 |
|------|--------|
| `andrej-karpathy-skills` | [forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills) |
| `caveman` | [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman) |
| `document-skills` | [anthropics/skills](https://github.com/anthropics/skills) |
| `ecc` | [affaan-m/everything-claude-code](https://github.com/affaan-m/everything-claude-code) |
| `example-skills` | [anthropics/skills](https://github.com/anthropics/skills) |
| `oh-my-claudecode` | [Yeachan-Heo/oh-my-claudecode](https://github.com/Yeachan-Heo/oh-my-claudecode) |
| `remember` | [Digital-Process-Tools/claude-remember](https://github.com/Digital-Process-Tools/claude-remember) |
| `ui-ux-pro-max` | [nextlevelbuilder/ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) |

### 开发工具

| 插件 | 源仓库 |
|------|--------|
| `agent-sdk-dev` | Anthropic 官方市场 |
| `ai` | [pydantic/skills](https://github.com/pydantic/skills) |
| `base44` | [base44/skills](https://github.com/base44/skills) |
| `chrome-devtools-mcp` | [ChromeDevTools/chrome-devtools-mcp](https://github.com/ChromeDevTools/chrome-devtools-mcp) |
| `code-modernization` | Anthropic 官方市场 |
| `context7` | Anthropic 官方市场 |
| `expo` | [expo/skills](https://github.com/expo/skills) |
| `feature-dev` | Anthropic 官方市场 |
| `firecrawl` | [firecrawl/firecrawl-claude-plugin](https://github.com/firecrawl/firecrawl-claude-plugin) |
| `frontend-design` | Anthropic 官方市场 |
| `greptile` | Anthropic 官方市场 |
| `laravel-boost` | Anthropic 官方市场 |
| `liquid-lsp` | [Shopify/liquid-skills](https://github.com/Shopify/liquid-skills) |
| `liquid-skills` | [Shopify/liquid-skills](https://github.com/Shopify/liquid-skills) |
| `mcp-server-dev` | Anthropic 官方市场 |
| `microsoft-docs` | [MicrosoftDocs/mcp](https://github.com/MicrosoftDocs/mcp) |
| `mintlify` | Anthropic 官方市场 |
| `oracle-ai-data-platform-workbench-spark-connectors` | [oracle-samples/oracle-aidp-samples](https://github.com/oracle-samples/oracle-aidp-samples) |
| `outputai` | [growthxai/output](https://github.com/growthxai/output) |
| `playground` | Anthropic 官方市场 |
| `plugin-dev` | Anthropic 官方市场 |
| `qodo-skills` | [qodo-ai/qodo-skills](https://github.com/qodo-ai/qodo-skills) |
| `qt-development-skills` | [TheQtCompanyRnD/agent-skills](https://github.com/TheQtCompanyRnD/agent-skills) |
| `quarkus-agent` | [quarkusio/quarkus-agent-mcp](https://github.com/quarkusio/quarkus-agent-mcp) |
| `ralph-loop` | Anthropic 官方市场 |
| `serena` | Anthropic 官方市场 |
| `skill-creator` | Anthropic 官方市场 |
| `superpowers` | [obra/superpowers](https://github.com/obra/superpowers) |
| `terraform` | Anthropic 官方市场 |

### 效率工具

| 插件 | 源仓库 |
|------|--------|
| `agent-skills` | [youdotcom-oss/agent-skills](https://github.com/youdotcom-oss/agent-skills) |
| `atlassian` | [atlassian/atlassian-mcp-server](https://github.com/atlassian/atlassian-mcp-server) |
| `box` | [box/box-for-ai](https://github.com/box/box-for-ai) |
| `claude-code-setup` | Anthropic 官方市场 |
| `claude-md-management` | Anthropic 官方市场 |
| `code-review` | Anthropic 官方市场 |
| `code-simplifier` | Anthropic 官方市场 |
| `coderabbit` | [coderabbitai/skills](https://github.com/coderabbitai/skills) |
| `commit-commands` | Anthropic 官方市场 |
| `cwc-makers` | Anthropic 官方市场 |
| `desktop-commander` | [wonderwhy-er/DesktopCommanderMCP](https://github.com/wonderwhy-er/DesktopCommanderMCP) |
| `gitlab` | Anthropic 官方市场 |
| `hookify` | Anthropic 官方市场 |
| `pr-review-toolkit` | Anthropic 官方市场 |
| `windsor-ai` | [windsor-ai/claude-windsor-ai-plugin](https://github.com/windsor-ai/claude-windsor-ai-plugin) |

### 其他

| 插件 | 源仓库 |
|------|--------|
| `clickhouse` | [ClickHouse/clickhouse-claude-code-plugin](https://github.com/ClickHouse/clickhouse-claude-code-plugin) |
| `cockroachdb` | [cockroachdb/claude-plugin](https://github.com/cockroachdb/claude-plugin) |
| `explanatory-output-style` | Anthropic 官方市场 |
| `learning-output-style` | Anthropic 官方市场 |
| `math-olympiad` | Anthropic 官方市场 |
| `mongodb` | [mongodb/agent-skills](https://github.com/mongodb/agent-skills) |
| `playwright` | Anthropic 官方市场 |
| `qdrant` | [qdrant/skills](https://github.com/qdrant/skills) |
| `security-guidance` | Anthropic 官方市场 |

## 协议

本仓库本身采用 [MIT License](LICENSE)。

仓库内聚合的插件归各自原作者所有，遵循其各自的许可证。
