# Commands 最佳实践

![Last Updated](https://img.shields.io/badge/Last_Updated-Apr%2014%2C%202026%2011%3A13%20PM%20PKT-white?style=flat&labelColor=555) ![Version](https://img.shields.io/badge/Claude_Code-v2.1.107-blue?style=flat&labelColor=555)<br>
[![Implemented](https://img.shields.io/badge/Implemented-2ea44f?style=flat)](../implementation/claude-commands-implementation.md)

Claude Code commands —— frontmatter 字段和官方内置斜杠命令。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## Frontmatter 字段 (14)

| 字段 | 类型 | 必需 | 描述 |
|-------|------|----------|-------------|
| `name` | string | 否 | 显示名称和 `/slash-command` 标识符。省略时默认为目录名 |
| `description` | string | 推荐 | 命令功能。显示在自动补全中，用于 Claude 自动发现 |
| `when_to_use` | string | 否 | Claude 应何时调用此 skill 的额外上下文 —— 触发短语或示例请求。追加到列表中的 `description`，计入 1536 字符上限 |
| `argument-hint` | string | 否 | 自动补全时显示的提示（如 `[issue-number]`, `[filename]`) |
| `disable-model-invocation` | boolean | 否 | 设为 `true` 阻止 Claude 自动调用此命令 |
| `user-invocable` | boolean | 否 | 设为 `false` 从 `/` 菜单隐藏 —— 命令仅作为背景知识 |
| `paths` | string/list | 否 | 限制此 skill 激活时的 glob 模式。接受逗号分隔字符串或 YAML 列表。设置后，Claude 仅在处理匹配模式的文件时自动加载该 skill |
| `allowed-tools` | string | 否 | 此命令激活时无需权限提示即可使用的工具 |
| `model` | string | 否 | 此命令运行时使用的模型（如 `haiku`, `sonnet`, `opus`) |
| `effort` | string | 否 | 调用时覆盖模型努力程度级别（`low`, `medium`, `high`, `max`) |
| `context` | string | 否 | 设为 `fork` 在隔离子代理上下文中运行命令 |
| `agent` | string | 否 | `context: fork` 时的子代理类型（默认：`general-purpose`) |
| `shell` | string | 否 | `` !`command` `` 块的 shell —— 接受 `bash`（默认）或 `powershell`。需要 `CLAUDE_CODE_USE_POWERSHELL_TOOL=1` |
| `hooks` | object | 否 | 限定于此命令的生命周期钩子 |

---

## ![Official](../!/tags/official.svg) **(70)**

### 认证 (Auth)

| # | 命令 | 描述 |
|---|---------|-------------|
| 1 | `/login` | 登录 Anthropic 账户 |
| 2 | `/logout` | 登出 Anthropic 账户 |
| 3 | `/setup-bedrock` | 通过交互式向导配置 Amazon Bedrock 认证、区域和模型绑定。仅在 `CLAUDE_CODE_USE_BEDROCK=1` 时可见。首次 Bedrock 用户也可从登录界面访问 |
| 4 | `/setup-vertex` | 通过交互式向导配置 Google Vertex AI 认证、项目、区域和模型绑定。仅在 `CLAUDE_CODE_USE_VERTEX=1` 时可见 |
| 5 | `/upgrade` | 打开升级页面切换到更高套餐 |

### 配置 (Config)

| # | 命令 | 描述 |
|---|---------|-------------|
| 6 | `/color [color\|default]` | 设置当前会话的提示栏颜色。可用：`red`, `blue`, `green`, `yellow`, `purple`, `orange`, `pink`, `cyan`。用 `default` 重置 |
| 7 | `/config` | 打开设置界面调整主题、模型、输出风格等。别名：`/settings` |
| 8 | `/keybindings` | 打开或创建键盘快捷键配置文件 |
| 9 | `/permissions` | 管理工具权限的允许、询问和拒绝规则。别名：`/allowed-tools` |
| 10 | `/privacy-settings` | 查看和更新隐私设置。仅 Pro 和 Max 用户可用 |
| 11 | `/sandbox` | 切换沙箱模式 |
| 12 | `/statusline` | 配置 Claude Code 的状态栏 |
| 13 | `/stickers` | 购买 Claude Code 贴纸 |
| 14 | `/terminal-setup` | 配置终端键盘绑定 |
| 15 | `/theme` | 更改颜色主题 |
| 16 | `/voice` | 切换按住说话语音输入 |

### 上下文 (Context)

| # | 命令 | 描述 |
|---|---------|-------------|
| 17 | `/context` | 以彩色网格可视化当前上下文使用 |
| 18 | `/cost` | 显示 token 使用统计 |
| 19 | `/extra-usage` | 配置额外用量以便在速率限制时继续工作 |
| 20 | `/insights` | 生成分析 Claude Code 会话的报告 |
| 21 | `/stats` | 可视化每日用量、会话历史等 |
| 22 | `/status` | 打开设置界面显示版本、模型、账户状态 |
| 23 | `/usage` | 显示套餐用量限制和速率限制状态 |

