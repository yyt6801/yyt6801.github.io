# MongoDB常用操作


MongoDB是Nosql数据库，以文档形式存储，适合大规模非关系型数据归档。  
  
### MongoDB与关系型数据库的对应关系：  
SQL|MongoDB|备注
--|--|--
database|database|数据库
table|collection|表 -&gt; 集合
row|document|某条数据记录 -&gt; 文档
column|field|字段 -&gt; 域/键
  
### MongoDB安装
* [官网](https://www.mongodb.com/try/download/community)下载二进制安装包。 
* [历史归档版本](https://www.mongodb.com/try/download/community-edition/releases/archive)    
* 使用压缩包安装  
MongoDB目录中新建data文件夹，data中新建db和log文件夹
mongod.cfg中配置数据保存路径（db）、日志目录（log）  
安装命令：  
mongod  
--bind_ip 0.0.0.0  
--logpath c:\MongoD..log文件的路径  
--logappend  
--dbpath c:\MongoD…data\db文件夹路径  
--port 27017  
--serviceName &#34;MongoDB&#34;  
--serviceDisplayName &#34;MongoDB&#34;  
--install  

例如：在  bin目录中执行：    
```
mongod --bind_ip 0.0.0.0 --logpath C:\mongodb\data\log\mongod.log --logappend --dbpath C:\mongodb\data\db --port 27017 --serviceName &#34;MongoDB&#34; --serviceDisplayName &#34;MongoDB&#34; --install
```
安装完成后，在bin目录中执行mongod命令启动MongoDB服务

### MongoDB工具推荐
Robo 3T (但Mongo4.0以后停止更新了，最后版本为1.4.4。 重构为更名为Studio 3T，体验比之前繁琐)；  
Navicat Lite也完美支持MongoDB，并且现在免费了，也强烈推荐


### MongoDB常用操作
* 查看数据库列表  
    *`show dbs`*  
* 查看当前数据库  
    *`db`*  
* 切换/创建数据库  
    *`use mydb`*  
* 查看mongodb版本  
    *`db.version()`* 

#### 增
* 插入文档  
    *`db.collection.insert({})`*  
* 插入多个文档  
    *`db.collection.insertMany([{},{}])`*
* 添加索引  
  添加title字段为索引升序；若降序为 -1  
    *`db.col.createIndex({&#34;title&#34;:1})`*  
    *`db.col.createIndex({&#34;title&#34;:1,&#34;description&#34;:-1})`*
    
#### 删
* 删除数据库  
    *`db.dropDatabase()`*  
* 删除集合  
    *`db.collection.drop()`*
* 删除文档  
  *  删除所有数据，清空CollName集合:  
    *`db.collection.remove({})`*  
#### 改
* 更新文档  
    *`db.collection.update({},{},1)`*

#### 查
* 查看集合列表  
    *`show collections`* 
* 查看集合  
    *`show collections`*  
* 查看集合中的文档  
    *`db.collection.find()`*  
* 查询文档  
    *`db.collection.find({})`*  
* 查询文档，并返回指定字段  
    *`db.collection.find({}, {field1: 1, field2: 1})`*  
* 查询文档，并返回指定字段，不返回_id字段  
    *`db.collection.find({}, {field1: 1, field2: 1, _id: 0})`*  
* 查询文档，并返回指定字段，不返回_id字段，并按指定字段排序  
    *`db.collection.find({}, {field1: 1, field2: 1, _id: 0}).sort({field1: 1})`  



---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/mongodb_operation/  

