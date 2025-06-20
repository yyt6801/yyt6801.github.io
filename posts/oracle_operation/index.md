# Oracle操作维护手册


Oracle是一款关系型数据库管理系统，广泛应用于企业级应用中。本文档将介绍Oracle的基本操作和维护方法，帮助用户更好地管理和维护Oracle数据库。

### 1. Oracle 安装
Oracle的安装过程比较复杂，需要按照官方文档进行操作。以下是一些基本的安装步骤：

1. 下载Oracle安装程序
2. 解压安装程序
3. 运行安装程序
4. 按照提示进行安装
5. 配置Oracle数据库

#### 安装服务端
1. 安装选项选择“创建和配置数据库”；
2. 数据库系统选择”服务器类”；
3. 数据安装类型选择”单实例数据库安装”；
4. 选择”高级安装”；
5. 选择“企业版”；
6. 数据库类型选择“一般用途/事务处理”；
7. 全局数据库实例名为Orcl;
8. 对所有账户使用相同的管理口令；
9. 完成安装

#### 安装客户端
1. 安装类型中选择“定制”；
2. 选择需要安装的组件：
   *  Oracle Database Utilities
   *  Oracle Programmer
   *  Oracle Net 
   *  Oracle Connection Manager
   *  Oracle Net Listener
   *  Oracle ODBC Driver
   *  Oracle Objects for OLE
   *  Oracle Provider for OLE DB
3. 通过界面配置监听（不执行典型配置）；
4. 配置命名方法（服务名-&gt;网络服务名）；

#### 安装后配置
1. 关闭监听日志listener.log  
   管理员模式下进入DOS或PowerShell,依次执行：
   1. *`Lsnrctl`*
   2. *`set log_status off`*
   3. 手动删除 `D:\app\Administrator\diag\tnslsnr\Your_PC_Name\listener\trace`文件夹路径下的`listener.log`；
   4. *`save_config`*  保存配置  
2. 关闭账户默认180天到期
   1. 查看当前用户密码设置
      ```SQL
      select * from dba_profiles where profile=&#39;DEFAULT&#39; and resource_name=&#39;PASSWORD_LIFE_TIME&#39;;
      ```
   2. 设置密码不过期，可用管理员登陆
      ```SQL
      ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED;
      ```
3. 关闭审计功能
   1. 使用SQLPlus登录(sqlplus 用户名/密码@数据库实例名)
      ```Bash
      sqlplus system/password@Orcl as sysdba;
      ```
   2. 查看审计功能开启状态
      ```Bash
      show parameter audit_trail;
      ```
      输出结果DB为开启审计功能。NONE：未开启
   3. 关闭审计
      ```Bash
      alter system set audit_trail=none scope=spfile;
      ```
   4. 关闭数据库实例使配置生效
      ```Bash
      shutdown immediate;
      ```
   5. 确认状态
      在服务中重新启动OracleServiceOrcl服务;再次查看是否为NONE已关闭状态:
      ```Bash
      show parameter audit_trail;
      ```



### 2. Oracle 基本操作
Oracle的基本操作包括创建数据库、创建表、插入数据、查询数据等。以下是一些基本的操作方法：

* 创建数据库
   ```
   CREATE DATABASE mydb;
   ```

* 创建表
   ```
   CREATE TABLE mytable (id INT, name VARCHAR(50));
   ```
* 查询数据
   ```
   SELECT * FROM mytable;
   ```
* 插入数据  
   *`INSERT INTO 表名称 VALUES (值1, 值2,....)`*  
   或  
   *`INSERT INTO table_name (列1, 列2,...) VALUES (值1, 值2,....)`* 

   * 示例：
      ```
      INSERT INTO mytable (id, name) VALUES (1, &#39;John&#39;);
      ```
     
* 插入数据前先判断  
      *`INSERT when (not exists () then into table_name (列1, 列2,...) VALUES (值1, 值2,....)`*
   * 示例：
      ```  
      insert when ( not exists (select DEPT_ID from T_JY_WX_DEPT where DEPT_ID = ?) ) then into T_JY_WX_DEPT (DEPT_ID,DEPT_NAME,PROJECT_ALIAS,PARENT_ID) values(?,?,?,?) select ? from dual
      ```
* 条件查找字符串不为空的数据：使用 IS NOT NULL可以判断  
   * 示例：
      ```  
      select * from tb_cpcavg_stdev t where t.analyze_report IS NOT NULL
      ```
* 时间格式  
      *`time &lt;= to_date(&#39;2020-06-09&#39;, &#39;yyyy-mm-dd&#39;)`*  
      *`time &lt;= to_date(&#39;2020-06-09 10:00:00&#39;, &#39;yyyy-mm-dd hh24:mi:ss&#39;)`*  
      *`time BETWEEN to_date(&#39;2024-12-25&#39;, &#39;yyyy-mm-dd&#39;) AND to_date(&#39;2024-12-26&#39;, &#39;yyyy-mm-dd&#39;)`*
