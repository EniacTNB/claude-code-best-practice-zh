# 设置最佳实践

Claude Code `settings.json` 文件的完整配置指南。截至 v2.1.107，Claude Code 提供 **60+ 设置** 和 **170+ 环境变量**（使用 `settings.json` 中的 `"env"` 字段可避免包装脚本）。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

## 目录

1. [设置层级](#设置层级)
2. [核心配置](#核心配置)
3. [权限](#权限)
4. [Hooks](#hooks)
5. [MCP 服务器](#mcp-服务器)
6. [沙箱](#沙箱)
7. [插件](#插件)
8. [模型配置](#模型配置)
9. [显示与 UX](#显示与-ux)
10. [AWS 与云凭证](#aws-与云凭证)
11. [环境变量](#环境变量-via-env)
12. [常用命令](#常用命令)

---

## 设置层级

设置按优先级顺序应用（从高到低）：

| 优先级 | 位置 | 范围 | 可共享？ | 用途 |
|----------|----------|-------|---------|---------|
| 1 | 管理设置 | 组织 | 是（IT 部署） | 无法覆盖的安全策略 |
| 2 | 命令行参数 | 会话 | N/A | 临时单会话覆盖 |
| 3 | `.claude/settings.local.json` | 项目 | 否（git-ignore） | 个人项目特定 |
| 4 | `.claude/settings.json` | 项目 | 是（提交） | 团队共享设置 |
| 5 | `~/.claude/settings.json` | 用户 | N/A | 全局个人默认 |

**管理设置** 是组织强制执行的，无法被任何其他级别覆盖，包括命令行参数。交付方式：
- **服务器管理** 设置（远程交付）
- **MDM profiles** — macOS plist 位于 `com.anthropic.claudecode`
- **注册表策略** — Windows `HKLM\SOFTWARE\Policies\ClaudeCode`（管理员）和 `HKCU\SOFTWARE\Policies\ClaudeCode`（用户级，最低策略优先级）
- **文件** — `managed-settings.json` 和 `managed-mcp.json`（macOS: `/Library/Application Support/ClaudeCode/`, Linux/WSL: `/etc/claude-code/`, Windows: `C:\Program Files\ClaudeCode\`)
- **Drop-in 目录** — `managed-settings.d/` 与 `managed-settings.json` 并存，用于独立策略片段（v2.1.83）。遵循 systemd 约定，`managed-settings.json` 先合并作为基础，然后 drop-in 目录中所有 `*.json` 文件按字母顺序排序并合并。后面的文件覆盖前面的标量值；数组拼接去重；对象深度合并。以 `.` 开头的隐藏文件被忽略。用数字前缀控制合并顺序（如 `10-telemetry.json`, `20-security.json`）

在管理层级内，优先级为：服务器管理 > MDM/OS 级策略 > 文件级（`managed-settings.d/*.json` + `managed-settings.json`) > HKCU 注册表（仅 Windows）。只使用一个管理源；各层级间不合并。在文件级层级内，drop-in 文件和基础文件一起合并。

> **注意：** 自 v2.1.75 起，已弃用的 Windows 回退路径 `C:\ProgramData\ClaudeCode\managed-settings.json` 已移除。请使用 `C:\Program Files\ClaudeCode\managed-settings.json`。

**重要**：
- `deny` 规则具有最高安全优先级，无法被较低优先级的 allow/ask 规则覆盖。
- 管理设置可能锁定或覆盖本地行为，即使本地文件指定了不同值。
- 数组设置（如 `permissions.allow`）在各范围间**拼接去重** —— 所有级别的条目合并，不替换。

---

## 核心配置

### 基本设置

| 键 | 类型 | 默认值 | 描述 |
|-----|------|---------|-------------|
| `$schema` | string | - | JSON Schema URL，用于 IDE 验证和自动补全（如 `"https://json.schemastore.org/claude-code-settings.json"`） |
| `model` | string | `"default"` | 覆盖默认模型。接受别名（`sonnet`, `opus`, `haiku`) 或完整模型 ID |
| `agent` | string | - | 设置主对话的默认代理。值为 `.claude/agents/` 中的代理名。也可通过 `--agent` CLI 标志使用 |
| `language` | string | `"english"` | Claude 的首选响应语言。也设置语音输入语言 |
| `cleanupPeriodDays` | number | `30` | 超过此周期未活跃的会话在启动时删除（最小 1）。也控制启动时自动清理孤立子代理工作树的年龄阈值。设为 `0` 会因验证错误被拒绝。在非交互模式（`-p`) 下禁用对话写入，使用 `--no-session-persistence` 或 SDK 的 `persistSession: false` 选项 |
| `autoUpdatesChannel` | string | `"latest"` | 发布通道：`"stable"` 或 `"latest"` |
| `alwaysThinkingEnabled` | boolean | `false` | 默认为所有会话启用扩展思考 |
| `skipWebFetchPreflight` | boolean | `false` | 获取 URL 前跳过 WebFetch 黑名单检查 *(JSON schema 中有，官方设置页面未记载)* |
| `availableModels` | array | - | 限制用户可通过 `/model`、`--model`、Config 工具或 `ANTHROPIC_MODEL` 选择哪些模型。不影响默认选项。示例：`["sonnet", "haiku"]` |
| `fastModePerSessionOptIn` | boolean | `false` | 要求用户每个会话单独选择启用快速模式 |
| `defaultShell` | string | `"bash"` | 输入框 `!` 命令的默认 shell。接受 `"bash"`（默认）或 `"powershell"`。设 `"powershell"` 在 Windows 上将交互式 `!` 命令通过 PowerShell 执行。需要 `CLAUDE_CODE_USE_POWERSHELL_TOOL=1`（v2.1.84） |
| `includeGitInstructions` | boolean | `true` | 在 Claude 系统提示中包含内置的提交和 PR 工作流指令以及 git 状态快照。设置 `CLAUDE_CODE_DISABLE_GIT_INSTRUCTIONS` 环境变量时优先于此设置 |
| `voiceEnabled` | boolean | - | 启用按住说话语音输入。运行 `/voice` 时自动写入。需要 Claude.ai 账户 |
| `showClearContextOnPlanAccept` | boolean | `false` | 在计划接受界面显示"清除上下文"选项。设为 `true` 恢复该选项（自 v2.1.81 默认隐藏） |
| `viewMode` | string | - | 启动时的默认对话查看模式：`"default"`、`"verbose"` 或 `"focus"`。设置后覆盖 Ctrl+O 的持久选择 |
| `disableDeepLinkRegistration` | string | - | 设为 `"disable"` 阻止 Claude Code 在启动时向操作系统注册 `claude-cli://` 协议处理器。深层链接让外部工具通过 `claude-cli://open?q=...` 打开预填充提示的 Claude Code 会话。`q` 参数支持用 URL 编码换行符（`%0A`) 的多行提示。在协议处理器注册受限或单独管理的环境中有用 |
| `showThinkingSummaries` | boolean | `false` | 在交互会话中显示扩展思考摘要。未设置或 `false`（交互模式默认）时，思考块被 API 红act并显示为折叠 stub。红act只改变你看到的，不改变模型生成的 —— 要减少思考花费，降低预算或禁用思考。非交互模式（`-p`) 和 SDK 调用者始终收到摘要，无论此设置 |
| `disableSkillShellExecution` | boolean | `false` | 禁止来自用户、项目、插件或额外目录源的 skills 和自定义命令中的 `` !`...` `` 和 `` ```! `` 块的内联 shell 执行。命令被替换为 `[shell command execution disabled by policy]` 而非执行。捆绑和管理 skills 不受影响（v2.1.91） |
| `forceRemoteSettingsRefresh` | boolean | `false` | **（仅管理设置）** 阻止 CLI 启动直到远程管理设置刷新获取。获取失败则 CLI 退出（fail-closed）。用于企业环境，策略执行必须在任何会话开始前更新（v2.1.92） |
| `feedbackSurveyRate` | number | - | 会话质量调查出现时的概率（0-1）。企业管理员可控制调查显示频率。示例：`0.05` = 5% 的符合条件会话 |

**示例：**
```json
{
  "model": "opus",
  "agent": "code-reviewer",
  "language": "japanese",
  "cleanupPeriodDays": 60,
  "autoUpdatesChannel": "stable",
  "alwaysThinkingEnabled": true
}
```

### 计划与记忆目录

将计划和自动记忆文件存储在自定义位置。

| 键 | 类型 | 默认值 | 描述 |
|-----|------|---------|-------------|
| `plansDirectory` | string | `~/.claude/plans` | `/plan` 输出存储目录 |
| `autoMemoryDirectory` | string | - | 自动记忆存储的自定义目录。接受 `~/` 扩展路径。项目设置（`.claude/settings.json`) 中不接受，防止记忆写入重定向到敏感位置；策略、本地和用户设置中接受 |

**示例：**
```json
{
  "plansDirectory": "./my-plans"
}
```

**用途：** 将计划工件与 Claude 内部文件分开组织，或保存在团队共享位置。

### 工作树设置

配置 `--worktree` 如何创建和管理 git worktrees。在大 monorepo 中减少磁盘使用和启动时间。

| 键 | 类型 | 默认值 | 描述 |
|-----|------|---------|-------------|
| `worktree.symlinkDirectories` | array | `[]` | 从主仓库符号链接到每个工作树的目录，避免在磁盘上复制大目录 |
| `worktree.sparsePaths` | array | `[]` | 每个工作树通过 git sparse-checkout（cone 模式）检出的目录。只有列出的路径写入磁盘 |

**示例：**
```json
{
  "worktree": {
    "symlinkDirectories": ["node_modules", ".cache"],
    "sparsePaths": ["packages/my-app", "shared/utils"]
  }
}
```

### 签名设置

自定义 git 提交和 pull request 的签名消息。

| 键 | 类型 | 默认值 | 描述 |
|-----|------|---------|-------------|
| `attribution.commit` | string | Co-authored-by | Git 提交签名（支持 trailers） |
| `attribution.pr` | string | Generated message | Pull request 描述签名 |
| `includeCoAuthoredBy` | boolean | `true` | **已弃用** - 使用 `attribution` 代替 |

**示例：**
```json
{
  "attribution": {
    "commit": "Generated with AI\n\nCo-Authored-By: Claude <noreply@anthropic.com>",
    "pr": "Generated with Claude Code"
  }
}
```

**注意：** 设为空字符串（`""`) 完全隐藏签名。

### 认证助手

动态认证令牌生成的脚本。

| 键 | 类型 | 描述 |
|-----|------|-------------|
| `apiKeyHelper` | string | 输出认证令牌的 shell 脚本路径（作为 `X-Api-Key` 头发送） |
| `forceLoginMethod` | string | 限制登录为 `"claudeai"` 或 `"console"` 账户 |
| `forceLoginOrgUUID` | string \| array | 要求登录属于特定组织。接受单个 UUID 字符串（也会在登录时预选该组织）或 UUID 数组，数组中任何组织都被接受而不预选。在管理设置中设置时，认证账户不属于列表中组织则登录失败；空数组 fail-closed 并显示配置错误消息阻止登录 |

**示例：**
```json
{
  "apiKeyHelper": "/bin/generate_temp_api_key.sh",
  "forceLoginMethod": "console",
  "forceLoginOrgUUID": ["xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", "yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy"]
}
```

### 公司公告

在启动时向用户显示自定义公告（随机轮换）。

| 键 | 类型 | 描述 |
|-----|------|-------------|
| `companyAnnouncements` | array | 启动时显示的字符串数组 |

**示例：**
```json
{
  "companyAnnouncements": [
    "欢迎使用 Acme Corp!",
    "提交前记得运行测试!",
    "查看 wiki 了解编码标准"
  ]
}
```

---
## 权限

控制 Claude 可执行的工具和操作。

### 权限结构

```json
{
  "permissions": {
    "allow": [],
    "ask": [],
    "deny": [],
    "additionalDirectories": [],
    "defaultMode": "acceptEdits",
    "disableBypassPermissionsMode": "disable"
  }
}
```

### 权限键

| 键 | 类型 | 描述 |
|-----|------|-------------|
| `permissions.allow` | array | 无需提示允许工具使用的规则 |
| `permissions.ask` | array | 需要用户确认的规则 |
| `permissions.deny` | array | 阻止工具使用的规则（最高优先级） |
| `permissions.additionalDirectories` | array | Claude 可访问的额外目录 |
| `permissions.defaultMode` | string | 默认权限模式。Remote 环境仅支持 `acceptEdits` 和 `plan`（v2.1.70+） |
| `permissions.disableBypassPermissionsMode` | string | 阻止绕过模式激活 |
| `permissions.skipDangerousModePermissionPrompt` | boolean | 跳过进入绕过权限模式前的确认提示 |
| `allowManagedPermissionRulesOnly` | boolean | **（仅管理）** 仅管理权限规则生效 |
| `autoMode` | object | 自定义自动模式分类器阻塞和允许的内容 |
| `disableAutoMode` | string | 设为 `"disable"` 阻止自动模式激活 |
| `useAutoModeDuringPlan` | boolean | 计划模式是否使用自动模式语义 |

### 权限模式

| 模式 | 行为 |
|------|----------|
| `"default"` | 带提示的标准权限检查 |
| `"acceptEdits"` | 自动接受文件编辑，不询问 |
| `"dontAsk"` | 除非预批准自动拒绝工具 |
| `"bypassPermissions"` | 跳过所有权限检查（危险） |
| `"auto"` | 后台分类器替代手动提示 |
| `"plan"` | 只读探索模式 |

### 工具权限语法

| 工具 | 语法 | 示例 |
|------|--------|----------|
| `Bash` | `Bash(command pattern)` | `Bash(npm run *)`, `Bash(git *)` |
| `Read` | `Read(path pattern)` | `Read(.env)`, `Read(./secrets/**)` |
| `Edit` | `Edit(path pattern)` | `Edit(src/**)` |
| `Write` | `Write(path pattern)` | `Write(*.md)` |
| `WebFetch` | `WebFetch(domain:pattern)` | `WebFetch(domain:example.com)` |
| `MCP` | `mcp__server__tool` | `mcp__memory__*`, `MCP(github:*)` |

**示例：**
```json
{
  "permissions": {
    "allow": ["Edit(*)", "Write(*)", "Bash(npm run *)", "Bash(git *)"],
    "ask": ["Bash(rm *)", "Bash(git push *)"],
    "deny": ["Read(.env)", "Read(./secrets/**)"],
    "additionalDirectories": ["../shared-libs/"]
  }
}
```

---

## Hooks

Hook 配置维护在专门仓库：

> **[claude-code-hooks](https://github.com/shanraisshan/claude-code-hooks)** — 完整 Hook 参考。

官方 hooks 参考见 [Claude Code Hooks Documentation](https://code.claude.com/docs/en/hooks)。

---

## MCP 服务器

| 键 | 类型 | 范围 | 描述 |
|-----|------|-------|-------------|
| `enableAllProjectMcpServers` | boolean | 任意 | 自动批准所有 `.mcp.json` 服务器 |
| `enabledMcpjsonServers` | array | 任意 | 特定服务器名称白名单 |
| `disabledMcpjsonServers` | array | 任意 | 特定服务器名称黑名单 |

---

## 沙箱

| 键 | 类型 | 默认值 | 描述 |
|-----|------|---------|-------------|
| `sandbox.enabled` | boolean | `false` | 启用 bash 沙箱 |
| `sandbox.autoAllowBashIfSandboxed` | boolean | `true` | 沙箱时自动批准 bash |
| `sandbox.excludedCommands` | array | `[]` | 在沙箱外运行的命令 |

**示例：**
```json
{
  "sandbox": {
    "enabled": true,
    "excludedCommands": ["git", "docker"],
    "network": {
      "allowUnixSockets": ["/var/run/docker.sock"]
    }
  }
}
```

---

## 插件

| 键 | 类型 | 范围 | 描述 |
|-----|------|-------|-------------|
| `enabledPlugins` | object | 任意 | 启用/禁用特定插件 |
| `extraKnownMarketplaces` | object | 项目 | 添加自定义插件市场 |

---


## 模型配置

### 模型别名

| 别名 | 描述 |
|-------|-------------|
| `"default"` | 根据账户类型推荐 |
| `"sonnet"` | 最新 Sonnet 模型（Claude Sonnet 4.6） |
| `"opus"` | 最新 Opus 模型（Claude Opus 4.6） |
| `"haiku"` | 快速 Haiku 模型 |
| `"sonnet[1m]"` | Sonnet 带 1M token 上下文 |
| `"opus[1m]"` | Opus 带 1M token 上下文 |

### 努力程度级别

`/model` 命令暴露**努力程度级别**控制，调整模型每次响应应用的推理量。在 `/model` UI 中用 ← → 箭头键切换级别。

| 级别 | 描述 |
|-------------|-------------|
| High（默认） | 完整推理深度，适合复杂任务 |
| Medium | 平衡推理，适合日常任务 |
| Low | 最少推理，最快响应 |

**使用方法：**
1. 运行 `/effort low`、`/effort medium` 或 `/effort high` 直接设置
2. 或运行 `/model` → 选择模型 → 用 ← → 箭头调整

---

## 显示与 UX

### 显示设置

| 键 | 类型 | 默认值 | 描述 |
|-----|------|---------|-------------|
| `statusLine` | object | - | 自定义状态栏配置 |
| `outputStyle` | string | `"default"` | 输出风格 |
| `spinnerTipsEnabled` | boolean | `true` | 等待时显示提示 |
| `respectGitignore` | boolean | `true` | 文件选择器尊重 .gitignore |
| `prefersReducedMotion` | boolean | `false` | 减少动画和动效 |

### 状态栏配置

```json
{
  "statusLine": {
    "type": "command",
    "command": "git branch --show-current",
    "padding": 2,
    "refreshInterval": 5
  }
}
```

---

## AWS 与云凭证

### AWS 设置

| 键 | 类型 | 描述 |
|-----|------|-------------|
| `awsAuthRefresh` | string | 刷新 AWS 认证的脚本 |
| `awsCredentialExport` | string | 输出 AWS 凭证 JSON 的脚本 |

---


## 环境变量（via env）

通过 `env` 键为所有 Claude Code 会话设置环境变量。

```json
{
  "env": {
    "ANTHROPIC_API_KEY": "...",
    "NODE_ENV": "development",
    "CLAUDE_CODE_EFFORT_LEVEL": "medium"
  }
}
```

### 常用环境变量

| 变量 | 描述 |
|----------|-------------|
| `ANTHROPIC_API_KEY` | 认证 API key |
| `ANTHROPIC_BASE_URL` | 自定义 API 端点 |
| `ANTHROPIC_MODEL` | 模型名称，接受别名或完整 ID |
| `CLAUDE_CODE_USE_BEDROCK` | 使用 AWS Bedrock（设为 `1`） |
| `CLAUDE_CODE_USE_VERTEX` | 使用 Google Vertex AI（设为 `1`） |
| `CLAUDE_CODE_USE_FOUNDRY` | 使用 Microsoft Foundry（设为 `1`） |
| `CLAUDE_CODE_DISABLE_THINKING` | 禁用扩展思考（设为 `1`） |
| `CLAUDE_CODE_MAX_OUTPUT_TOKENS` | 每次响应最大输出 token |
| `MAX_THINKING_TOKENS` | 每次响应最大扩展思考 token |
| `DISABLE_AUTO_COMPACT` | 禁用自动上下文压缩 |
| `DISABLE_COMPACT` | 禁用所有压缩 |
| `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` | 自动压缩阈值百分比（1-100） |
| `CLAUDE_CODE_EFFORT_LEVEL` | 设置努力程度：`low`, `medium`, `high`, `max`, `auto` |
| `CLAUDE_CODE_DISABLE_MCP` | 禁用所有 MCP 服务器（设为 `1`） |
| `MCP_TIMEOUT` | MCP 启动超时（毫秒） |
| `MAX_MCP_OUTPUT_TOKENS` | 最大 MCP 输出 token（默认 25000） |
| `API_TIMEOUT_MS` | API 请求超时（默认 600000） |
| `BASH_MAX_TIMEOUT_MS` | Bash 命令超时 |
| `HTTP_PROXY` / `HTTPS_PROXY` | 代理 URL |

---

## 常用命令

| 命令 | 描述 |
|---------|-------------|
| `/model` | 切换模型并调整 Opus 4.6 努力程度 |
| `/effort` | 直接设置努力程度：`low`, `medium`, `high` |
| `/config` | 交互式配置 UI |
| `/memory` | 查看/编辑所有记忆文件 |
| `/agents` | 管理子代理 |
| `/mcp` | 管理 MCP 服务器 |
| `/hooks` | 查看配置的钩子 |
| `/permissions` | 查看和管理权限规则 |
| `--doctor` | 诊断配置问题 |

---

## 快速参考：完整示例

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "model": "sonnet",
  "language": "english",
  "cleanupPeriodDays": 30,
  "alwaysThinkingEnabled": true,
  "effortLevel": "medium",

  "permissions": {
    "allow": ["Edit(*)", "Write(*)", "Bash(npm run *)", "Bash(git *)"],
    "deny": ["Read(.env)"],
    "defaultMode": "acceptEdits"
  },

  "enableAllProjectMcpServers": true,

  "sandbox": {
    "enabled": true,
    "excludedCommands": ["git", "docker"]
  },

  "env": {
    "CLAUDE_CODE_EFFORT_LEVEL": "medium"
  }
}
```

---

## 来源

- [Claude Code Settings 文档](https://code.claude.com/docs/en/settings)
- [Claude Code Settings JSON Schema](https://json.schemastore.org/claude-code-settings.json)
- [Claude Code Changelog](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
- [Claude Code Environment Variables 参考](https://code.claude.com/docs/en/env-vars)
