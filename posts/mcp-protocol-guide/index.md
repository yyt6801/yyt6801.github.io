# MCP 协议入门 —— AI 与工具生态的新连接标准


## MCP 是什么

MCP(Model Context Protocol,模型上下文协议)是 Anthropic 于 2024 年底开源的一项协议，现已成为 AI 应用连接外部工具与数据的事实标准。简单来说,它定义了一套**统一的接口规范**,让 LLM 应用能够以标准化的方式访问文件系统、数据库、API、Web 服务等外部资源。

在没有 MCP 之前,每个 AI 应用接入每种工具都需要单独编写适配代码,存在典型的&#34;M×N 集成问题&#34;:

- **M 个 AI 应用** × **N 种工具** = M×N 份集成代码
- 每新增一个应用或工具,集成本就翻倍增长

引入 MCP 后,架构从&#34;多对多&#34;退化为&#34;多对一&#34;:

- **M 个 AI 应用** &#43; **N 种工具** = 开发 M&#43;N 个符合 MCP 的实现即可

这和 USB-C 接口的理念非常相似:过去每种设备各有各的充电口,统一之后,一个接口连接所有设备。

## 核心概念

### 架构角色

```
┌────────────────┐      ┌──────────────────┐      ┌───────────────┐
│  Host(宿主)   │      │  MCP Client      │      │  MCP Server   │
│  如 OpenCode、 │ ───▶ │  连接管理,一个   │ ───▶ │  封装具体能力, │
│  Claude Desktop│      │  Client 对应一个 │      │  如 GitHub、   │
│                │      │  Server          │      │  数据库        │
└────────────────┘      └──────────────────┘      └───────────────┘
```

- **Host**:运行 AI 应用的一方(如 OpenCode、Claude Desktop、各类 IDE 插件)
- **Client**:Host 内部的连接器,每个 Client 与一个 Server 保持 1:1 会话
- **Server**:轻量级服务进程,向 AI 暴露某种具体能力,例如操作 Git、查询 PostgreSQL、抓取网页

### 三大原语

MCP Server 可以向 Host 暴露三类能力,称为&#34;原语&#34;(primitives):

| 原语 | 方向 | 说明 | 典型例子 |
|------|------|------|----------|
| **Tools** | 模型可控 | 由 LLM 决定何时调用,类似函数调用 | 执行 SQL、创建 Issue |
| **Resources** | 应用可控 | 由 Host/用户决定何时读取的上下文数据 | 代码文件、数据库模式 |
| **Prompts** | 用户可控 | 由用户主动触发的预制提示词模板 | &#34;/review&#34; 发起代码评审 |

把 Tools 理解为&#34;AI 能做什么&#34;,Resources 理解为&#34;AI 能看到什么&#34;,Prompts 理解为&#34;AI 能被指示怎么做&#34;,这个协议一下子就好记了。

&gt; 💡 另外还有 Sampling(服务端反向请求 LLM 补全)和 Roots(限定可访问目录)等高级能力,初学阶段可以暂不关注。

## 传输方式

Client 与 Server 之间的通信支持两种标准传输:

| 传输 | 适用场景 | 说明 |
|------|----------|------|
| **stdio** | 本地部署 | Server 作为子进程启动,通过标准输入/输出通信,实现最简单,适合本地工具 |
| **Streamable HTTP** | 远程/共享服务 | 基于 HTTP &#43; SSE 的双向流式通信,适合团队共享的远程 MCP 服务 |

本地个人使用选 stdio 即可;想让团队所有人共用同一个 MCP 服务(比如内部知识库),就走 Streamable HTTP。

## 快速上手:写一个最小的 MCP Server

以官方 Python SDK 为例,10 行核心代码就能对外暴露一个工具:

```Bash
pip install &#34;mcp[cli]&#34;
```

创建 `server.py`:

