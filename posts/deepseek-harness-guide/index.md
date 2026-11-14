# DeepSeek Harness (DSH) 完全指南：从入门到插件开发实践


## 什么是 DeepSeek Harness (DSH)

### 一句话概括

**DeepSeek Harness (DSH)** 是一个开源的 coding / 自动化 agent 框架，采用 **一切皆插件** 的架构：核心只留 AgentLoop 与抽象缝，具体能力全靠插件组合。

### 核心定位

| 特性 | DSH 定位 |
|------|----------|
| **本质** | 独立进程，有自己的配置(~/.dsh/)、会话(~/.dsh/sessions/)、权限与沙箱体系 |
| **交互方式** | 非聊天框，也非 IDE 补全插件，而是可组合的能力平台 |
| **模型支持** | 多 Provider、多模型路由（llm-pi-ai），可灵活切换模型 |
| **扩展方式** | 写插件扩展能力，通过 patch 叠加配置而非修改核心 |
| **工作台** | 支持 Web 工作台（dsh web）和 CLI 一次性任务（dsh --profile headless） |

&gt; **避免误区**：DSH 不是固定功能的工具，而是一个**可组合的框架**。核心不包含具体能力，仅提供循环与抽象接口（ctx.llm、ctx.tools、ctx.sandbox 等），实际能力通过插件完全由社区和用户贡献。

---

## DSH 是什么

### 1. 独立进程的 Agent

DSH 以独立进程运行，拥有独立的配置和会话管理：

- **配置目录**：`~/.dsh/` - 存储用户配置、Profile、Bundle 等
- **会话目录**：`~/.dsh/sessions/` - 每次会话的历史记录和状态
- **权限与沙箱**：内置权限预设和命令沙箱，确保操作的安全性

### 2. 非聊天框的能力平台

与其将 DSH 视为聊天机器人，不如将其视为一个**可插拔能力的工作台**：

- **读写文件**：`tool-fs` 插件处理文件系统操作
- **执行命令**：`tool-bash` 插件执行 shell 命令  
- **搜索历史**：`session_search` 工具查询历史会话
- **多模型路由**：支持多 Provider 模型自由切换
- **多 Agent 协作**：可以将任务拆分给多个 Agent 并行执行

### 3. 插件化架构

**&#34;一切皆插件&#34;** 是 DSH 的核心设计哲学：

| 概念 | 说明 |
|------|------|
| **SDK** | 从另一进程驱动，基于 JSON-RPC wire protocol / client / server |
| **Cordis** | 底座框架，提供 Context/服务注入/插件装载的组合框架 |
| **Harness** | 组合出来的完整 agent 应用：循环、工具、会话、沙箱 |
| **Profile** | 插件的组合配置：bundle patch 层叠加用户覆盖层 |
| **Bundle** | 自带 patch 的插件包，作为一层加入 profile |
| **Seam** | 能力缝——抽象能力接口（ctx.llm、ctx.tools、ctx.sandbox…），实现可换各能力包 |

---

## DSH 核心概念

### 1. 主要概念解释

| 概念 | 位置/说明 |
|------|----------|
| **SDK** | `packages/sdk` - 从另一进程驱动的核心库，实现 client / server 架构 |
| **Cordis** | `@deepseek-ai/cordis` - 底座框架，提供 Context/服务注入/插件装载功能 |
| **Harness** | 完整的 agent 应用组合：包含循环机制、工具集、会话管理、沙箱环境 |
| **Profile** | `$DSH_HOME/profiles/&lt;name&gt;` - 插件的组合配置，支持层叠叠加 |
| **Bundle** | 自带 patch 的插件包，作为基础层加入到 Profile 中 |
| **Seam** | 抽象能力接口：`ctx.llm`、`ctx.tools`、`ctx.sandbox` 等，实现能力的可插拔 |

### 2. 三种启动方式

| 方式 | 命令 | 说明 |
|------|------|------|
| **Web 工作台** | `npx @deepseek-ai/dsh web` | 官方零安装入口，启动 Web UI（默认 127.0.0.1:3080） |
| **一次性任务** | `dsh --profile headless &#34;跑一下测试&#34;` | 执行完即退出，不启动 Web 界面 |
| **显式 Profile** | `dsh --profile web` | 显式指定使用 web profile |

&gt; **说明**：`web` 与 `headless` 是内置 Profile 模板。`--profile headless &lt;task&gt;` 是一次性任务入口（`dsh run` 已移除）。

