# DBeaver 完全使用指南：从入门到离线环境驱动安装


## DBeaver 简介

[DBeaver](https://dbeaver.io/) 是一款免费开源的通用数据库管理工具，支持所有主流数据库（MySQL、PostgreSQL、Oracle、SQL Server、SQLite、MongoDB、Redis 等），基于 Eclipse 平台开发，跨平台运行（Windows、macOS、Linux）。

**核心优势**：
- **免费开源**：社区版完全免费，企业版提供额外功能（NoSQL 支持、数据对比、ER 图等）
- **通用性强**：一款工具连接所有数据库，无需安装多个客户端
- **功能丰富**：SQL 编辑器、数据导入导出、ER 图、数据对比、脚本执行、任务调度等
- **插件生态**：基于 Eclipse 插件架构，可扩展功能

---

## 安装与基础配置

### 1. 下载安装

| 平台 | 下载方式 |
|------|----------|
| Windows | [官网下载](https://dbeaver.io/download/) `.exe` 安装包或便携版 |
| macOS | `.dmg` 安装包 或 `brew install --cask dbeaver-community` |
| Linux | `.deb`/`.rpm` 包、Flatpak、Snap 或解压运行 |

**推荐**：下载 **Community Edition**（社区版），功能已覆盖 95% 日常需求。

### 2. 首次启动配置

启动后会引导完成基础设置：
- **工作空间路径**：建议放在非系统盘（如 `D:\Workspace\DBeaver`）
- **驱动下载目录**：默认 `~/.dbeaver-drivers`，可自定义
- **主题选择**：Dark/Light/System
- **语言**：自动检测系统语言，支持中文

### 3. 核心界面介绍

```
┌─────────────────────────────────────────────────────────────┐
│ 菜单栏：文件、编辑、视图、导航、窗口、帮助                   │
├─────────────┬───────────────────────────────────────────────┤
│ 数据库导航器 │  编辑器区域：SQL 编辑器、数据查看器、ER 图等    │
│ (左侧)       │  (右侧，支持多标签页)                          │
├─────────────┼───────────────────────────────────────────────┤
│ 属性/输出/   │  底部面板：日志、结果集、脚本输出、任务进度      │
│ 书签面板    │                                              │
└─────────────┴───────────────────────────────────────────────┘
```

---

## 数据库连接配置

### 1. 创建新连接

**方式一**：工具栏 `新建连接` 图标（插头形状）或 `Ctrl&#43;Shift&#43;N`  
**方式二**：右键数据库导航器空白处 → `新建` → `数据库连接`

### 2. 常用数据库连接参数模板

| 数据库 | 主机/端口 | 数据库名 | 用户名 | 驱动类型 | 备注 |
|--------|-----------|----------|--------|----------|------|
| MySQL | `localhost:3306` | `mydb` | `root` | MySQL 8.0&#43; | 需开启 SSL 选项 |
| PostgreSQL | `localhost:5432` | `postgres` | `postgres` | PostgreSQL | 支持 SSL 模式 |
| Oracle | `localhost:1521` | `ORCL` / `XEPDB1` | `system` | Oracle Thin | 需配置 TNS 或服务名 |
| SQL Server | `localhost:1433` | `master` | `sa` | SQL Server (jTDS) | 或 Microsoft 驱动 |
| SQLite | 文件路径 | - | - | SQLite | 直接选文件即可 |
| MongoDB | `localhost:27017` | `admin` | `root` | MongoDB | 支持副本集/分片 |
| Redis | `localhost:6379` | - | 密码 | Redis | 企业版功能 |

### 3. 连接进阶设置

点击连接向导中的 **`驱动程序属性`** 标签页，可配置：

- **连接属性**：`autoReconnect=true`、`useSSL=false`、`serverTimezone=Asia/Shanghai`
- **SSH 隧道**：跳板机连接内网数据库（`SSH` 标签页）
- **SSL 证书**：上传 `.pem`/`.jks` 证书文件
- **初始化脚本**：连接建立后自动执行的 SQL

### 4. 连接分组与颜色标记

- 右键连接 → `编辑连接` → `常规` → `分组`：按环境分组（dev/test/prod）
- 右键连接 → `颜色设置`：为生产环境设置红色边框，防误操作

---

## 核心功能详解

### 1. SQL 编辑器

**快捷键**（可在 `Window → Preferences → General → Keys` 自定义）：

| 功能 | Windows/Linux | macOS |
|------|---------------|-------|
| 执行当前语句 | `Ctrl&#43;Enter` | `Cmd&#43;Enter` |
| 执行选中/全部 | `Ctrl&#43;Shift&#43;Enter` | `Cmd&#43;Shift&#43;Enter` |
| 格式化 SQL | `Ctrl&#43;Shift&#43;F` | `Cmd&#43;Shift&#43;F` |
| 大小写转换 | `Ctrl&#43;Shift&#43;U/L` | `Cmd&#43;Shift&#43;U/L` |
| 注释/取消注释 | `Ctrl&#43;/` | `Cmd&#43;/` |
| 自动补全 | `Ctrl&#43;Space` | `Ctrl&#43;Space` |

**实用技巧**：
- **变量替换**：`${variable}` 语法，执行时弹窗输入
- **脚本分隔符**：`@set delimiter = ;` 支持存储过程
- **结果集模式**：网格/文本/记录/透视表 切换

### 2. 数据导入导出

**导出**：右键表/结果集 → `导出数据` → 支持 CSV、JSON、XML、SQL INSERT、Excel、HTML、Markdown  
**导入**：右键表 → `导入数据` → 字段映射、类型转换、批量提交大小配置

&gt; **大数据量技巧**：导入时调大 `批量大小`（如 5000）、关闭索引/约束、使用事务模式

### 3. ER 图生成

右键数据库/Schema/表 → `查看 ER 图`：
- 支持表关系自动识别（外键）
- 可拖拽布局、缩放、导出 PNG/SVG
- 支持 `显示列详情`、`仅显示主键` 等视图模式

### 4. 数据对比与同步

`工具 → 数据对比` 或右键两张表 → `比较数据`：
- 对比结构（DDL）或数据（行级差异）
- 生成同步脚本（INSERT/UPDATE/DELETE）
- 支持同步执行或导出 SQL 文件

### 5. 任务调度与脚本

`窗口 → 显示视图 → 任务管理器`：
- 定时执行 SQL 脚本（Cron 表达式）
- 导入导出任务自动化
- 邮件通知、日志记录

---

## 重点：离线环境安装数据库驱动

**场景**：生产环境、隔离网络、无法访问互联网下载驱动。

### 1. 理解 DBeaver 驱动机制

- 驱动本质是 **JDBC 驱动 JAR 包** &#43; 元数据配置
- DBeaver 首次连接时会自动从 Maven Central / GitHub 下载
- 离线环境需 **手动放置 JAR 包到驱动目录**

### 2. 找到驱动存放目录

| 方式 | 路径 |
|------|------|
| 默认目录 | `~/.dbeaver-drivers` (Linux/macOS) / `%USERPROFILE%\.dbeaver-drivers` (Windows) |
| 自定义目录 | `Window → Preferences → Drivers → Driver files folder` |
| 便携版目录 | `&lt;DBeaver安装目录&gt;\.metadata\.dbeaver-drivers` |

&gt; **建议**：在有网环境预先配置好自定义目录（如 `D:\DBeaverDrivers`），便于迁移。

### 3. 手动获取驱动 JAR 包

**方法一：从有网机器复制**
1. 有网机器上用 DBeaver 正常连接一次目标数据库
2. 复制其驱动目录下对应数据库文件夹（如 `maven/maven-central/com/mysql/mysql-connector-j/8.2.0/`）
3. 粘贴到离线机器同目录下

**方法二：直接下载 JDBC JAR（推荐）**

| 数据库 | Maven 坐标 | 官方下载页 |
|--------|------------|------------|
| **MySQL** | `com.mysql:mysql-connector-j:8.3.0` | [MySQL Connector/J](https://dev.mysql.com/downloads/connector/j/) |
| **PostgreSQL** | `org.postgresql:postgresql:42.7.3` | [PostgreSQL JDBC](https://jdbc.postgresql.org/download.html) |
| **Oracle** | `com.oracle.database.jdbc:ojdbc11:23.4.0.0` | [Oracle JDBC](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) |
| **SQL Server** | `com.microsoft.sqlserver:mssql-jdbc:12.4.2.jre11` | [Microsoft JDBC](https://docs.microsoft.com/en-us/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server) |
| **SQLite** | `org.xerial:sqlite-jdbc:3.46.0.0` | [SQLite JDBC](https://github.com/xerial/sqlite-jdbc/releases) |
| **MongoDB** | `org.mongodb:mongo-java-driver:3.12.14` | [MongoDB Java Driver](https://mongodb.github.io/mongo-java-driver/) |
| **Redis** | `redis.clients:jedis:5.1.0` | [Jedis](https://github.com/redis/jedis/releases) |
| **ClickHouse** | `com.clickhouse:clickhouse-jdbc:0.4.6` | [ClickHouse JDBC](https://github.com/ClickHouse/clickhouse-jdbc) |
| **TDengine** | `com.taosdata.jdbc:taos-jdbcdriver:3.0.5` | [TDengine JDBC](https://docs.tdengine.com/taos-tools/taos-jdbcdriver/) |

&gt; **注意**：Oracle 驱动需区分 JDK 版本（ojdbc8/ojdbc11/ojdbc17），下载与运行环境 JDK 匹配的版本。

### 4. 离线安装步骤（以 MySQL 为例）

#### 步骤 1：下载 JAR 包
在有网机器访问 Maven Central 或官网，下载：
```
mysql-connector-j-8.3.0.jar
```
也可下载完整依赖包（含传递依赖），避免缺少依赖报错。

#### 步骤 2：放置到驱动目录
在离线机器的驱动目录下创建对应结构：
```
.dbeaver-drivers/
└── maven/
    └── maven-central/
        └── com/
            └── mysql/
                └── mysql-connector-j/
                    └── 8.3.0/
                        ├── mysql-connector-j-8.3.0.jar
                        └── mysql-connector-j-8.3.0.pom  (可选，用于依赖解析)
```

#### 步骤 3：在 DBeaver 中注册驱动
1. 重启 DBeaver
2. `数据库 → 驱动程序管理器` (或 `Ctrl&#43;Alt&#43;D`)
3. 找到 MySQL 驱动，若显示为灰色不可用，右键 → `编辑`
4. **库** 标签页 → `添加文件` → 选中刚才放置的 `.jar`
5. **设置** 标签页 → 检查 `类名`：`com.mysql.cj.jdbc.Driver`
6. 点击 `测试连接` 验证

#### 步骤 4：创建连接测试
新建 MySQL 连接，填入主机/端口/用户名/密码，点击 `测试连接` → 成功即完成离线安装。

### 5. 批量离线安装脚本（Windows PowerShell）

```powershell
# 配置参数
$driverDir = &#34;$env:USERPROFILE\.dbeaver-drivers\maven\maven-central&#34;
$drivers = @(
    @{ name=&#34;mysql&#34;; group=&#34;com/mysql/mysql-connector-j&#34;; version=&#34;8.3.0&#34;; url=&#34;https://repo1.maven.org/maven2/com/mysql/mysql-connector-j/8.3.0/mysql-connector-j-8.3.0.jar&#34; }
    @{ name=&#34;postgresql&#34;; group=&#34;org/postgresql/postgresql&#34;; version=&#34;42.7.3&#34;; url=&#34;https://repo1.maven.org/maven2/org/postgresql/postgresql/42.7.3/postgresql-42.7.3.jar&#34; }
    @{ name=&#34;sqlite&#34;; group=&#34;org/xerial/sqlite-jdbc&#34;; version=&#34;3.46.0.0&#34;; url=&#34;https://repo1.maven.org/maven2/org/xerial/sqlite-jdbc/3.46.0.0/sqlite-jdbc-3.46.0.0.jar&#34; }
)

foreach ($d in $drivers) {
    $targetDir = &#34;$driverDir/$($d.group)/$($d.version)&#34;
    if (-not (Test-Path $targetDir)) { New-Item -ItemType Directory -Path $targetDir -Force | Out-Null }
    $jarName = &#34;$($d.name)-$($d.version).jar&#34;
    $targetFile = &#34;$targetDir\$jarName&#34;
    if (-not (Test-Path $targetFile)) {
        Write-Host &#34;Downloading $jarName...&#34;
        try { Invoke-WebRequest -Uri $d.url -OutFile $targetFile -UseBasicParsing } catch { Write-Warning &#34;Download failed: $($d.url)&#34; }
    }
}
Write-Host &#34;Done. Restart DBeaver to load drivers.&#34;
```

&gt; **Linux/macOS** 可用 `wget`/`curl` 实现类似逻辑。

### 6. 常见离线安装问题排查

| 现象 | 原因 | 解决方案 |
|------|------|----------|
| 驱动列表显示灰色/不可选 | JAR 未放入正确目录或版本号不匹配 | 检查目录结构、文件名、重启 DBeaver |
| `ClassNotFoundException` | 缺少传递依赖（如 `protobuf-java`、`slf4j`） | 下载完整依赖树，或使用 Maven `dependency:copy-dependencies` |
| `UnsupportedClassVersionError` | JDK 版本不匹配（驱动编译版本 &gt; 运行版本） | 下载兼容当前 JDK 版本的驱动，或升级 JDK |
| 连接超时/拒绝 | 网络不通、防火墙、数据库配置 | 排查网络、防火墙、数据库 `bind_address`、`pg_hba.conf` |
| Oracle 连接报 `IO Error: The Network Adapter could not establish the connection` | 缺少 `ojdbc` 对应版本、或未配置 `oracle.net.tns_admin` | 确认 ojdbc 版本匹配 JDK，配置 TNS_ADMIN 环境变量 |

---

## 进阶技巧与最佳实践

### 1. 连接会话保持与自动重连

- **驱动属性** 添加：`autoReconnect=true`、`maxReconnects=3`、`initialTimeout=2`
- **SSH 隧道** 配置心跳：`ServerAliveInterval=60`、`ServerAliveCountMax=3`

### 2. 多环境配置管理

**方案**：导出/导入连接配置
- `文件 → 导出连接` → 保存为 `.dbeaver-data-sources.xml`
- 团队共享：放入版本控制（脱敏密码），新成员导入即用
- **密码存储**：`Window → Preferences → Security → Master Password` 设置主密码加密保存

### 3. 自定义 SQL 模板

`Window → Preferences → Editors → SQL Templates`：
```sql
-- 模板名称: sel
SELECT * FROM ${table_name} WHERE ${cursor};
-- 使用：输入 sel &#43; Ctrl&#43;Space → 选择模板 → 自动补全表名
```

### 4. 数据脱敏与导出限制

- **结果集行数限制**：`Preferences → Editors → Result Sets → Max rows`（默认 200，防止误查大表）
- **导出脱敏**：导出向导中勾选 `掩码敏感列`，配置正则替换规则

### 5. 性能优化建议

| 场景 | 优化建议 |
|------|----------|
| 大表浏览 | 关闭 `自动获取行数`、使用 `分页模式`、仅选取所需列 |
| 复杂查询 | 使用 `EXPLAIN` 分析执行计划（右键 SQL → `执行计划`） |
| 批量操作 | 调大 `Fetch size`（如 1000）、使用 `批量提交` 模式 |
| 内存不足 | 增加 JVM 堆内存：编辑 `dbeaver.ini` 添加 `-Xmx2g` |

---

## 常见问题速查表

| 问题 | 快速解决 |
|------|----------|
| 忘记主密码 | 删除 `.dbeaver/.metadata/.plugins/org.eclipse.core.runtime/.settings/org.eclipse.equinox.security.ui.prefs` 重置 |
| 连接突然断开 | 检查数据库 `wait_timeout`/`interactive_timeout`、驱动 `autoReconnect`、网络防火墙 |
| 导出报 `OutOfMemoryError` | 增加 `-Xmx`、分批导出、使用流式导出模式 |
| ER 图显示不全 | 右键画布 → `自动布局`、调整 `显示深度`、检查外键是否存在 |
| 无法看到系统表 | `数据库导航器 → 过滤器` → 取消勾选 `隐藏系统对象` |
| 时区不一致 | 连接属性添加 `serverTimezone=Asia/Shanghai`、或 JVM 参数 `-Duser.timezone=Asia/Shanghai` |

---

## 结语

DBeaver 作为「数据库界的瑞士军刀」，掌握其核心功能与离线驱动安装技巧，能极大提升数据库开发运维效率。本文重点覆盖了：

1. **全平台安装与基础配置**
2. **主流数据库连接参数模板**
3. **SQL 编辑、导入导出、ER 图、数据对比等核心功能**
4. **离线环境驱动安装完整方案**（手动下载、目录结构、注册验证、批量脚本、问题排查）
5. **进阶技巧**（会话保持、多环境管理、模板、性能优化）

建议收藏本文作为速查手册，遇到具体问题可直接对照排查。如有补充或纠错，欢迎在评论区交流。

---

**参考链接**

- [DBeaver 官方文档](https://dbeaver.io/docs/)
- [DBeaver GitHub](https://github.com/dbeaver/dbeaver)
- [JDBC 驱动下载汇总](https://mvnrepository.com/)
- [Maven Central Repository](https://repo1.maven.org/maven2/)
- [各数据库官方 JDBC 下载页](https://dev.mysql.com/downloads/connector/j/) | [PostgreSQL](https://jdbc.postgresql.org/download.html) | [Oracle](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) | [SQL Server](https://docs.microsoft.com/en-us/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)

---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/dbeaver-complete-guide/  