```Python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP(&#34;my-first-server&#34;)

@mcp.tool()
def add(a: int, b: int) -&gt; int:
    &#34;&#34;&#34;两数相加&#34;&#34;&#34;
    return a &#43; b

if __name__ == &#34;__main__&#34;:
    mcp.run()  # 默认以 stdio 传输运行
```

运行验证:

```Bash
mcp dev server.py   # 启动官方 Inspector 调试界面
```

浏览器会打开 Inspector,可以看到刚才注册的 `add` 工具,并直接调用测试。所有带类型签名的函数、docstring 都会自动转换成符合规范的 Tool 定义——**Type hints 就是文档**,这是 Python SDK 最大的优点。

TypeScript 生态同样成熟:

```Bash
npm install @modelcontextprotocol/sdk
```

## 在 OpenCode 中使用 MCP

关于 OpenCode 本身,可以参考我之前的文章[《OpenCode —— 开源 AI Coding Agent 介绍与操作指南》](/posts/opencode-intro-guide/)。

OpenCode 原生支持 MCP,一条命令即可接入.stdio 方式:

```Bash
opencode mcp add github -- claude mcp run @modelcontextprotocol/server-github
```

远程 HTTP 方式:

```Bash
opencode mcp add --transport http docs https://docs.example.com/mcp
```

配置保存在项目的 `opencode.json` 中,可以随项目提交,团队成员拉取后即获得相同的工具集。

## 常见 MCP 服务器生态

经过两年多的发展,MCP 已经积累了相当成熟的生态,官方维护的就有一批高可用 Server:

| Server | 能力 | 适合谁 |
|--------|------|--------|
| **Filesystem** | 受控的本地文件读写 | 所有人 |
| **GitHub** | Issue/PR/仓库管理 | 团队与开源维护者 |
| **Playwright** | 浏览器自动化与 E2E 测试 | 测试工程师 |
| **PostgreSQL / SQLite** | 只读 SQL 查询 | 数据分析 |
| **Fetch** | 网抓内容转 Markdown | 所有需要联网资料的场景 |
| **Slack** | 消息检索与发送 | 办公协作 |

在 [MCP Servers 目录](https://github.com/modelcontextprotocol/servers) 和 [mcp.so](https://mcp.so) 可以找到更多社区实现的 Server。

## 安全与实践建议

把工具交到 AI 手上,安全性必须放在首位:

1. **最小权限原则** — 如 Filesystem Server 支持指定允许访问的目录白名单,不要把整个磁盘交给 AI
2. **慎用凭证** — GitHub 等需要 Token 的 Server,用最小 scope 的 PAT,并按需设置只读
3. **警惕提示注入** — AI 可能读取到恶意网页内容后调用你的本地工具,敏感操作(删除、付款等)工具设计上要加确认门槛
4. **供应链审查** — 第三方 MCP Server 部署前审查其源码或发布来源,优先选择官方与高星数实现
5. **企业内部用只读(Server)起步** — 团队落地 MCP 的稳妥路径:先做到&#34;AI 只能读&#34;,验证价值后再逐步开放写能力

## 小结

- MCP 用统一的协议标准把 **AI 应用 ↔ 工具** 的集成从 M×N 降到 M&#43;N
- 三大原语:**Tools**(AI 能做)、**Resources**(AI 能看)、**Prompts**(AI 能被指点)
- 本地 stdio、远程 Streamable HTTP 两种传输,按场景选型
- OpenCode 等主流 AI 编程工具已原生支持,一条命令接入即可

工具生态的标准化,正在让&#34;AI 会用工具&#34;从演示变成生产力。如果你也用 AI 辅助开发,值得花半小时动手把 MCP 跑起来。

---

**参考链接**

- [MCP 官方文档](https://modelcontextprotocol.io)
- [MCP Python SDK](https://github.com/modelcontextprotocol/python-sdk)
- [Server 目录](https://github.com/modelcontextprotocol/servers)


---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/mcp-protocol-guide/  

