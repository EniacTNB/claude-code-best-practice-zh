# Sub-agents 最佳实践

![Last Updated](https://img.shields.io/badge/Last_Updated-Apr%2014%2C%202026%2011%3A14%20PM%20PKT-white?style=flat&labelColor=555) ![Version](https://img.shields.io/badge/Claude_Code-v2.1.107-blue?style=flat&labelColor=555)<br>
[![Implemented](https://img.shields.io/badge/Implemented-2ea44f?style=flat)](../implementation/claude-subagents-implementation.md)

Claude Code subagents —— frontmatter 字段和官方内置代理类型。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## Frontmatter 字段 (16)

| 字段 | 类型 | 必需 | 描述 |
|-------|------|----------|-------------|
| `name` | string | 是 | 使用小写字母和连字符的唯一标识符 |
| `description` | string | 是 | 何时调用。用 `"PROACTIVELY"` 让 Claude 自动调用 |
| `tools` | string/list | 否 | 逗号分隔的工具白名单（如 `Read, Write, Edit, Bash`）。省略时继承所有工具。支持 `Agent(agent_type)` 语法限制可启动的子代理；旧版 `Task(agent_type)` 别名仍可用 |
| `disallowedTools` | string/list | 否 | 要拒绝的工具，从继承或指定的列表中移除 |
| `model` | string | 否 | 使用的模型：`sonnet`, `opus`, `haiku`, 完整模型 ID（如 `claude-opus-4-6`），或 `inherit`（默认：`inherit`） |
| `permissionMode` | string | 否 | 权限模式：`default`, `acceptEdits`, `auto`, `dontAsk`, `bypassPermissions`, 或 `plan` |
| `maxTurns` | integer | 否 | 子代理停止前的最大 agentic turns 数 |
| `skills` | list | 否 | 启动时预加载到代理上下文的 skill 名称（注入完整内容，不只是可用） |
| `mcpServers` | list | 否 | 此子代理的 MCP servers —— server 名称字符串或内联 `{name: config}` 对象 |
| `hooks` | object | 否 | 限定于此子代理的生命周期钩子。支持所有 hook 事件；`PreToolUse`, `PostToolUse`, 和 `Stop` 最常用 |
| `memory` | string | 否 | 持久记忆范围：`user`, `project`, 或 `local` |
| `background` | boolean | 否 | 设为 `true` 始终作为后台任务运行（默认：`false`） |
| `effort` | string | 否 | 此子代理活跃时的努力程度覆盖：`low`, `medium`, `high`, `max`（仅 Opus 4.6）。默认：继承会话设置 |
| `isolation` | string | 否 | 设为 `"worktree"` 在临时 git worktree 中运行（无更改时自动清理） |
| `initialPrompt` | string | 否 | 此代理作为主会话代理运行时（通过 `--agent` 或 `agent` 设置）自动提交为第一个用户回合。Commands 和 skills 会被处理。追加到任何用户提供的提示前 |
| `color` | string | 否 | 子代理在任务列表和 transcript 中的显示颜色：`red`, `blue`, `green`, `yellow`, `purple`, `orange`, `pink`, 或 `cyan` |

---

## ![Official](../!/tags/official.svg) **(5)**

| # | Agent | Model | Tools | 描述 |
|---|-------|-------|-------|-------------|
| 1 | `general-purpose` | inherit | All | 复杂多步任务 —— 研究、代码搜索和自主工作的默认代理类型 |
| 2 | `Explore` | haiku | Read-only（无 Write, Edit） | 快速代码库搜索和探索 —— 优化用于查找文件、搜索代码和回答代码库问题 |
| 3 | `Plan` | inherit | Read-only（无 Write, Edit） | plan mode 中的预规划研究 —— 在编写代码前探索代码库并设计实现方案 |
| 4 | `statusline-setup` | sonnet | Read, Edit | 配置用户的 Claude Code status line 设置 |
| 5 | `claude-code-guide` | haiku | Glob, Grep, Read, WebFetch, WebSearch | 回答关于 Claude Code 功能、Agent SDK 和 Claude API 的问题 |

---

## 来源

- [创建自定义 subagents — Claude Code Docs](https://code.claude.com/docs/en/sub-agents)
- [CLI 参考 — Claude Code Docs](https://code.claude.com/docs/en/cli-reference)
- [Claude Code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
