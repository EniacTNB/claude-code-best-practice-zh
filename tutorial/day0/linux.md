# Linux 安装指南

[返回 Day 0](README.md)

## 前置条件

需要 **Node.js v18 或更高版本** 和 **npm**。

## 步骤 1：安装 Node.js

### 方式 A：通过 nodejs.org下载页面配合 fnm（推荐）

**fnm**（Fast Node Manager）是 Node.js 官方推荐的。它快速、轻量，让你以后轻松切换 Node 版本。

1. 打开浏览器访问 [nodejs.org/en/download](https://nodejs.org/en/download)。

2. 你会看到一行下拉菜单：**"Get Node.js® vXX.XX.X (LTS) for __ using __ with __"**。设置如下：

   | 下拉菜单 | 选择 |
   |----------|--------|
   | 版本 | **vXX.XX.X (LTS)** — 保持默认 LTS 版本，不改 |
   | 操作系统 | **Linux** |
   | 包管理器 | **fnm**（在 "Recommended (Official)" 下） |
   | 包格式 | **npm** — 保持默认 |

3. 页面会显示具体命令。打开终端复制粘贴。类似这样：

   ```bash
   # 步骤 1 — 安装 fnm
   curl -fsSL https://fnm.vercel.app/install | bash

   # 步骤 2 — 重启终端或重载 shell 配置
   source ~/.bashrc   # 或：source ~/.zshrc（如果用 zsh）

   # 步骤 3 — 安装 Node.js
   fnm install 24   # 页面会显示具体版本号
   ```

   > 版本号可能与上面不同 —— 始终用网站显示的。

4. **关闭并重新打开终端**（或运行上面的 `source` 命令），让 `fnm`、`node` 和 `npm` 可用。

> **为什么用 fnm？** 它在 Node.js 下载页的 "Recommended (Official)" 类别。像 nvm 一样，它把 Node 安装到家目录，npm 全局安装不需要 `sudo` —— 但 fnm 更快（用 Rust 写）且跨平台一致。

### 方式 B：用发行版包管理器

更快但可能安装旧版 Node.js。**安装后检查版本** —— 如果低于 v18，用方式 A。

**Ubuntu / Debian:**

```bash
sudo apt update
sudo apt install -y nodejs npm

# 检查版本
node --version   # 必须是 v18 或更高
```

**Fedora:**

```bash
sudo dnf install -y nodejs npm
```

**Arch Linux:**

```bash
sudo pacman -S nodejs npm
```

### 方式 C：NodeSource（通过 apt 安装最新 LTS，不用 nvm）

Ubuntu/Debian 用户想要最新 LTS 不用 nvm：

```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs
```

## 步骤 2：验证 Node.js

```bash
node --version
npm --version
```

两个都应打印版本号。`node --version` 必须显示 v18.x 或更高。

## 步骤 3：安装 Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

> **权限错误？**
> - 如果用 **fnm** 或 **nvm**：不应该发生。检查是否激活（`which node` 应指向家目录内路径，不是 `/usr/...`）。
> - 如果用系统安装：要么用 `sudo npm install -g @anthropic-ai/claude-code`，要么修复 npm 全局目录权限：
>   ```bash
>   mkdir -p ~/.npm-global
>   npm config set prefix '~/.npm-global'
>   echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
>   source ~/.bashrc
>   ```

## 步骤 4：验证 Claude Code

```bash
claude --version
```

应看到 Claude Code 版本打印。现在返回 [README.md](README.md) 进行认证设置。

---

## 注意

- **WSL（Windows Subsystem for Linux）：** 本指南在 WSL 内也适用。在 WSL 终端按这些步骤即可。
- **PATH 问题：** 如果安装后找不到 `claude`，确保 npm 全局 bin 在 PATH 中。运行 `npm config get prefix` —— 该路径的 `bin/` 子目录需要在 PATH 中。