* oracle字符串截取、拼接  substr &#43;  concat  
   *`select t.en_weight from tb_defect_storage t where t.coil_id like CONCAT(substr(&#39;H1910173000000&#39;,0,8),&#39;%&#39;)`*


#### 高级操作
1. 创建索引
   ```
   CREATE INDEX myindex ON mytable (id);
   ```

2. 创建视图
   ```
   CREATE VIEW myview AS SELECT id, name FROM mytable;
   ```

3. 创建触发器
   ```
   CREATE TRIGGER mytrigger BEFORE INSERT ON mytable FOR EACH ROW BEGIN :new.id := :new.id &#43; 1; END;

   ```

4. 创建存储过程
   ```
   CREATE PROCEDURE myprocedure (id INT, name VARCHAR(50)) AS BEGIN INSERT INTO mytable (id, name) VALUES (id, name); END;
   ```

5. 创建函数
   ```
   CREATE FUNCTION myfunction (id INT) RETURN VARCHAR(50) AS BEGIN RETURN (SELECT name FROM mytable WHERE id = id); END;
   ```


#### 典型示例
* 查看数据库服务端版本和位数：
   ```SQL
   select * from v$version
   ```
* 查询表中重复的数据  
   ```SQL
   select * from tb_product where coilno in (select coilno from tb_product group by coilno having count(*) &gt; 1) 
   ```
* 查看服务端版本和位数：
   ```SQL
   select * from v$version
   ```

### 3. Oracle 维护
Oracle的维护包括备份、恢复、性能优化等。以下是一些基本的维护方法：

1. 备份数据库
   ```
   RMAN&gt; BACKUP DATABASE;
   ```

2. 恢复数据库
   ```
   RMAN&gt; RESTORE DATABASE;
   ```

3. 性能优化
   ```
   SQL&gt; EXEC DBMS_STATS.GATHER_DATABASE_STATS;
   ```

### 4. Oracle 安全
Oracle的安全包括用户管理、权限控制、加密等。以下是一些基本的安全方法：

1. 用户管理
   ```
   CREATE USER myuser IDENTIFIED BY mypassword;
   GRANT CONNECT, RESOURCE TO myuser;
   ```

2. 权限控制
   ```
   GRANT SELECT ON mytable TO myuser;
   ```

3. 加密
   ```
   SQL&gt; ALTER TABLE mytable ENCRYPT COLUMN mycolumn;
   ```

### 5. Oracle 监控
Oracle的监控包括性能监控、错误日志、性能调优等。以下是一些基本的监控方法：

1. 性能监控
   ```
   SQL&gt; SELECT * FROM v$sysstat;
   ```

2. 错误日志
   ```
   SQL&gt; SELECT * FROM v$logfile;
   ```

3. 性能调优
   ```
   SQL&gt; EXEC DBMS_STATS.SET_TABLE_PREFS(&#39;mytable&#39;, &#39;STALE_PERCENT&#39;, &#39;10&#39;);
   ```

### 6. Oracle 故障排除
Oracle的故障排除包括错误日志分析、性能调优、故障恢复等。以下是一些基本的故障排除方法：

1. 错误日志分析
   ```
   SQL&gt; SELECT * FROM v$diag_info;
   ```

2. 性能调优
   ```
   SQL&gt; EXEC DBMS_STATS.SET_TABLE_PREFS(&#39;mytable&#39;, &#39;STALE_PERCENT&#39;, &#39;10&#39;);
   ```

3. 故障恢复
   ```
   RMAN&gt; RESTORE DATABASE;
   ```
#### 常见报错及解决

##### 查看数据库错误信息  
`select * from err_logs t order by t.err_time desc`
##### ORA-01017: 用户名/口令无效; 登录被拒绝
   * 检查用户名和密码是否正确
   * 检查是否使用了正确的数据库实例名
   * 检查是否使用了正确的SID
   * 检查是否使用了正确的服务名
##### ORA-01653: unable to extend table
   * 检查表空间是否已满
   * 检查表空间是否已启用自动扩展
##### ORA-12516:TNS:监听程序无法找到匹配协议栈的可用句柄  
查看连接数是否超过最大连接数，适当增加最大连接数可解决
  * 查看当前连接数  
  `select count(*) from v$session`  
  `select count(*) from v$process`  
  * 查看最大连接数  
  `show parameter sessions;`  
  `show parameter processes;`  
  * 查看各用户的连接数  
  `select username,count(*) from v$session where username is not null group by username`  
  * 查看各进程的连接数  
  `select program,count(*) from v$session group by program order by count(*) desc`


---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/oracle_operation/  

