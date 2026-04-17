# 命令最佳实践

Claude Code 命令 —— frontmatter 字段和官方内置斜杠命令。

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
| `description` | string | 推荐 | 命令功能说明。显示在自动补全中，用于 Claude 自动发现 |
| `when_to_use` | string | 否 | Claude 应何时调用该 skill 的额外上下文 —— 触发短语或示例请求。追加到 `description`，计入 1536 字符上限 |
| `argument-hint` | string | 否 | 自动补全时显示的提示（如 `[issue-number]`, `[filename]`) |
| `disable-model-invocation` | boolean | 否 | 设为 `true` 阻止 Claude 自动调用此命令 |
| `user-invocable` | boolean | 否 | 设为 `false` 从 `/` 菜单隐藏 —— 命令仅作为背景知识 |
| `paths` | string/list | 否 | 限制该 skill 激活时的 glob 模式。接受逗号分隔字符串或 YAML 列表。设置后，Claude 仅在处理匹配模式的文件时自动加载该 skill |
| `allowed-tools` | string | 否 | 此命令激活时无需权限提示即可使用的工具 |
| `model` | string | 否 | 此命令运行时使用的模型（如 `haiku`, `sonnet`, `opus`) |
| `effort` | string | 否 | 调用时覆盖模型努力程度级别 (`low`, `medium`, `high`, `max`) |
| `context` | string | 否 | 设为 `fork` 在隔离子代理上下文中运行命令 |
| `agent` | string | 否 | `context: fork` 时的子代理类型（默认: `general-purpose`) |
| `shell` | string | 否 | `` !`command` `` 块的 shell —— 接受 `bash`（默认）或 `powershell`。需要 `CLAUDE_CODE_USE_POWERSHELL_TOOL=1` |
| `hooks` | object | 否 | 限定于此命令的生命周期钩子 |

---

## 官方内置命令 (70)

### 认证 (Auth)

| # | 命令 | 描述 |
|---|---------|-------------|
| 1 | `/login` | 登录 Anthropic 账户 |
| 2 | `/logout` | 登出 Anthropic 账户 |
| 3 | `/setup-bedrock` | 通过交互式向导配置 Amazon Bedrock 认证、区域和模型绑定。仅在 `CLAUDE_CODE_USE_BEDROCK=1` 时可见。首次 Bedrock 用户也可从登录界面访问此向导 |
| 4 | `/setup-vertex` | 通过交互式向导配置 Google Vertex AI 认证、项目、区域和模型绑定。仅在 `CLAUDE_CODE_USE_VERTEX=1` 时可见。首次 Vertex AI 用户也可从登录界面访问此向导 |
| 5 | `/upgrade` | 打开升级页面切换到更高套餐 |

### 配置 (Config)

| # | 命令 | 描述 |
|---|---------|-------------|
| 6 | `/color [color\|default]` | 设置当前会话的提示栏颜色。可用颜色：`red`, `blue`, `green`, `yellow`, `purple`, `orange`, `pink`, `cyan`。用 `default` 重置 |
| 7 | `/config` | 打开设置界面调整主题、模型、输出风格等偏好。别名：`/settings` |
| 8 | `/keybindings` | 打开或创建键盘快捷键配置文件 |
| 9 | `/permissions` | 管理工具权限的允许、询问和拒绝规则。打开交互对话框，可按范围查看规则、添加或删除规则、管理工作目录、查看最近的自动模式拒绝。别名：`/allowed-tools` |
| 10 | `/privacy-settings` | 查看和更新隐私设置。仅 Pro 和 Max 套餐用户可用 |
| 11 | `/sandbox` | 切换沙箱模式。仅在支持的平台可用 |
| 12 | `/statusline` | 配置 Claude Code 的状态栏。描述你想要的，或无参数运行以从 shell 提示自动配置 |
| 13 | `/stickers` | 购买 Claude Code 贴纸 |
| 14 | `/terminal-setup` | 配置 Shift+Enter 和其他快捷键的终端键盘绑定。仅在需要它的终端可见，如 VS Code、Alacritty、Warp |
| 15 | `/theme` | 更改颜色主题。包含亮色和暗色变体、色盲友好主题，以及使用终端颜色调色的 ANSI 主题 |
| 16 | `/voice` | 切换按住说话语音输入。需要 Claude.ai 账户 |

