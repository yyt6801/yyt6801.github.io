# Windows命令提示符 CMD 常用操作

# 常用命令
* 切换盘符
    *`cd 盘符:`*
    也可直接输入盘符切换盘符，如：`E:`
* 切换到当前路径的上一级目录  
    *`cd ..`*
* 切换到当前路径的下一级目录  
    *`cd 目录名`*
* 切换到当前路径的上一级目录的上一级目录  
    *`cd ../..`*
* 切换到当前路径  
    *`cd /d %~dp0`*
* 清空剪切板  
    *`cmd /c &#34;echo off | clip`*  
    或  
    *`dir|clip`*

# autoIP.bat
```bat
@echo off
cd /d %~dp0
%1 start &#34;&#34; mshta vbscript:createobject(&#34;shell.application&#34;).shellexecute(&#34;&#34;&#34;%~0&#34;&#34;&#34;,&#34;::&#34;,,&#34;runas&#34;,1)(window.close)&amp;exit
netsh interface ip set address name=&#34;以太网&#34; source=dhcp
netsh interface ip set address name=&#34;WLAN&#34; source=dhcp
```

# renewIP.bat
```bat
ipconfig/flushdns
ipconfig/release
ipconfig/renew
pause
```

# 设置以太网Ip为10.20.32.247.bat
```bat
netsh interface ip set address name=以太网 source=static addr=10.20.32.247 mask=255.255.255.0
pause
```

# 设置无线网wlan的Ip为_10.20.32.252.bat
```bat
netsh interface ip set address name=WLAN source=static addr=10.20.32.252 mask=255.255.255.0
pause
```

# 修改路由表到192.168.1.1.bat
```bat
cd /d %~dp0
%1 start &#34;&#34; mshta vbscript:createobject(&#34;shell.application&#34;).shellexecute(&#34;&#34;&#34;%~0&#34;&#34;&#34;,&#34;::&#34;,,&#34;runas&#34;,1)(window.close)&amp;exit
route add 10.151.18.86 192.168.1.1
route add 10.151.18.218 192.168.1.1
route add 10.151.18.165 192.168.1.1
pause
```

# oracle开启服务
```bat
cd /d %~dp0
%1 start &#34;&#34; mshta vbscript:createobject(&#34;shell.application&#34;).shellexecute(&#34;&#34;&#34;%~0&#34;&#34;&#34;,&#34;::&#34;,,&#34;runas&#34;,1)(window.close)&amp;exit
net start OracleServiceNERCAR
net start OracleOraDb11g_home1TNSListener
pause
```

# oracle关闭服务
```bat
cd /d %~dp0
%1 start &#34;&#34; mshta vbscript:createobject(&#34;shell.application&#34;).shellexecute(&#34;&#34;&#34;%~0&#34;&#34;&#34;,&#34;::&#34;,,&#34;runas&#34;,1)(window.close)&amp;exit
NET STOP OracleServiceNERCAR
NET STOP OracleOraDb11g_home1TNSListener
NET STOP OracleDBConsolenercar
NER STOP Oracl NERCAR VSS Writer Service
```

# 打开虚拟机Vmware服务及应用.bat
```bat
@echo off
cd /d %~dp0
%1 start &#34;&#34; mshta vbscript:createobject(&#34;shell.application&#34;).shellexecute(&#34;&#34;&#34;%~0&#34;&#34;&#34;,&#34;::&#34;,,&#34;runas&#34;,1)(window.close)&amp;exit
net start &#34;VMware Authorization Service&#34;
net start &#34;VMware DHCP Service&#34;
net start &#34;VMware NAT Service&#34;
net start &#34;VMware USB Arbitration Service&#34;
net start &#34;VMware Workstation Server&#34;
start &#34;&#34; &#34;F:\vmware\vmware.exe&#34;
exit
```


---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/windows_cmd_operation/  

