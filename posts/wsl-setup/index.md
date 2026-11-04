# WSL开发环境配置与进阶实践


## 什么是 WSL 以及为什么使用它

WSL（Windows Subsystem for Linux）是微软在 Windows 10/11 中内置的兼容层，允许你在 Windows 桌面环境下直接运行 Linux 命令行工具和应用程序，**无需安装双系统或虚拟机**。它极大地降低了 Windows 用户接触 Linux 的门槛，特别适合：

- **前端/后端开发**：需要 Linux 环境运行 Node、Python、Ruby 等工具链
- **运维与运行脚本**：使用 Bash、Python 进行自动化任务
- **学习 Linux**：在熟悉的 Windows 上尝试 Linux 命令和配置
- **Docker 容器开发**：WSL 2 原生支持 Docker Engine

WSL 分为两个主要版本，选择至关重要：

| 特性 | **WSL 1** | **WSL 2** |
|------|-----------|-----------|
| **内核** | 翻译层，将 Linux 系统调用转换为 Windows 系统调用 | 包含真实的 Linux 内核，运行在轻量级虚拟机中 |
| **文件系统性能** | 读写速度快，但不支持某些 Linux 文件属性 | 性能稍低，但兼容性更好，支持完整文件系统特性 |
| **兼容性** | 不支持某些内核模块、Docker 等需要完整内核的场景 | 支持几乎所有 Linux 软件，包括 Docker、systemd（新版） |
| **启动速度** | 极快（直接进程） | 较快（虚拟机启动，但仍低于完整 VM） |
| **推荐使用场景** | 简单的命令行工具、脚本运行 | 日常开发、Docker、图形界面应用、需要完整 Linux 体验的场景 |

**结论**：本文及当前工业界默认以 **WSL 2** 为基础，因为它已成为官方推荐且兼容性更强的选择。

---

## 安装 WSL (Windows 10 / 11)

### 第一步：启用 WSL 功能与虚拟机平台

以**管理员身份**打开 PowerShell，依次执行：

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

**重启电脑**以完成安装程序的安装。

### 第二步：安装 Linux 发行版

打开 **Microsoft Store**，搜索并安装你喜欢的发行版：

- **推荐**：`Ubuntu 22.04 LTS`（长期支持、社区强、教程最多）
- 替代方案：`Debian`, `Kali Linux`, `openSUSE`

点击 &#34;获取&#34; 安装，安装完成后会在开始菜单出现对应的应用图标。

### 第三步：设置默认 WSL 版本

大多数新安装的发行版已经是 WSL 2。如需确认或手动指定：

```powershell
# 查看所有已安装发行版及其版本
wsl --list --verbose

# 将指定发行版升级到 WSL 2（将 &lt;发行版名&gt; 替换为实际名称，如 Ubuntu）
wsl --set-version Ubuntu-22.04 2

# 设置未来新安装的默认版本为 WSL 2
wsl --set-default-version 2
```

### 第四步：更新 Linux 内核（WSL2 必需）

WSL 2 依赖微软提供的 Linux 内核更新包，建议定期运行：

```powershell
wsl --update
```

如需手动检查当前内核版本：

```powershell
wsl -l -v
```

---

## 基础环境配置

### 1. 配置 apt 源（提高下载速度）

默认的 Ubuntu 源在国内较慢，建议换源。以阿里云源为例：

```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

# 备份并替换为阿里云源
sudo tee /etc/apt/sources.list &lt;&lt;-&#39;EOF&#39;
deb http://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
EOF

# 更新并升级
sudo apt update &amp;&amp; sudo apt upgrade -y
```

### 2. 配置 Zsh &#43; Oh My Zsh（增强终端体验）

```bash
# 安装 zsh
sudo apt install -y zsh

# 设置为默认 Shell
chsh -s $(which zsh)

# 安装 Oh My Zsh（一键安装，会自动下载配置）
sh -c &#34;$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)&#34;

# 常用主题与插件
# 编辑 ~/.zshrc，修改 ZSH_THEME=&#34;agnoster&#34; 或其他喜好
# 插件插件：git, zsh-autosuggestions, zsh-syntax-highlighting
```

### 3. 常用开发工具链安装

```bash
# 安装基础编译工具和 Python
sudo apt install -y git curl wget vim build-essential python3-pip

# 国内常用镜像加速 pip
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

# 安装常用 Python 包
pip install numpy requests pandas matplotlib
```

