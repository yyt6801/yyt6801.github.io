# OpenCode —— 开源 AI Coding Agent 介绍与操作指南


## OpenCode 简介

[OpenCode](https://opencode.ai) 是一款开源的 AI 编程代理（coding agent），在终端中运行，能理解项目上下文并直接操作代码。GitHub 160K&#43; Stars，每月超 750 万开发者使用。

- **开源免费** — MIT 协议，无需额外订阅
- **终端原生** — 不依赖 IDE，任何编辑器都能用
- **模型自由** — 支持 Claude、GPT、Gemini 及 75&#43; 模型提供商

---

## 安装

前提：已安装 Node.js。

```Bash
npm install -g opencode-ai
```

验证安装：

```Bash
opencode --version
```

---

## 快速上手

### 1. 启动

在项目根目录运行：

```Bash
opencode
```

### 2. 初始化

在 TUI 中输入 `/init`，OpenCode 会自动分析项目结构，生成 `AGENTS.md` 文件（建议提交到 Git）。

### 3. 配置模型

首次使用需配置 LLM 提供商。在 TUI 中输入 `/connect`，选择 provider 并填入 API Key 即可。

推荐使用 [OpenCode Zen](https://opencode.ai/zen)（官方精选模型，即开即用）或接入自己的 API Key。

---

## 核心操作

### 两种模式（Tab 键切换）

| 模式 | 说明 |
|------|------|
| **Build** | 默认模式，可读写文件、执行命令，适合实际开发 |
| **Plan** | 只读模式，仅分析代码和生成计划，不改动任何文件 |

### 常用命令

| 命令 | 用途 |
|------|------|
| `/init` | 初始化项目，生成 AGENTS.md |
| `/connect` | 配置 LLM 模型提供商 |
| `/undo` | 撤销上一步改动（可多次撤销） |
| `/redo` | 重做已撤销的改动 |
| `/share` | 生成当前会话分享链接 |
| `/help` | 查看帮助 |

### 提问代码

使用 `@` 模糊搜索项目文件，快速定位上下文：

```
这个项目的认证逻辑是怎么实现的？@packages/functions/src/api/index.ts
```

### 添加功能

先用 Plan 模式制定方案，确认后再切回 Build 模式执行：

```
用户删除笔记后，将其标记为已删除，并提供一个恢复已删除笔记的页面。
```

Plan 模式下 OpenCode 只会输出方案，不修改代码；Build 模式才会实际执行。

### 修改代码

直接描述需求即可：

```
给 /settings 路由加上认证，参考 @packages/functions/src/notes.ts 的实现。
```

### 撤销修改

改错了？直接 `/undo` 恢复，可多次回退。

---

## 核心优势

1. **项目级理解** — 自动加载 LSP，理解代码结构、类型和依赖关系
2. **多会话并行** — 可同时启动多个 agent 处理不同任务
3. **隐私安全** — 不存储代码数据，适合敏感环境
4. **高度可定制** — 自定义命令、规则、主题、快捷键、Agents、MCP 服务器等
5. **零锁定** — 自由切换模型、编辑器、运行环境

---

## 参考

- 官网：https://opencode.ai
- 文档：https://opencode.ai/docs
- GitHub：https://github.com/anomalyco/opencode


---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/opencode-intro-guide/  

