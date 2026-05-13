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

## 协议

本仓库本身采用 [MIT License](LICENSE)。

仓库内聚合的插件归各自原作者所有，遵循其各自的许可证。
