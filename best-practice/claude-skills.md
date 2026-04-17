# Skills（技能）最佳实践

Claude Code 技能 —— frontmatter 字段和官方内置技能。

---

## Frontmatter 字段（14 个）

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `name` | string | 否 | 显示名和 /slash-command 标识符。省略则默认为目录名 |
| `description` | string | 推荐 | 技能做什么。显示在自动补全，用于自动发现 |
| `when_to_use` | string | 否 | 额外触发上下文 —— 何时调用、示例请求。附加到 description |
| `argument-hint` | string | 否 | 自动补全时显示的提示（如 `[issue-number]`） |
| `disable-model-invocation` | boolean | 否 | 设为 true 阻止 Claude 自动调用此技能 |
| `user-invocable` | boolean | 否 | 设为 false 从 / 菜单隐藏 —— 只作后台知识，用于代理预加载 |
| `allowed-tools` | string | 否 | 技能激活时无需权限确认的工具 |
| `model` | string | 否 | 运行技能时用的模型（haiku/sonnet/opus） |
| `effort` | string | 否 | 覆盖模型努力级别（low/medium/high/max） |
| `context` | string | 否 | 设为 fork 在隔离子代理上下文运行 |
| `agent` | string | 否 | context: fork 时的子代理类型（默认 general-purpose） |
| `hooks` | object | 否 | 该技能的生命周期钩子 |
| `paths` | string/list | 否 | 限制自动激活的 glob 模式 —— 只在匹配文件时加载 |
| `shell` | string | 否 | `` !`command` `` 块的 shell —— bash（默认）或 powershell |

---

## 官方内置技能（5 种）

| # | 技能 | 说明 |
|---|------|------|
| 1 | `simplify` | 审查变更代码的复用、质量、效率 —— 重构消除重复 |
| 2 | `batch` | 批量跨多文件运行命令 |
| 3 | `debug` | 调试失败命令或代码问题 |
| 4 | `loop` | 定时循环运行提示或 slash 命令（最多 3 天） |
| 5 | `claude-api` | 构建 Claude API 或 Anthropic SDK 应用 —— 在 `anthropic` / `@anthropic-ai/sdk` 导入时触发 |

---

## 示例

```markdown
---
name: test-runner
description: 运行测试并分析失败原因
when_to_use: 当用户提到测试失败、运行测试、修复测试时
allowed-tools: Bash, Read, Grep
context: fork
---

## 规则

1. 先运行 `npm test` 或等效命令
2. 分析失败输出，定位根本原因
3. 提出修复方案

## Gotchas

- 某些测试需要特定环境变量
- watch 模式测试不适合一次性运行
```

---

> 原文：best-practice/claude-skills.md