### 上下文 (Context)

| # | 命令 | 描述 |
|---|---------|-------------|
| 17 | `/context` | 以彩色网格可视化当前上下文使用情况。显示上下文密集工具、内存膨胀和容量警告的优化建议 |
| 18 | `/cost` | 显示 token 使用统计。查看成本跟踪指南了解套餐特定详情 |
| 19 | `/extra-usage` | 配置额外用量以便在达到速率限制时继续工作 |
| 20 | `/insights` | 生成分析 Claude Code 会话的报告，包括项目区域、交互模式和摩擦点 |
| 21 | `/stats` | 可视化每日用量、会话历史、连续使用天数和模型偏好 |
| 22 | `/status` | 打开设置界面（状态标签页）显示版本、模型、账户和连接状态。Claude 响应时也可工作，无需等待当前响应完成 |
| 23 | `/usage` | 显示套餐用量限制和速率限制状态 |

### 调试 (Debug)

| # | 命令 | 描述 |
|---|---------|-------------|
| 24 | `/doctor` | 诊断和验证 Claude Code 安装及设置。结果带状态图标显示。按 `f` 让 Claude 修复任何报告的问题 |
| 25 | `/feedback [report]` | 提交关于 Claude Code 的反馈。别名：`/bug` |
| 26 | `/help` | 显示帮助和可用命令 |
| 27 | `/powerup` | 通过快速互动课程和动画演示发现 Claude Code 功能 |
| 28 | `/release-notes` | 在交互式版本选择器中查看更新日志。选择特定版本查看其发布说明，或选择显示所有版本 |
| 29 | `/tasks` | 列出和管理后台任务。别名：`/bashes` |

### 导出 (Export)

| # | 命令 | 描述 |
|---|---------|-------------|
| 30 | `/copy [N]` | 将最后的助手响应复制到剪贴板。传入数字 `N` 复制第 N 个最近的响应：`/copy 2` 复制倒数第二个。有代码块时，显示交互选择器选择单个块或完整响应。在选择器中按 `w` 将选择写入文件而非剪贴板，SSH 时有用 |
| 31 | `/export [filename]` | 将当前对话导出为纯文本。带文件名时直接写入该文件。无文件名时打开对话框复制到剪贴板或保存到文件 |

### 扩展 (Extensions)

| # | 命令 | 描述 |
|---|---------|-------------|
| 32 | `/agents` | 管理代理配置 |
| 33 | `/chrome` | 配置 Chrome 中的 Claude 设置 |
| 34 | `/hooks` | 查看工具事件的钩子配置 |
| 35 | `/ide` | 管理 IDE 集成并显示状态 |
| 36 | `/mcp` | 管理 MCP 服务器连接和 OAuth 认证 |
| 37 | `/plugin` | 管理 Claude Code 插件 |
| 38 | `/reload-plugins` | 重载所有活跃插件以应用待定更改，无需重启。报告每个重载组件的数量并标记任何加载错误 |
| 39 | `/skills` | 列出可用的 skills |

### 记忆 (Memory)

| # | 命令 | 描述 |
|---|---------|-------------|
| 40 | `/memory` | 编辑 `CLAUDE.md` 记忆文件、启用或禁用自动记忆、查看自动记忆条目 |

### 模型 (Model)

| # | 命令 | 描述 |
|---|---------|-------------|
| 41 | `/effort [low\|medium\|high\|max\|auto]` | 设置模型努力程度级别。`low`, `medium`, `high` 会跨会话持久化。`max` 仅适用于当前会话且需要 Opus 4.6。`auto` 重置为模型默认。无参数时显示当前级别。立即生效无需等待当前响应完成 |
| 42 | `/fast [on\|off]` | 切换快速模式开关 |
| 43 | `/model [model]` | 选择或更改 AI 模型。支持努力程度调整的模型可用左右箭头调整级别。更改立即生效无需等待当前响应完成 |
| 44 | `/passes` | 与朋友分享一周免费 Claude Code。仅账户符合条件时可见 |
| 45 | `/plan [description]` | 从提示直接进入计划模式。传可选描述以进入计划模式并立即开始该任务，如 `/plan fix the auth bug` |
| 46 | `/ultraplan <prompt>` | 在 ultraplan 会话中起草计划，在浏览器中审查，然后远程执行或发回终端 |