### 调试 (Debug)

| # | 命令 | 描述 |
|---|---------|-------------|
| 24 | `/doctor` | 诊断和验证 Claude Code 安装及设置 |
| 25 | `/feedback [report]` | 提交反馈。别名：`/bug` |
| 26 | `/help` | 显示帮助和可用命令 |
| 27 | `/powerup` | 通过互动课程发现 Claude Code 功能 |
| 28 | `/release-notes` | 查看更新日志 |
| 29 | `/tasks` | 列出和管理后台任务。别名：`/bashes` |

### 导出 (Export)

| # | 命令 | 描述 |
|---|---------|-------------|
| 30 | `/copy [N]` | 将最后的助手响应复制到剪贴板。`/copy 2` 复制倒数第二个 |
| 31 | `/export [filename]` | 将当前对话导出为纯文本 |

### 扩展 (Extensions)

| # | 命令 | 描述 |
|---|---------|-------------|
| 32 | `/agents` | 管理代理配置 |
| 33 | `/chrome` | 配置 Chrome 中的 Claude 设置 |
| 34 | `/hooks` | 查看工具事件的钩子配置 |
| 35 | `/ide` | 管理 IDE 集成 |
| 36 | `/mcp` | 管理 MCP 服务器连接 |
| 37 | `/plugin` | 管理 Claude Code 插件 |
| 38 | `/reload-plugins` | 重载所有活跃插件 |
| 39 | `/skills` | 列出可用的 skills |

### 记忆 (Memory)

| # | 命令 | 描述 |
|---|---------|-------------|
| 40 | `/memory` | 编辑 CLAUDE.md 记忆文件 |

### 模型 (Model)

| # | 命令 | 描述 |
|---|---------|-------------|
| 41 | `/effort [low\|medium\|high\|max\|auto]` | 设置模型努力程度。`low/medium/high` 持久化。`max` 仅当前会话且需 Opus 4.6 |
| 42 | `/fast [on\|off]` | 切换快速模式 |
| 43 | `/model [model]` | 选择或更改 AI 模型 |
| 44 | `/passes` | 与朋友分享一周免费 Claude Code |
| 45 | `/plan [description]` | 进入计划模式 |
| 46 | `/ultraplan <prompt>` | 在 ultraplan 会话中起草计划 |

### 项目 (Project)

| # | 命令 | 描述 |
|---|---------|-------------|
| 47 | `/add-dir <path>` | 添加工作目录 |
| 48 | `/diff` | 打开交互式差异查看器 |
| 49 | `/init` | 用 CLAUDE.md 初始化项目 |
| 50 | `/review` | 已弃用。安装 code-review 插件 |
| 51 | `/security-review` | 分析待定更改的安全漏洞 |
| 52 | `/team-onboarding` | 生成团队入职指南 |

### 远程 (Remote)

| # | 命令 | 描述 |
|---|---------|-------------|
| 53 | `/autofix-pr [prompt]` | 启动 web 会话监视当前分支的 PR，CI 失败或评论时推送修复 |
| 54 | `/desktop` | 在 Claude Code Desktop 应用中继续会话。别名：`/app` |
| 55 | `/install-github-app` | 设置 Claude GitHub Actions 应用 |
| 56 | `/install-slack-app` | 安装 Claude Slack 应用 |
| 57 | `/mobile` | 显示下载 Claude 移动应用的二维码。别名：`/ios`, `/android` |
| 58 | `/remote-control` | 使此会话可从 claude.ai 远程控制。别名：`/rc` |
| 59 | `/remote-env` | 配置 `--remote` web 会话的默认远程环境 |
| 60 | `/schedule [description]` | 创建、更新、列出或运行例行任务 |
| 61 | `/teleport` | 将 web 会话拉入此终端。别名：`/tp` |
| 62 | `/web-setup` | 用 gh CLI 凭证连接 GitHub 到 Claude Code web |

### 会话 (Session)

| # | 命令 | 描述 |
|---|---------|-------------|
| 63 | `/branch [name]` | 在当前对话点创建分支。别名：`/fork` |
| 64 | `/btw <question>` | 问快速小问题，不加入对话 |
| 65 | `/clear` | 清除对话历史。别名：`/reset`, `/new` |
| 66 | `/compact [instructions]` | 压缩对话 |
| 67 | `/exit` | 退出 CLI。别名：`/quit` |
| 68 | `/rename [name]` | 重命名当前会话 |
| 69 | `/resume [session]` | 恢复对话。别名：`/continue` |
| 70 | `/rewind` | 回退对话和代码。别名：`/checkpoint` |

捆绑的 skills 如 `/debug` 也会出现在斜杠命令菜单中，但不是内置命令。

---

## 来源

- [Claude Code Slash Commands](https://code.claude.com/docs/en/slash-commands)
- [Claude Code Interactive Mode](https://code.claude.com/docs/en/interactive-mode)
- [Claude Code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
