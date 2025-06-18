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

### MongoDB工具推荐：  
Robo 3T (但Mongo4.0以后停止更新了，最后版本为1.4.4。 重构为更名为Studio 3T，体验比之前繁琐)；  
Navicat Lite也完美支持MongoDB，并且现在免费了，也强烈推荐

---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: http://localhost:1313/posts/mongodb_operation/  