### 项目 (Project)

| # | 命令 | 描述 |
|---|---------|-------------|
| 47 | `/add-dir <path>` | 为当前会话的文件访问添加工作目录。大部分 `.claude/` 配置不会从添加的目录中发现 |
| 48 | `/diff` | 打开交互式差异查看器显示未提交更改和每次响应的差异。用左右箭头切换当前 git diff 和单个 Claude 响应，上下箭头浏览文件 |
| 49 | `/init` | 用 `CLAUDE.md` 指南初始化项目。设 `CLAUDE_CODE_NEW_INIT=1` 获得交互式流程，也会引导完成 skills、hooks 和个人记忆文件 |
| 50 | `/review` | 已弃用。安装 `code-review` 插件：`claude plugin install code-review@claude-plugins-official` |
| 51 | `/security-review` | 分析当前分支待定更改的安全漏洞。审查 git diff 并识别注入、认证问题和数据暴露等风险 |
| 52 | `/team-onboarding` | 从 Claude Code 使用历史生成团队入职指南。分析过去 30 天的会话、命令和 MCP 服务器使用 |

### 远程 (Remote)

| # | 命令 | 描述 |
|---|---------|-------------|
| 53 | `/autofix-pr [prompt]` | 在 web 会话中启动 Claude Code 监视当前分支的 PR，CI 失败或审阅者评论时推送修复。用 `gh pr view` 从检出分支检测打开的 PR；监视其他 PR 请先检出其分支。需要 `gh` CLI 和 Claude Code on the web 访问权限 |
| 54 | `/desktop` | 在 Claude Code Desktop 应用中继续当前会话。仅 macOS 和 Windows。别名：`/app` |
| 55 | `/install-github-app` | 为仓库设置 Claude GitHub Actions 应用。引导选择仓库并配置集成 |
| 56 | `/install-slack-app` | 安装 Claude Slack 应用。打开浏览器完成 OAuth 流程 |
| 57 | `/mobile` | 显示下载 Claude 移动应用的二维码。别名：`/ios`, `/android` |
| 58 | `/remote-control` | 使此会话可从 claude.ai 远程控制。别名：`/rc` |
| 59 | `/remote-env` | 配置 `--remote` 启动的 web 会话的默认远程环境 |
| 60 | `/schedule [description]` | 创建、更新、列出或运行例行任务。Claude 会对话式引导完成设置 |
| 61 | `/teleport` | 将 Claude Code on the web 会话拉入此终端：打开选择器，获取分支和对话。也可用 `/tp`。需要 claude.ai 订阅 |
| 62 | `/web-setup` | 用本地 `gh` CLI 凭证连接 GitHub 账户到 Claude Code on the web。GitHub 未连接时 `/schedule` 会自动提示 |

### 会话 (Session)

| # | 命令 | 描述 |
|---|---------|-------------|
| 63 | `/branch [name]` | 在当前对话点创建分支。别名：`/fork` |
| 64 | `/btw <question>` | 问一个快速小问题，不加入对话 |
| 65 | `/clear` | 清除对话历史并释放上下文。别名：`/reset`, `/new` |
| 66 | `/compact [instructions]` | 带可选聚焦指令压缩对话 |
| 67 | `/exit` | 退出 CLI。别名：`/quit` |
| 68 | `/rename [name]` | 重命名当前会话并在提示栏显示名称。无名称时从对话历史自动生成 |
| 69 | `/resume [session]` | 按 ID 或名称恢复对话，或打开会话选择器。别名：`/continue` |
| 70 | `/rewind` | 将对话和/或代码回退到先前点，或从选定消息摘要。参见 checkpointing。别名：`/checkpoint` |

捆绑的 skills 如 `/debug` 也会出现在斜杠命令菜单中，但它们不是内置命令。

---

## 来源

- [Claude Code Slash Commands](https://code.claude.com/docs/en/slash-commands)
- [Claude Code Interactive Mode](https://code.claude.com/docs/en/interactive-mode)
- [Claude Code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)