### 3. 核心能力速览

DSH 可以进行以下类型的操作：

| 场景 | 如何实现 |
|------|----------|
| **改代码 / 读文件** | `tool-fs` / 文件系统插件 |
| **跑命令** | `tool-bash` 插件 |
| **搜索历史会话** | `session_search(dsh-tool-session-query)` |
| **任务拆分给多 Agent** | 子 Agent / 工作流定义 |
| **定时提醒/调度** | 定时调度接口 |
| **接入外部工具 (MCP)** | MCP 集成插件 |
| **长期目标推进** | 目标与待办管理系统 |

---

## 三种启动方式详解

### 1. npx @deepseek-ai/dsh web（推荐入口）

```bash
# 全局安装或使用 npx
npx @deepseek-ai/dsh web

# 或直接使用 dsh 命令（需提前安装）
dsh web
```

**效果**：启动 Web 工作台，访问 http://127.0.0.1:3080，提供可视化的 Agent 交互界面。

### 2. dsh web - 一次性任务

```bash
# 执行一个测试任务，完成后进程退出
dsh --profile headless &#34;列出当前目录下的前 5 个文件&#34;
```

**适用场景**：不需要持久化会话的快速任务执行。

### 3. dsh --profile web（显式指定）

```bash
dsh --profile web
```

**适用场景**：明确使用 web profile 的模式，确保加载对应的插件组合。

---

## 一切皆插件的哲学

### 1. 架构设计原则

DSH 采用 **&#34;一切皆插件&#34;** 的顶层设计：

&gt; **官方原文**：“它采用了『一切皆插件』的架构：新增能力 = 写插件(工具、服务、事件、UI)，修改行为 = 换插件 / 叠 patch 层,不改核心。核心只留一个 AgentLoop 具体循环：&#39;新行为应放入插件,而不是这里&#39;。”

### 2. 插件类型分类

| 插件类型 | 说明 | 示例 |
|----------|------|------|
| **工具插件** | 提供具体的执行能力（文件、命令、网络等） | `tool-fs`、`tool-bash`、`session_search` |
| **服务插件** | 提供持久化或外部服务集成 | MCP 集成、数据库连接、API 服务 |
| **事件插件** | 处理异步事件和钩子 | Webhook 规则、定时任务触发 |
| **UI 面板插件** | 扩展可视化界面 | 工作台扩展、自定义视图 |

### 3. 行为修改机制

- **换插件**：直接替换对应的插件实现
- **叠 patch 层**：在 Profile 中通过 patch 机制叠加配置，而不修改核心代码
- **配置覆盖**：用户自定义配置优先于默认配置

---

## 快速开始

### 1. 快速验证当前插件组合

```bash
# 查看当前组合的插件树（层叠结构一目了然）
dsh web --dump-config

# 查看默认 bundle 的组合结构
dsh web --dump-default-config
```

### 2. 从哪开始？

根据你的目标，选择对应的学习路径：

| 目标 | 推荐入口 |
|------|----------|
| **先跑起来** | 快速上手 |
| **理解架构** | 架构总览 |
| **理解主循环** | Agent 主循环 |
| **动手写插件** | 第一个插件 |

### 3. 第一步：安装 DSH

```bash
# 方法一：使用 npm/npx
npm install -g @deepseek-ai/dsh
# 或
npx @deepseek-ai/dsh --version

# 方法二：通过 pip (如果有 Python 版本)
pip install deepseek-harness

# 方法三：从源码安装
git clone https://github.com/deepseek-ai/deepseek-harness.git
cd deepseek-harness
pip install -e .
```

### 3. 第四步：启动你的第一个 DSH 会话

```bash
# 启动 Web 工作台（最常用的方式）
dsh web

# 或者直接使用 npx
npx @deepseek-ai/dsh web
```

访问 http://127.0.0.1:3080 即可看到 DSH 的 Web 界面，开始进行文件操作、命令执行等。

### 4. 第五步：尝试一次性任务

```bash
# 列出当前目录文件
dsh --profile headless &#34;ls -la&#34;

# 或者通过 npx
npx @deepseek-ai/dsh --profile headless &#34;pwd &amp;&amp; whoami&#34;
```

---

## 插件开发实践

### 1. 编写第一个插件

DSH 的插件系统相对开放，主要涉及以下步骤：

**文件结构示例**：

```bash
my-plugin/
├── plugin.json          # 插件元数据定义
├── index.js             # 插件主入口
├── tool-fs.js           # 文件系统工具实现
└── tool-bash.js         # 命令执行工具实现
```

