# CLI 启动参数最佳实践

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar%2002%2C%202026-white?style=flat&labelColor=555)

从终端启动 Claude Code 时的启动参数、顶级子命令和启动环境变量参考。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 目录

1. [会话管理](#会话管理)
2. [模型与配置](#模型与配置)
3. [权限与安全](#权限与安全)
4. [输出与格式](#输出与格式)
5. [系统提示](#系统提示)
6. [代理与子代理](#代理与子代理)
7. [MCP 与插件](#mcp与插件)
8. [目录与工作区](#目录与工作区)
9. [预算与限制](#预算与限制)
10. [集成](#集成)
11. [初始化与维护](#初始化与维护)
12. [调试与诊断](#调试与诊断)
13. [设置覆盖](#设置覆盖)
14. [版本与帮助](#版本与帮助)
15. [子命令](#子命令)
16. [环境变量](#环境变量)

---

## 会话管理

| 标志 | 简写 | 描述 |
|------|-------|-------------|
| `--continue` | `-c` | 继续当前目录最近的对话 |
| `--resume` | `-r` | 按 ID 或名称恢复特定会话，或显示交互选择器 |
| `--from-pr <NUMBER\|URL>` | | 恢复链接到特定 GitHub PR 的会话 |
| `--fork-session` | | 恢复时创建新会话 ID（配合 `--resume` 或 `--continue`） |
| `--session-id <UUID>` | | 使用特定会话 ID（必须是有效 UUID） |
| `--no-session-persistence` | | 禁用会话持久化（仅 print 模式） |
| `--remote` | | 在 claude.ai 创建新 web 会话 |
| `--teleport` | | 在本地终端恢复 web 会话 |

---

## 模型与配置

| 标志 | 简写 | 描述 |
|------|-------|-------------|
| `--model <NAME>` | | 设置模型（别名或完整 ID） |
| `--fallback-model <NAME>` | | 默认模型过载时的自动回退模型（仅 print 模式） |
| `--betas <LIST>` | | API 请求中包含的 beta headers（仅 API key 用户） |

---

## 权限与安全

| 标志 | 简写 | 描述 |
|------|-------|-------------|
| `--dangerously-skip-permissions` | | 跳过所有权限提示。极度谨慎使用 |
| `--allow-dangerously-skip-permissions` | | 启用权限绕过选项但不激活 |
| `--permission-mode <MODE>` | | 以指定权限模式开始：`default`, `plan`, `acceptEdits`, `bypassPermissions` |
| `--allowedTools <TOOLS>` | | 无需提示即可执行的工具 |
| `--disallowedTools <TOOLS>` | | 从模型上下文完全移除的工具 |
| `--tools <TOOLS>` | | 限制 Claude 可用的内置工具 |
| `--permission-prompt-tool <TOOL>` | | 指定 MCP 工具处理非交互模式的权限提示 |

---

## 输出与格式

| 标志 | 简写 | 描述 |
|------|-------|-------------|
| `--print` | `-p` | 打印响应不进入交互模式（headless/SDK 模式） |
| `--output-format <FORMAT>` | | 输出格式：`text`, `json`, `stream-json` |
| `--input-format <FORMAT>` | | 输入格式：`text`, `stream-json` |
| `--json-schema <SCHEMA>` | | 获取匹配 schema 的验证 JSON（仅 print 模式） |
| `--include-partial-messages` | | 包含部分流事件（需 `--print` + `--output-format=stream-json`） |
| `--verbose` | | 启用详细日志 |

---

## 系统提示

| 标志 | 简写 | 描述 |
|------|-------|-------------|
| `--system-prompt <TEXT>` | | 用自定义文本替换整个系统提示 |
| `--system-prompt-file <PATH>` | | 从文件加载系统提示，替换默认（仅 print 模式） |
| `--append-system-prompt <TEXT>` | | 追加自定义文本到默认系统提示 |
| `--append-system-prompt-file <PATH>` | | 追加文件内容到默认提示（仅 print 模式） |

---

## 代理与子代理

| 标志 | 简写 | 描述 |
|------|-------|-------------|
| `--agent <NAME>` | | 指定当前会话的代理 |
| `--agents <JSON>` | | 通过 JSON 动态定义自定义子代理 |
| `--teammate-mode <MODE>` | | 设置代理团队显示：`auto`, `in-process`, `tmux` |

---

## MCP 与插件

| 标志 | 简写 | 描述 |
|------|-------|-------------|
| `--mcp-config <PATH\|JSON>` | | 从 JSON 文件或字符串加载 MCP servers |
| `--strict-mcp-config` | | 仅使用 `--mcp-config` 的 MCP servers |
| `--plugin-dir <PATH>` | | 仅此会话从目录加载插件（可重复） |

---

## 目录与工作区

| 标志 | 简写 | 描述 |
|------|-------|-------------|
| `--add-dir <PATH>` | | 添加 Claude 可访问的额外工作目录 |
| `--worktree` | `-w` | 在隔离 git worktree 中启动 Claude |

---

## 预算与限制

| 标志 | 简写 | 描述 |
|------|-------|-------------|
| `--max-budget-usd <AMOUNT>` | | API 调用停止前的最大金额（仅 print 模式） |
| `--max-turns <NUMBER>` | | 限制 agentic turns 数（仅 print 模式） |

---

## 集成

| 标志 | 简写 | 描述 |
|------|-------|-------------|
| `--chrome` | | 启用 Chrome 浏览器集成 |
| `--no-chrome` | | 此会话禁用 Chrome 浏览器集成 |
| `--ide` | | 启动时自动连接 IDE |

---

## 初始化与维护

| 标志 | 简写 | 描述 |
|------|-------|-------------|
| `--init` | | 运行初始化钩子并启动交互模式 |
| `--init-only` | | 运行初始化钩子并退出（无交互会话） |
| `--maintenance` | | 运行维护钩子并退出 |

---

## 调试与诊断

| 标志 | 简写 | 描述 |
|------|-------|-------------|
| `--debug <CATEGORIES>` | | 启用带可选类别过滤的调试模式 |

---

## 设置覆盖

| 标志 | 简写 | 描述 |
|------|-------|-------------|
| `--settings <PATH\|JSON>` | | 加载设置的 JSON 文件路径或 JSON 字符串 |
| `--setting-sources <LIST>` | | 加载的来源列表：`user`, `project`, `local` |
| `--disable-slash-commands` | | 此会话禁用所有 skills 和斜杠命令 |

---

## 版本与帮助

| 标志 | 简写 | 描述 |
|------|-------|-------------|
| `--version` | `-v` | 输出版本号 |
| `--help` | `-h` | 显示帮助信息 |

---

## 子命令

顶级命令，运行方式 `claude <subcommand>`：

| 子命令 | 描述 |
|------------|-------------|
| `claude` | 启动交互 REPL |
| `claude "query"` | 带初始提示启动 REPL |
| `claude agents` | 列出配置的代理 |
| `claude auth` | 管理 Claude Code 认证 |
| `claude doctor` | 从命令行运行诊断 |
| `claude install` | 安装或切换 Claude Code 原生构建 |
| `claude mcp` | 配置 MCP servers（`add`, `remove`, `list`, `get`, `enable`） |
| `claude plugin` | 管理 Claude Code 插件 |
| `claude remote-control` | 管理远程控制会话 |
| `claude setup-token` | 创建订阅使用的长效 token |
| `claude update` / `claude upgrade` | 更新到最新版本 |

---

## 环境变量

这些启动专用环境变量在启动 Claude Code 前在 shell 中设置（无法通过 `settings.json` 配置）：

| 变量 | 描述 |
|----------|-------------|
| `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` | 启用实验性代理团队 |
| `CLAUDE_CODE_TMPDIR` | 覆盖内部文件的临时目录 |
| `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1` | 启用额外目录 CLAUDE.md 加载 |
| `DISABLE_AUTOUPDATER=1` | 禁用自动更新 |
| `CLAUDE_CODE_EFFORT_LEVEL` | 控制思考深度 |
| `USE_BUILTIN_RIPGREP=0` | 使用系统 ripgrep而非内置（Alpine Linux） |
| `CLAUDE_CODE_SIMPLE` | 启用简单模式（仅 Bash + Edit 工具） |
| `CLAUDE_BASH_NO_LOGIN=1` | BashTool 跳过登录 shell |
| `CCR_FORCE_BUNDLE=1` | 使用 `--remote` 时强制捆绑/上传本地仓库 |

可通过 `settings.json` `"env"` 键配置的环境变量见 [Claude Settings Reference](./claude-settings.md#environment-variables-via-env)。

---

## 来源

- [Claude Code CLI Reference](https://code.claude.com/docs/en/cli-reference)
- [Claude Code Headless Mode](https://code.claude.com/docs/en/headless)
- [Claude Code Setup](https://code.claude.com/docs/en/setup)
- [Claude Code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
