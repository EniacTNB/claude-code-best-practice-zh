# Claude Code Best Practice 中文翻译计划

> 原项目：https://github.com/shanraisshan/claude-code-best-practice
> 翻译目录：`~/project/claude-code-best-practice-zh/`

---

## 翻译原则

1. **完整翻译**：保留原文所有链接、徽章、图片、代码块，不精简
2. **结构一致**：保持原项目的目录结构和文件名
3. **术语统一**：
   - Slash command → 斜杠命令
   - Subagent → 子代理
   - Skill → Skill（保留英文）
   - MCP → MCP（保留英文）
   - Frontmatter → Frontmatter（保留英文）
   - Hook → 钩子
   - Sandbox → 沙箱

---

## 跳过内容（不翻译）

| 类别 | 原因 |
|------|------|
| `changelog/` | 更新日志，保持英文便于追溯 |
| `reports/` | 技术报告，专业性强，暂不翻译 |
| `implementation/` | 实现细节，开发者参考用 |
| `.claude/` | Claude 项目内部文件 |
| `.codex/` | Codex 内部文件 |
| `videos/` | 视频相关，无文字内容 |
| `LICENSE` | 法律文件，保持原文 |

---

## 翻译进度

### 📁 根目录

| 文件 | 状态 | 原大小 | 翻译大小 |
|------|------|--------|----------|
| `README.md` | ✅ 完成 | 55KB | 88KB |

### 📁 best-practice/

| 文件 | 状态 | 原大小 | 翻译大小 |
|------|------|--------|----------|
| `claude-subagents.md` | ✅ 完成 | 4KB | 4KB |
| `claude-skills.md` | ✅ 完成 | 3.4KB | 3.2KB |
| `claude-power-ups.md` | ✅ 完成 | 1.9KB | 1.9KB |
| `claude-memory.md` | ✅ 完成 | 5.4KB | 4.9KB |
| `claude-mcp.md` | ✅ 完成 | 5KB | 4.9KB |
| `claude-commands.md` | ✅ 完成 | 17KB | 8.4KB |
| `claude-cli-startup-flags.md` | ✅ 完成 | 9.7KB | 8KB |
| `claude-settings.md` | ⚠️ 精简版 | 66KB | 13KB |

### 📁 tutorial/

| 文件 | 状态 | 原大小 | 翻译大小 |
|------|------|--------|----------|
| `day0/README.md` | ✅ 完成 | 1.6KB | 1.5KB |
| `day0/linux.md` | ✅ 完成 | 3.7KB | 3.4KB |
| `day0/mac.md` | ✅ 完成 | 0.6KB | 0.6KB |
| `day0/windows.md` | ✅ 完成 | 0.8KB | 0.7KB |
| `day1/README.md` | ✅ 完成 | 6KB | 5.3KB |

### 📁 tips/（待翻译）

| 文件 | 状态 | 备注 |
|------|------|------|
| `claude-boris-10-tips-01-feb-26.md` | ❌ 未翻译 | Boris 10 条技巧 |
| `claude-boris-12-tips-12-feb-26.md` | ❌ 未翻译 | Boris 12 条技巧 |
| `claude-boris-13-tips-03-jan-26.md` | ❌ 未翻译 | Boris 13 条技巧 |
| `claude-boris-15-tips-30-mar-26.md` | ❌ 未翻译 | Boris 15 条技巧 |
| `claude-boris-2-tips-10-mar-26.md` | ❌ 未翻译 | Boris 2 条技巧 |
| `claude-boris-2-tips-25-mar-26.md` | ❌ 未翻译 | Boris 2 条技巧 |
| `claude-thariq-tips-16-apr-26.md` | ❌ 未翻译 | Thariq 技巧 |
| `claude-thariq-tips-17-mar-26.md` | ❌ 未翻译 | Thariq 技巧 |

### 📁 agent-teams/（待翻译）

| 文件 | 状态 | 备注 |
|------|------|------|
| `agent-teams-prompt.md` | ❌ 未翻译 | Agent teams 配置 |
| `output/output.md` | ❌ 未翻译 | 输出示例 |

### 📁 orchestration-workflow/（待翻译）

| 文件 | 状态 | 备注 |
|------|------|------|
| `orchestration-workflow.md` | ❌ 未翻译 | 编排工作流 |
| `output.md` | ❌ 未翻译 | 输出示例 |

### 📁 development-workflows/（待翻译）

| 文件 | 状态 | 备注 |
|------|------|------|
| `cross-model-workflow/cross-model-workflow.md` | ❌ 未翻译 | 跨模型工作流 |
| `rpi/rpi-workflow.md` | ❌ 未翻译 | RPI 工作流 |

---

## 统计

| 类别 | 数量 |
|------|------|
| 总文件数 | 58 |
| 跳过翻译 | 18 |
| 需翻译 | 40 |
| ✅ 已完成 | 14 |
| ⚠️ 精简版 | 1 |
| ❌ 未翻译 | 25 |

**已翻译文件总大小：约 140KB**

---

## 优先级

**P0 - 已完成：**
- ✅ README.md — 项目主文档
- ✅ best-practice/*.md — 核心参考文档（8个）
- ✅ tutorial/day0/*.md — 安装教程（4个）
- ✅ tutorial/day1/README.md — Day 1 教程

**P1 - 待翻译：**
- `tips/*.md` — Boris 和 Thariq 的技巧合集（8个）

**P2 - 待翻译：**
- `agent-teams/` — Agent teams 配置
- `orchestration-workflow/` — 编排工作流
- `development-workflows/` — 开发工作流

---

## 更新日志

| 日期 | 操作 |
|------|------|
| 2026-04-16 | 开始翻译，完成 8 个文件的精简版 |
| 2026-04-17 10:35 | 移动到 project 目录 |
| 2026-04-17 10:50 | 创建翻译计划文件 |
| 2026-04-17 11:04 | README.md 完整翻译完成（88KB） |
| 2026-04-17 11:15 | best-practice 目录 8 个文件完整翻译完成 |
| 2026-04-17 11:30 | tutorial/day0 和 day1 完整翻译完成 |
| 2026-04-17 11:35 | 更新翻译计划，总计 14 个文件完成 |

---

*最后更新：2026-04-17 11:35*
