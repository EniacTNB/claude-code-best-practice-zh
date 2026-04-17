# Skills 最佳实践

![Last Updated](https://img.shields.io/badge/Last_Updated-Apr%2014%2C%202026%2011%3A13%20PM%20PKT-white?style=flat&labelColor=555) ![Version](https://img.shields.io/badge/Claude_Code-v2.1.107-blue?style=flat&labelColor=555)<br>
[![Implemented](https://img.shields.io/badge/Implemented-2ea44f?style=flat)](../implementation/claude-skills-implementation.md)

Claude Code skills —— frontmatter 字段和官方捆绑 skills。

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
| `description` | string | 推荐 | Skill 功能。显示在自动补全中，用于 Claude 自动发现 |
| `when_to_use` | string | 否 | Claude 应何时调用此 skill 的额外上下文 —— 触发短语和示例请求。追加到 skill 列表中的 `description`，计入 1536 字符上限 |
| `argument-hint` | string | 否 | 自动补全时显示的提示（如 `[issue-number]`, `[filename]`) |
| `disable-model-invocation` | boolean | 否 | 设 `true` 阻止 Claude 自动调用此 skill |
| `user-invocable` | boolean | 否 | 设 `false` 从 `/` 菜单隐藏 —— skill 仅作为背景知识，用于代理预加载 |
| `allowed-tools` | string | 否 | 此 skill 激活时无需权限提示即可使用的工具 |
| `model` | string | 否 | 此 skill 运行时使用的模型（如 `haiku`, `sonnet`, `opus`) |
| `effort` | string | 否 | 调用时覆盖模型努力程度级别（`low`, `medium`, `high`, `max`) |
| `context` | string | 否 | 设为 `fork` 在隔离子代理上下文中运行 skill |
| `agent` | string | 否 | `context: fork` 时的子代理类型（默认：`general-purpose`) |
| `hooks` | object | 否 | 限定于此 skill 的生命周期钩子 |
| `paths` | string/list | 否 | 限制 skill 自动激活时的 glob 模式。接受逗号分隔字符串或 YAML 列表 —— Claude 仅在处理匹配文件时加载 skill |
| `shell` | string | 否 | `` !`command` `` 块的 shell —— `bash`（默认）或 `powershell`。需要 `CLAUDE_CODE_USE_POWERSHELL_TOOL=1` |

---

## ![Official](../!/tags/official.svg) **(5)**

| # | Skill | 描述 |
|---|-------|-------------|
| 1 | `simplify` | 审查变更代码的复用性、质量和效率 —— 重构消除重复 |
| 2 | `batch` | 批量跨多个文件运行命令 |
| 3 | `debug` | 调试失败的命令或代码问题 |
| 4 | `loop` | 按周期运行提示或斜杠命令（最长 3 天） |
| 5 | `claude-api` | 用 Claude API 或 Anthropic SDK 构建应用 —— 在 `anthropic` / `@anthropic-ai/sdk` 导入时触发 |

参见：[官方 Skills 仓库](https://github.com/anthropics/skills/tree/main/skills) 获取社区维护的可安装 skills。

---

## 来源

- [Claude Code Skills — Docs](https://code.claude.com/docs/en/skills)
- [Monorepo 中的 Skills 发现](../reports/claude-skills-for-larger-mono-repos.md)
- [Claude Code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
