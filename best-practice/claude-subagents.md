# Subagents（子代理）最佳实践

Claude Code 子代理 —— frontmatter 字段和官方内置代理类型。

---

## Frontmatter 字段（16 个）

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `name` | string | 是 | 唯一标识符，小写字母和连字符 |
| `description` | string | 是 | 何时调用。用 `"PROACTIVELY"` 实现自动调用 |
| `tools` | string/list | 否 | 工具白名单（如 `Read, Write, Edit, Bash`）。省略则继承所有工具 |
| `disallowedTools` | string/list | 否 | 禁用的工具，从继承或指定列表中移除 |
| `model` | string | 否 | 模型：sonnet/opus/haiku 或 inherit（默认 inherit） |
| `permissionMode` | string | 否 | 权限模式：default/acceptEdits/auto/dontAsk/bypassPermissions/plan |
| `maxTurns` | integer | 否 | 最大代理轮数 |
| `skills` | list | 否 | 启动时预加载的技能名 |
| `mcpServers` | list | 否 | 该子代理的 MCP 服务器 |
| `hooks` | object | 否 | 生命周期钩子（PreToolUse/PostToolUse/Stop 最常用） |
| `memory` | string | 否 | 持久记忆范围：user/project/local |
| `background` | boolean | 否 | 设为 true 总是后台运行（默认 false） |
| `effort` | string | 否 | 努力级别覆盖：low/medium/high/max（仅 Opus 4.6） |
| `isolation` | string | 否 | 设为 "worktree" 在临时 git worktree 运行 |
| `initialPrompt` | string | 否 | 作为主会话代理运行时自动提交的首条用户提示 |
| `color` | string | 否 | 显示颜色：red/blue/green/yellow/purple/orange/pink/cyan |

---

## 官方内置代理（5 种）

| # | 代理 | 模型 | 工具 | 说明 |
|---|------|------|------|------|
| 1 | `general-purpose` | inherit | 全部 | 复杂多步任务 —— 默认代理类型，用于研究、代码搜索、自主工作 |
| 2 | `Explore` | haiku | 只读（无 Write/Edit） | 快速代码库搜索和探索 —— 查找文件、搜索代码、回答代码库问题 |
| 3 | `Plan` | inherit | 只读（无 Write/Edit） | plan mode 预规划研究 —— 探索代码库并设计实现方案 |
| 4 | `statusline-setup` | sonnet | Read/Edit | 配置用户的 Claude Code 状态栏设置 |
| 5 | `claude-code-guide` | haiku | Glob/Grep/Read/WebFetch/WebSearch | 回答 Claude Code 功能、Agent SDK、Claude API 问题 |

---

## 示例

```markdown
---
name: code-reviewer
description: 审查 PR 代码质量、安全漏洞、回归
tools: Read, Grep, Glob
model: opus
permissionMode: default
---

你是一个严格的代码审查员。审查以下内容：
1. 安全漏洞
2. 性能问题
3. 代码风格一致性
4. 潜在回归

输出格式：
- 问题列表（按严重程度排序）
- 每个问题附带代码位置和修复建议
```

---

> 原文：best-practice/claude-subagents.md