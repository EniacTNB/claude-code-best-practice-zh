# claude-code-best-practice 中文版

从 vibe coding 到 agentic engineering —— 熟能生巧

> 本文档是从英文版精简翻译，保留了核心概念和技巧，省略了大量链接和徽章。

---

## 🧠 核心概念

| 功能 | 位置 | 说明 |
|------|------|------|
| **Subagents（子代理）** | `.claude/agents/<name>.md` | 在独立上下文中运行的自主代理 —— 可自定义工具、权限、模型、记忆和持久身份 |
| **Commands（命令）** | `.claude/commands/<name>.md` | 注入到现有上下文的知识 —— 用户调用的简单提示模板，用于工作流编排 |
| **Skills（技能）** | `.claude/skills/<name>/SKILL.md` | 注入到现有上下文的知识 —— 可配置、可预加载、自动发现，支持上下文 fork 和渐进式披露 |
| **Workflows（工作流）** | `.claude/commands/` | 通过命令编排多代理协作 |
| **Hooks（钩子）** | `.claude/hooks/` | 在代理循环之外的用户定义处理器（脚本、HTTP、提示、代理），在特定事件触发 |
| **MCP Servers** | `.mcp.json` | 连接外部工具、数据库和 API 的 Model Context Protocol |
| **Settings（设置）** | `.claude/settings.json` | 分层配置系统 —— 权限、模型配置、输出样式、沙箱、快捷键等 |
| **Memory（记忆）** | `CLAUDE.md`、`.claude/rules/` | 通过 CLAUDE.md 文件和 `@path` 导入实现持久上下文 |

---

## 🔥 热门新功能

| 功能 | 说明 |
|------|------|
| **Routines** | Anthropic 云基础设施上的自动化 —— 定时任务、API触发、GitHub事件驱动，机器关闭时也能运行 |
| **Ultraplan** | 在云端起草计划，浏览器端审阅、批注，灵活执行 |
| **Auto Mode** | 后台安全分类器替代手动权限确认 —— Claude 自动判断什么是安全的 |
| **Computer Use** | 让 Claude 控制你的屏幕 —— 打开应用、点击、输入、截图（macOS） |
| **Agent SDK** | 将 Claude Code 作为库构建生产级 AI 代理 —— Python 和 TypeScript SDK |
| **Voice Dictation** | 按键说话输入提示，支持20种语言 |

---

## 💡 提示与技巧（核心精选）

### Prompting

- 挑战 Claude —— "质问我这些改动，通过测试才能做 PR"
- 修复平庸方案后 —— "基于现在知道的一切，抛弃这个，实现优雅方案"
- Claude 自己能修大多数 bug —— 粘贴 bug，说 "修复"，不要微观管理

### Planning

- 总是从 **plan mode** 开始
- 从最小 spec 开始，让 Claude 用 AskUserQuestion 工具采访你
- 原型 > PRD —— 做 20-30 个版本而不是写 spec

### CLAUDE.md

- 每个文件控制在 **200 行以内**
- 用 `<important if="...">` 包裹关键规则
- 保持代码库干净 —— 部分迁移的框架会混淆模型

### Agents

- 说 "用 subagents" —— 把更多算力投入问题，保持主上下文干净
- 用 tmux + git worktrees 实现代理团队并行开发
- 分离上下文让结果更好 —— 一个代理制造 bug，另一个能发现

### Skills

- 用 `context: fork` 在隔离上下文中运行技能
- description 字段是触发器，写 "何时触发" 而不是摘要
- 不要 railroad Claude —— 给目标和约束，不给步骤指令
- 每个技能建 Gotchas 章节 —— 记录失败点

### Hooks

- 用 on-demand hooks —— /careful 阻止破坏性命令
- 用 PostToolUse hook 自动格式化代码

### Workflows

- 避免代理 dumb zone，手动 /compact 最多 50%
- 用 /model 选模型，/context 看上下文用量
- 在提示中用 ultrathink 关键词获得高努力推理

### Git / PR

- 保持 PR 小 —— 中位数 118 行
- 总是 squash merge
- 经常提交，每小时至少一次
- tag @claude 在同事 PR 上自动生成 lint 规则

### Debugging

- 卡住时截图分享给 Claude
- 用 MCP 让 Claude 自己看 console 日志
- 用 cross-model 做 QA —— Codex 计划和实现审阅

---

## 子代理 Frontmatter 字段

| 字段 | 说明 |
|------|------|
| `name` | 唯一标识符，小写+连字符 |
| `description` |何时触发，用 "PROACTIVELY" 实现自动调用 |
| `tools` | 工具白名单，如 `Read, Write, Edit, Bash` |
| `model` | 模型：sonnet/opus/haiku 或 inherit |
| `permissionMode` | 权限模式：default/auto/plan 等 |
| `skills` | 启动时预加载的技能 |
| `mcpServers` | 该子代理的 MCP 服务器 |
| `hooks` | 生命周期钩子 |
| `memory` | 持久记忆范围：user/project/local |

---

## 技能 Frontmatter 字段

| 字段 | 说明 |
|------|------|
| `name` | 显示名和 /slash-command 标识符 |
| `description` | 描述，用于自动发现 |
| `when_to_use` | 触发上下文 |
| `context` | 设为 fork 在隔离子代理运行 |
| `agent` | context: fork 时的子代理类型 |
| `paths` | 限制自动激活的 glob 模式 |

---

## 官方内置代理（5 种）

| 代理 | 说明 |
|------|------|
| `general-purpose` | 复杂多步任务 —— 默认代理类型 |
| `Explore` | 快速代码库搜索和探索（haiku，只读） |
| `Plan` | plan mode 预规划研究（只读） |
| `statusline-setup` | 配置状态栏设置 |
| `claude-code-guide` | 回答 Claude Code 功能问题 |

---

## 官方内置技能（5 种）

| 技能 | 说明 |
|------|------|
| `simplify` | 审查代码的复用、质量、效率 |
| `batch` | 批量跨文件运行命令 |
| `debug` | 调试失败命令或代码问题 |
| `loop` | 定时循环运行提示（最多3天） |
| `claude-api` | 构建 Claude API 应用 |

---

> 原项目地址：https://github.com/shanraisshan/claude-code-best-practice