### 4. VS Code WSL 远程开发（强烈推荐）

1. 在 Windows 上安装 [Visual Studio Code](https://code.visualstudio.com/)。
2. 在 VS Code 中安装扩展 **“Remote - WSL”**。
3. 打开 VS Code，底部状态栏会出现一个小图标，点击后即可连接到 WSL 环境。
4. 所有在 WSL 中的操作（安装包、编辑文件、运行终端）都将在 VS Code 的界面下完成，**文件直接同步**到 Windows 资源管理器。

&gt; **优势**：在 Windows 界面里获得 Linux 开发体验，代码文件的读写性能接近原生 Linux，且可直接使用 Windows 端的插件（如 Docker、GitLens 等）。

---

## 与 Windows 的文件系统互操作

### 路径映射概览

| 场景 | Windows 路径 | Linux 路径 | 说明 |
|------|-------------|-----------|------|
| **Windows 访问 Linux** | `\\wsl$\Ubuntu\home\&lt;用户名&gt;\` | `~` (home 目录) | 在资源管理器地址栏直接粘贴访问 |
| **Linux 访问 Windows** | `C:\Users\&lt;用户名&gt;\` | `/mnt/c/Users/&lt;用户名&gt;/` | 自动挂载，直接 `cd /mnt/c/...` |
| **D 盘、E 盘** | `D:\`, `E:\` | `/mnt/d/`, `/mnt/e/` | 类似 C 盘，建议将项目放在 Linux 原生文件系统 |

### 最佳实践

- **源代码放在 Linux 原生路径**：`~/project/` 或 `~/dev/`，可获得最佳 I/O 性能。
- **文本编辑、Git 操作**均在 Linux 侧进行。
- **仅在需要跨平台共享**（如使用 Windows 专有软件打开、与非技术人员共享）时，才将文件放在 `/mnt/c/` 下。
- **注意文件权限**：在 Linux 侧新建文件后，如果在 Windows 侧打开可能提示“无法保存”，通常是权限问题，用 `chmod -R 755 ~/project` 可解决。

---

## systemd 与 Docker/WSL2 集成

### 1. 启用 systemd (WSL2 新版已支持)

从 WSL2 更新版本开始，官方已部分支持 systemd。按以下步骤开启：

```powershell
# 1. 确保已更新到最新的 WSL2 版本
wsl --update

# 2. 在 .wslconfig 中配置 (在 Windows %USERPROFILE% 下创建)
# 将以下内容写入 %USERPROFILE%\ .wslconfig
[wsl2]
systemd=true

# 3. 重启 WSL
wsl --shutdown
# 然后进入 Linux:
sudo systemctl enable &lt;service-name&gt;
```

&gt; **注意**：并非所有服务都能完美运行，常用的如 `ssh`, `nginx`, `cron` 通常可用。

### 2. Docker Desktop 与 WSL2 集成

1. 下载安装 **Docker Desktop for Windows**。
2. 打开 Docker Desktop -&gt; Settings -&gt; Resources -&gt; WSL Integration。
3. 勾选你要集成的 Linux 发行版（如 Ubuntu）。
4. 关闭并重启 Docker Desktop。

**验证是否成功**：

```bash
# 在 WSL 终端中
docker --version
docker run --rm hello-world
```

&gt; 此时不需要在 Linux 内单独安装 Docker 引擎，Docker Desktop 的后端已通过 WSL2 透明提供容器能力。

### 3. 常见问题与解决

| 问题 | 可能原因 | 解决方案 |
|------|----------|----------|
| `docker: command not found` | WSL Integration 未勾选或未重启 Docker | 按上述步骤勾选并 `wsl --shutdown` 后重试 |
| `Permission denied` 运行 Docker | 非 root 用户 | 将用户加入 docker 组：`sudo usermod -aG docker $USER`，随后 `logout` 并重新登录 WSL |
| `Cannot connect to the Docker daemon` | Docker 服务未运行 | 确认 Docker Desktop 已完全启动，且 WSL Integration 已开启 |

---

## 常见问题与排查指南

| 问题 | 常见原因 | 完整排查与解决 |
|------|----------|----------------|
| **`wsl` 命令找不到** | WSL 功能未启动、未重启系统 | 1. 以管理员身份运行 PowerShell&lt;br&gt;2. 执行 `dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart`&lt;br&gt;3. 重启电脑 |
| **WSL 启动极慢** | 虚拟机平台未开启、系统省电设置 | 1. 确认“虚拟机平台”功能已启用&lt;br&gt;2. 检查电源选项，将“睡眠”设置为“启用”&lt;br&gt;3. 运行 `wsl --shutdown` 完全关闭后重启 |
| **Linux 文件在 Windows 打开卡死/报错** | 文件权限、路径过长、实时杀毒干扰 | 1. 在 Linux 下 `chmod -R 755 ~/project`&lt;br&gt;2. 将项目路径移至 `~/` 或盘符根目录（如 `D:\dev`）&lt;br&gt;3. 暂时退出实时保护或将 WSL 进程加入白名单 |
| **无法运行 GUI 应用（如 VS Code、Xed）** | WSLg 未启动、缺少显示转发 | 1. Windows 11 自带 WSLg，无需额外配置&lt;br&gt;2. Windows 10 可使用 X Server（如 [VcXsrv](https://sourceforge.net/projects/vcxsrv/)）配合 `export DISPLAY=:0`&lt;br&gt;3. 部分应用直接 `code .` 或 `gedit` 在 WSLg 下即可运行 |
| **磁盘空间被占用过多** | WSL 自动挂载的 Windows 盘产生大量缓存、日志 | 1. 清理 `~/tmp`、`/var/log`&lt;br&gt;2. 使用 `wsl --terminate &lt;PID&gt;` 释放资源&lt;br&gt;3. 设置 `.wslconfig` 限制 内存和 交换空间大小 |

### `.wslconfig` 配置文件详解（性能微调）

在 Windows 的用户主目录（`%USERPROFILE%`）创建 `.wslconfig` 文件，可以对 WSL 2 进行精细化配置：

```ini
[wsl2]
memory=4GB        # 限制 WSL 可使用的系统内存，防止其占满物理内存
swap=2GB          # 设置 Swap 空间大小
localhost=net   # 网络模式，net 表示使用 NAT 网络，更好地与 Windows 网络共存
localhostPorts=localhost:localhost  # 端口映射，避免端口冲突
processes=0       # 限制同时运行的进程数，防止系统卡死
```

---

## 备份与迁移 WSL

### 1. 导出当前发行版

```bash
# 在 PowerShell (管理员) 中
wsl --export &lt;发行名&gt; &lt;输出文件名&gt;.tar
# 例如：
wsl --export Ubuntu-22.04 wsl-ubuntu2204.tar
```

### 2. 导入到另一台电脑或新发行版

```bash
# 在目标电脑上：
wsl --import &lt;新发行名&gt; &lt;安装路径&gt; &lt;.tar文件路径&gt;
# 例如：
wsl --import Debian-12 /home/user/debian12 wsl-debian12.tar
```

### 3. 迁移至另一块盘（如从 C 迁移到 D）

```bash
# 1. 导出
wsl --export Ubuntu-22.04 wsl-backup.tar

# 2. 停止 WSL
wsl --shutdown

# 3. 移动 %userprofile%\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu22.04onWindows 到目标盘

# 4. 重新导入
wsl --import Ubuntu-22.04 /home/user/ubuntu wsl-backup.tar
```

---

## 小结

WSL 已成为 Windows 开发者不可或缺的工具。通过 **WSL 2** 配合合理的源配置、Zsh 美化、VS Code 远程开发，你可以在 Windows 上获得接近原生 Linux 的开发体验。掌握文件系统互操作、systemd/Docker 集成以及常见问题的排查方法，将极大提升你的工作效率。

无论是日常脚本编写、后端开发，还是配合 Docker 进行容器化部署，WSL 都能提供轻量、高效且兼容性好的解决方案。随着 `.wslconfig` 的配置和最佳实践的落地，你可以进一步优化性能，根据需要在 Windows 与 Linux 之间无缝切换。

**参考链接**

- [Microsoft 官方 WSL 文档](https://learn.microsoft.com/zh-cn/windows/wsl/)
- [WSL 官方 GitHub 仓库](https://github.com/microsoft/WSL)
- [Docker Desktop WSL2 集成指南](https://docs.docker.com/desktop/features/wsl/)
- [VcXsrv X Server (Windows 10 GUI 支持)](https://sourceforge.net/projects/vcxsrv/)

---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/wsl-setup/  

