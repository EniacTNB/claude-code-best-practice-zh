# Settings 最佳实践

![Last Updated](https://img.shields.io/badge/Last_Updated-Apr%2014%2C%202026%2011%3A25%20PM%20PKT-white?style=flat&labelColor=555) ![Version](https://img.shields.io/badge/Claude_Code-v2.1.107-blue?style=flat&labelColor=555)<br>
[![Implemented](https://img.shields.io/badge/Implemented-2ea44f?style=flat)](../.claude/settings.json)

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
| `$schema` | string | - | JSON Schema URL，用于 IDE 验证和自动补全 |
| `model` | string | `"default"` | 覆盖默认模型。接受别名或完整模型 ID |
| `agent` | string | - | 设置主对话的默认代理。值为 `.claude/agents/` 中的代理名 |
| `language` | string | `"english"` | Claude 的首选响应语言。也设置语音输入语言 |
| `cleanupPeriodDays` | number | `30` | 超过此周期未活跃的会话在启动时删除（最小 1） |
| `autoUpdatesChannel` | string | `"latest"` | 发布通道：`"stable"` 或 `"latest"` |
| `alwaysThinkingEnabled` | boolean | `false` | 默认为所有会话启用扩展思考 |
| `skipWebFetchPreflight` | boolean | `false` | 获取 URL 前跳过 WebFetch 黑名单检查 |
| `availableModels` | array | - | 限制用户可选择哪些模型 |
| `fastModePerSessionOptIn` | boolean | `false` | 要求用户每个会话单独启用快速模式 |
| `defaultShell` | string | `"bash"` | 输入框 `!` 命令的默认 shell |
| `includeGitInstructions` | boolean | `true` | 在系统提示中包含 git 指令 |
| `voiceEnabled` | boolean | - | 启用按住说话语音输入 |
| `showClearContextOnPlanAccept` | boolean | `false` | 在计划接受界面显示"清除上下文"选项 |
| `viewMode` | string | - | 启动时的默认查看模式 |
| `disableDeepLinkRegistration` | string | - | 阻止注册 `claude-cli://` 协议处理器 |
| `showThinkingSummaries` | boolean | `false` | 显示扩展思考摘要 |
| `disableSkillShellExecution` | boolean | `false` | 禁用 skills 中的 shell 执行 |
| `forceRemoteSettingsRefresh` | boolean | `false` | **（仅管理）** 强制刷新远程设置 |
| `feedbackSurveyRate` | number | - | 调查出现概率（0-1） |

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

| 键 | 类型 | 默认值 | 描述 |
|-----|------|---------|-------------|
| `plansDirectory` | string | `~/.claude/plans` | `/plan` 输出存储目录 |
| `autoMemoryDirectory` | string | - | 自动记忆存储的自定义目录 |

### Worktree 设置

| 键 | 类型 | 默认值 | 描述 |
|-----|------|---------|-------------|
| `worktree.symlinkDirectories` | array | `[]` | 从主仓库符号链接的目录 |
| `worktree.sparsePaths` | array | `[]` | 每个工作树检出的路径 |

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

| 键 | 类型 | 默认值 | 描述 |
|-----|------|---------|-------------|
| `attribution.commit` | string | Co-authored-by | Git 提交签名 |
| `attribution.pr` | string | Generated message | PR 描述签名 |

**注意：** 设为空字符串（`""`) 完全隐藏签名。

### 认证助手

| 键 | 类型 | 描述 |
|-----|------|-------------|
| `apiKeyHelper` | string | 输出认证令牌的脚本路径 |
| `forceLoginMethod` | string | 限制登录方式 |
| `forceLoginOrgUUID` | string \| array | 要求登录属于特定组织 |

### 公司公告

| 键 | 类型 | 描述 |
|-----|------|-------------|
| `companyAnnouncements` | array | 启动时显示的字符串数组 |

---

## 权限

### 权限结构

```json
{
  "permissions": {
    "allow": [],
    "ask": [],
    "deny": [],
    "additionalDirectories": [],
    "defaultMode": "acceptEdits"
  }
}
```

### 权限键

| 键 | 类型 | 描述 |
|-----|------|-------------|
| `permissions.allow` | array | 无需提示允许的工具规则 |
| `permissions.ask` | array | 需要确认的规则 |
| `permissions.deny` | array | 阻止的规则（最高优先级） |
| `permissions.additionalDirectories` | array | Claude 可访问的额外目录 |
| `permissions.defaultMode` | string | 默认权限模式 |
| `autoMode` | object | 自定义自动模式行为 |

### 权限模式

| 模式 | 行为 |
|------|----------|
| `"default"` | 带提示的标准检查 |
| `"acceptEdits"` | 自动接受文件编辑 |
| `"dontAsk"` | 未预批准自动拒绝 |
| `"bypassPermissions"` | 跳过所有检查（危险） |
| `"auto"` | 后台分类器替代手动提示 |
| `"plan"` | 只读探索模式 |

### 工具权限语法

| 工具 | 语法 | 示例 |
|------|--------|----------|
| `Bash` | `Bash(pattern)` | `Bash(npm run *)` |
| `Read` | `Read(path)` | `Read(.env)` |
| `Edit` | `Edit(path)` | `Edit(src/**)` |
| `Write` | `Write(path)` | `Write(*.md)` |
| `MCP` | `mcp__server__tool` | `mcp__memory__*` |

**示例：**
```json
{
  "permissions": {
    "allow": ["Edit(*)", "Write(*)", "Bash(npm run *)"],
    "deny": ["Read(.env)"],
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
| `enabledMcpjsonServers` | array | 任意 | 特定服务器白名单 |
| `disabledMcpjsonServers` | array | 任意 | 特定服务器黑名单 |

---

## 沙箱

| 键 | 类型 | 默认值 | 描述 |
|-----|------|---------|-------------|
| `sandbox.enabled` | boolean | `false` | 启用沙箱 |
| `sandbox.autoAllowBashIfSandboxed` | boolean | `true` | 沙箱时自动批准 bash |
| `sandbox.excludedCommands` | array | `[]` | 在沙箱外运行的命令 |
| `sandbox.network.allowedDomains` | array | `[]` | 网络域名白名单 |
| `sandbox.filesystem.allowWrite` | array | `[]` | 可写入的额外路径 |

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
| `extraKnownMarketplaces` | object | 项目 | 添加自定义市场 |

---

## 模型配置

### 模型别名

| 别名 | 描述 |
|-------|-------------|
| `"default"` | 根据账户类型推荐 |
| `"sonnet"` | Claude Sonnet 4.6 |
| `"opus"` | Claude Opus 4.6 |
| `"haiku"` | 快速 Haiku 模型 |
| `"sonnet[1m]"` | Sonnet 带 1M 上下文 |
| `"opus[1m]"` | Opus 带 1M 上下文 |

### 努力程度

| 级别 | 描述 |
|-------------|-------------|
| High | 完整推理，复杂任务 |
| Medium | 平衡推理，日常任务 |
| Low | 最少推理，最快响应 |

---

## 显示与 UX

| 键 | 类型 | 默认值 | 描述 |
|-----|------|---------|-------------|
| `statusLine` | object | - | 自定义状态栏配置 |
| `outputStyle` | string | `"default"` | 输出风格 |
| `spinnerTipsEnabled` | boolean | `true` | 等待时显示提示 |
| `respectGitignore` | boolean | `true` | 文件选择器尊重 .gitignore |

### 状态栏配置

```json
{
  "statusLine": {
    "type": "command",
    "command": "git branch --show-current",
    "refreshInterval": 5
  }
}
```

---

## AWS 与云凭证

| 键 | 类型 | 描述 |
|-----|------|-------------|
| `awsAuthRefresh` | string | 刷新 AWS 认证的脚本 |
| `awsCredentialExport` | string | 输出 AWS 凭证 JSON 的脚本 |

---

## 环境变量（via env）

通过 `env` 键设置环境变量：

```json
{
  "env": {
    "ANTHROPIC_API_KEY": "...",
    "CLAUDE_CODE_EFFORT_LEVEL": "medium",
    "MAX_THINKING_TOKENS": "10000"
  }
}
```

### 常用环境变量

| 变量 | 描述 |
|----------|-------------|
| `ANTHROPIC_API_KEY` | API key |
| `ANTHROPIC_BASE_URL` | 自定义 API 端点 |
| `ANTHROPIC_MODEL` | 模型名称 |
| `CLAUDE_CODE_USE_BEDROCK` | 使用 AWS Bedrock |
| `CLAUDE_CODE_USE_VERTEX` | 使用 Vertex AI |
| `CLAUDE_CODE_DISABLE_THINKING` | 禁用扩展思考 |
| `CLAUDE_CODE_MAX_OUTPUT_TOKENS` | 最大输出 token |
| `MAX_THINKING_TOKENS` | 最大思考 token |
| `DISABLE_AUTO_COMPACT` | 禁用自动压缩 |
| `MCP_TIMEOUT` | MCP 启动超时 |

---

## 常用命令

| 命令 | 描述 |
|---------|-------------|
| `/model` | 切换模型 |
| `/effort` | 设置努力程度 |
| `/config` | 交互式配置 |
| `/permissions` | 管理权限 |
| `/doctor` | 诊断配置 |

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
    "allow": ["Edit(*)", "Write(*)", "Bash(npm run *)"],
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
- [Claude Code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
- [Claude Code Environment Variables 参考](https://code.claude.com/docs/en/env-vars)
