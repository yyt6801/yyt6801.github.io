# Visual Studio 配置与常用设置


## 安装与工作负载

### 版本选择
- Community（免费，个人/小型团队）
- Professional（中型团队）
- Enterprise（大型企业，功能最全）

### 常用工作负载（Workloads）
- **ASP.NET 与 Web 开发**：Web 应用、API 开发
- **使用 C&#43;&#43; 的桌面开发**：Win32、MFC、Qt 项目
- **NET 桌面开发**：WinForms、WPF 应用
- **Python 开发**：Python 项目支持
- **使用 .NET 的移动开发**：Xamarin / MAUI
- **数据存储与处理**：SQL Server、Azure Data

## 常用设置

### 环境设置（Tools &gt; Options）
- **字体与颜色**：Consolas 12pt、自定义主题配色
- **键盘映射**：可选 VS Code / ReSharper 方案
- **启动行为**：启动时打开上次加载的解决方案
- **自动保存**：Tools &gt; Options &gt; Environment &gt; AutoRecover
- **行号显示**：Text Editor &gt; All Languages &gt; General &gt; Line numbers

### 项目默认设置
- 默认命名空间规则
- 生成事件（Pre-build / Post-build）
- 目标框架版本管理

## 必备扩展

| 扩展名 | 用途 |
|--------|------|
| **ReSharper** | 代码分析、重构、导航（付费） |
| **GitHub Extension** | 内联 PR 管理 |
| **CodeMaid** | 代码清理与格式化 |
| **Trailing Whitespace Visualizer** | 显示尾部空格 |
| **VS Color Output** | 输出窗口着色 |
| **Markdown Editor** | 编辑 .md 文件 |
| **SonarLint** | 代码质量实时检测 |

## 调试配置

### 常用调试窗口
- 即时窗口（Immediate Window）：Ctrl&#43;Alt&#43;I
- 自动窗口（Autos）：调试时自动显示变量
- 监视窗口（Watch）：手动添加表达式
- 调用堆栈（Call Stack）：Ctrl&#43;Alt&#43;C
- 异常设置：Debug &gt; Windows &gt; Exception Settings

### 调试快捷键
- F5：开始调试
- Ctrl&#43;F5：不调试运行
- F9：切换断点
- F10：逐过程（Step Over）
- F11：逐语句（Step Into）
- Shift&#43;F11：跳出（Step Out）
- Ctrl&#43;Shift&#43;F5：重启调试

## 快捷键速查

### 编辑
| 快捷键 | 功能 |
|--------|------|
| Ctrl&#43;Space | 触发智能提示 |
| Ctrl&#43;K&#43;C | 注释选中行 |
| Ctrl&#43;K&#43;U | 取消注释 |
| Ctrl&#43;R&#43;R | 重命名 |
| Ctrl&#43;K&#43;D | 格式化文档 |
| Ctrl&#43;M&#43;M | 折叠/展开代码块 |
| Ctrl&#43;Shift&#43;F | 在文件中查找 |
| Ctrl&#43;H | 快速替换 |

### 导航
| 快捷键 | 功能 |
|--------|------|
| F12 | 转到定义 |
| Ctrl&#43;F12 | 转到实现 |
| Ctrl&#43;Tab | 切换文件 |
| Ctrl&#43;Shift&#43;S | 全部保存 |
| Ctrl&#43;` | 打开终端 |

## 项目配置

### .csproj 常用配置项
```xml
&lt;PropertyGroup&gt;
  &lt;TargetFramework&gt;net8.0&lt;/TargetFramework&gt;
  &lt;Nullable&gt;enable&lt;/Nullable&gt;
  &lt;ImplicitUsings&gt;enable&lt;/ImplicitUsings&gt;
&lt;/PropertyGroup&gt;
```

### 生成配置
- Debug：关闭优化，包含调试符号
- Release：开启优化，无调试符号
- 条件编译符号（DEBUG、TRACE 等）
- 平台目标（AnyCPU、x86、x64、ARM64）

## NuGet 包管理
- 浏览、安装、更新包
- 配置包源（官方源 / 私有源）
- `packages.config` vs `PackageReference`
- 全局包缓存路径管理

## 性能优化
- 禁用不必要的扩展
- 关闭&#34;根据输入搜索&#34;功能
- 启用&#34;轻量级解决方案加载&#34;
- 清理生成缓存（删除 bin/obj）
- 使用 .NET 6&#43; 的热重载加速开发


---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/visualstudio-configuration/  

