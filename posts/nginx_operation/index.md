# Nginx常用配置


Nginx是一个高性能的HTTP和反向代理服务器，常用于负载均衡、静态资源处理、反向代理等场景。

### 1. Nginx操作

* 1.启动nginx:  
 在nginx的安装目录  
`start nginx`  或  `nginx.exe`
  
* 2.停止nginx  
  (stop是快速停止nginx，可能并不保存相关信息；quit是完整有序的停止nginx，并保存相关信息)  
  `nginx.exe  -s stop`  或  `nginx.exe -s quit`
* 3.检查 重启：  
    `nginx -t`  检查修改后的nginx.conf配置是否正确  
   `nginx -s reload` 重启nginx
   
### 2. Nginx配置

Nginx配置文件为conf文件夹下的nginx.conf配置文件，主要分为三部分：
* 1.http部分：  
    http部分是nginx最核心的部分，包含了http请求的处理、负载均衡、缓存等配置
* 2.server部分：  
    server部分是http部分的子部分，每个server部分定义了一个虚拟主机，包含了监听的端口、域名、请求的处理等配置
* 3.location部分：  
    location部分是server部分的子部分，每个location部分定义了一个请求的处理规则，包含了请求的路径、请求的处理方式等配置

#### 最基本的http配置

    http {
        include       mime.types;
        default_type  application/octet-stream;
        sendfile        on;
        keepalive_timeout  65;
        server {
            listen       80;
            server_name  localhost;
            location / {
                root   html;
                index  index.html index.htm;
            }
        }
    }

上述配置定义了一个http请求的处理规则，监听80端口，当访问localhost时，返回html目录下的index.html文件。一个http部分可以包含多个server部分，每个server部分定义了一个虚拟主机，一个server部分可以包含多个location部分，每个location部分定义了一个请求的处理规则。

#### 配置多个虚拟主机(web容器服务)

#### 最基本的server配置

    server {  
        listen  80;
        server_name  localhost;
        location / {
            root   html;
            index  index.html index.htm;
        }
    }  

上述配置监听80端口，当访问localhost时，返回html目录下的index.html文件

#### 配置多个web容器服务

    server {  
        listen  80;
        server_name  localhost;
        location / {
            root   path1;
            index  index.html index.htm;
        }
    }  

    server {  
        listen  8080;
        server_name  localhost;
        location / {
            root   path2;
            index  index.html index.htm;
        }
    }  

上述配置监听80端口和8080端口，当访问localhost时，返回path1目录下的index.html文件，当访问localhost:8080时，返回path2目录下的index.html文件

#### 配置反向代理

    server {  
        listen  80;
        server_name  localhost;
        location / {
            proxy_pass  http://localhost:8080;
        }
    }  

上述配置监听80端口，当访问localhost时，将请求转发到http://localhost:8080

#### 配置负载均衡

    http {
        upstream myserver {
            server localhost:8080;
            server localhost:8081;
        }
        server {
            listen  80;
            server_name  localhost;
            location / {
                proxy_pass  http://myserver;
            }
        }
    }
上述配置监听80端口，当访问localhost时，将请求转发到http://myserver，myserver是一个负载均衡组，包含两个服务器，当请求到达时，nginx会根据负载均衡算法将请求转发到其中一个服务器

#### 配置静态资源，如图片、视频等

    server {
        listen       80;
        server_name  localhost;
        location ~* \.jpg$ {  
          root D:/img; 
        }
        location ~* \.wav$ {  
          root D:/wav; 
        }
        location ~* \.mp4$ {  
          root D:/video;
        }
    }

上述配置实现监听8080端口,当访问localhost时，根据请求的文件类型，将请求转发到对应的目录。  
如访问localhost/1.jpg时，将请求转发到D:/img/1.jpg；访问localhost/1.wav时，将请求转发到D:/wav/1.wav；访问localhost/1.mp4时，将请求转发到D:/video/1.mp4

#### 配置二级域名转发

    server {
        listen       80;
        server_name demo.test.com;
        location / {
            proxy_pass http://localhost:8001;
        }
    }
    server {
        listen       80;
        server_name product.test.com;
        location / {
            proxy_pass http://localhost:8002;
        }
    }

上述配置实现监听80端口,当访问demo.test.com时，将请求转发到http://localhost:8001；当访问product.test.com时，将请求转发到http://localhost:8002

#### 前端静态打包请求代理  
针对前端项目有多个请求接口，打包为静态资源后，需配置代理，将请求转发到后端接口

    server {
        listen       80;
        server_name  localhost;
        location / {
          root   /path/to/dist;
          index  index.html index.htm;
          try_files $uri $uri/ /index.html;
        }
        location /api1/ {
            proxy_pass http://localhost:8001;
        }
        location /api2/ {
            proxy_pass http://localhost:8002;
        }
    }

上述配置实现监听80端口，请求的静态资源路径为/path/to/dist  前端接口请求localhost/api1/时，将请求转发到http://localhost:8001；前端接口请求localhost/api2/时，将请求转发到http://localhost:8002


---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: http://localhost:1313/posts/nginx_operation/  