**plugin.json 示例**：

```json
{
  &#34;name&#34;: &#34;my-custom-plugin&#34;,
  &#34;version&#34;: &#34;0.1.0&#34;,
  &#34;description&#34;: &#34;自定义插件示例&#34;,
  &#34;provider&#34;: &#34;deepseek-ai&#34;,
  &#34;seams&#34;: [&#34;tool-fs&#34;, &#34;tool-bash&#34;],
  &#34;entry&#34;: &#34;index.js&#34;
}
```

**index.js 示例**：

```javascript
const { createPlugin } = require(&#39;@deepseek-ai/cordis&#39;);

module.exports = createPlugin({
  name: &#39;my-custom-plugin&#39;,
  async onLoad(context) {
    // 注册自定义工具
    context.registerTool(&#39;my-tool&#39;, {
      description: &#39;我的自定义工具&#39;,
      execute: async (args) =&gt; {
        // 实现具体逻辑
        return { result: &#39;success&#39; };
      }
    });
  }
});
```

### 2. 插件开发要点

| 要点 | 说明 |
|------|------|
| **Seam 注册** | 在 `onLoad` 中通过 `context.registerTool()`、`context.registerService()` 注册能力 |
| **Context 获取** | 通过 `context.llm`、`context.tools`、`context.sandbox` 等获取核心能力 |
| **Patch 机制** | 通过 Profile 的 patch 层实现配置的叠加和自定义 |
| **版本兼容** | 注意插件版本与 DSH/Core 版本的兼容性 |

### 3. 发布与分享插件

- 将插件发布到 npm: `npm publish`
- 或提交到 DeepSeek Harness 社区仓库
- 配合 Profile 进行版本管理和分发

---

## 常见问题与排查

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| `Command not found: dsh` | 未安装或未配置环境变量 | `npm install -g @deepseek-ai/dsh` 或检查 PATH |
| Web 界面启动失败（端口占用） | 3080 端口被占用 | 修改配置：`dsh web --port 3081` 或杀占用进程 |
| 插件加载失败 | 版本不兼容或 plugin.json 格式错误 | 检查 DSH 版本，验证 plugin.json 结构 |
| 权限被拒绝 | 缺少沙箱权限或配置错误 | 检查 `~/.dsh/config.json` 的权限设置 |
| 模型路由不生效 | 缺少 Provider 配置 | 在 `--model_args` 中指定模型提供者 |
| 任务卡死或超时 | 循环未终止或工具执行错误 | 检查任务是否有明确的结束条件 |

### 设置自定义端口

```bash
dsh web --port 3090
# 访问 http://127.0.0.1:3090
```

### 配置文件位置

- `~/.dsh/config.json` - 主配置文件
- `~/.dsh/profiles/` - Profile 目录
- `~/.dsh/sessions/` - 会话目录

---

## 最佳实践建议

1. **先用 Web 工作台**：熟悉 DSH 界面和基本操作后，再尝试 CLI 与插件开发。
2. **使用 Profile 进行自定义**：通过创建自定义 Profile，在不修改核心的情况下组合所需插件。
3. **利用 Patch 机制**：对于常用的微调，使用 patch 而非 Fork 核心代码。
4. **关注社区生态**：DeepSeek Harness 社区已有众多插件，优先检查现有插件是否满足需求。
5. **配合 MCP 使用**：MCP (Model-Controller-Protocol) 集成可显著扩展 DSH 的自动化能力。
6. **保持插件轻量**：插件应专注于单一职责，避免过于臃肿，便于维护和复用。

---

## 参考链接

- [DeepSeek Harness 官方文档](https://deepseekdocs.com/docs/learn/intro/what-is-dsh)
- [DeepSeek Harness GitHub 仓库](https://github.com/deepseek-ai/deepseek-harness)
- [Cordis 框架文档](https://github.com/deepseek-ai/cordis)
- [DSH Community 教程站](https://deepseekdocs.com)
- [DSH v0.1.5-alpha.1 发布说明](https://github.com/deepseek-ai/deepseek-harness/releases/tag/v0.1.5-alpha.1)

---

**编者注**：本文基于 DeepSeek Harness 官方文档（v0.1.5-alpha.1）整理。DSH 目前处于 Developer Preview 阶段，API 和功能可能在后续版本中发生变化。建议关注官方仓库以获取最新动态。

---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/deepseek-harness-guide/  

