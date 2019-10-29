# Nginx[^nginx]
[^nginx]: (engine x)HTTP和反向代理服务器，邮件代理服务器TCP/UDP代理服务器功能
## 第一章、简介
### 1.1 Nginx特性
Nginx是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，在BSD-like 协议下发行。其特点是占有内存少，并发能力强
### 1.2 Nginx安装
+ 官网: [https://nginx.org/en/download.html](https://nginx.org/en/download.html "nginx")
+ 版本Version
  + Mainline version:主力版本,就是开发版本
  + Stable version: 稳定版本[推荐]
  + Legacy versions:老版本的稳定版本
+ 安装步骤
1) 下载压缩包: `wget -c http://nginx.org/download/nginx-1.16.0.tar.gz`
2) 解压: `tar -zxvf nginx-1.16.0.tar.gz`
3) 创建用户组:`groupadd nginx`
4) 创建用户且不分配home目录: `useradd -g nginx -M nginx`
5) 修改`/etc/passwd`的nginx配置,使得无法bash登录: **nginx: x:1005:1006::/home/nginx:/sbin/nologin**
6) 编译配置:
```shell
# --prefix=/usr/local/nginx \:表示指定安装到 /usr/local/nginx 目录下
./configure --prefix=/usr/local/nginx \
--pid-path=/usr/local/nginx/run/nginx.pid \
--with-http_ssl_module \
--user=nginx \
 --group=nginx \
--with-pcre \
--without-mail_pop3_module \
--without-mail_imap_module \
--without-mail_smtp_module
```
7)编译并安装: `make && make install`
8)查看安装版本: `[root@localhost xlx]# /usr/local/nginx/sbin/nginx -v`
9)修改配置nginx.conf,验证是否生效: `[root@localhost xlx]# /usr/local/nginx/sbin/nginx -t`
10)启动nginx
```shell
[root@localhost nginx-1.10.3]# /usr/local/nginx/sbin/nginx
#未添加nginx服务前对nginx的管理只能通过一下方式管理：
#  nginx 管理的几种方式 -
# 启动Nginx 
/usr/local/nginx/sbin/nginx 
# 从容停止Nginx：
kill -QUIT 主进程号 # 如上一步中的 ps 命令输出的 29151，就是 Nginx的主进程号
# 快速停止Nginx：
kill -TERM 主进程号
# 强制停止Nginx：
pkill -9 nginx

nginx -s reload  # 重新载入配置文件
nginx -s reopen  # 重启 Nginx
nginx -s stop   # 停止 Nginx
nginx -s quit  #正常关闭服务
```
11) 启动后测试(浏览器也行)`curl http://localhost:8080`

#### 1.2.1 Linux安装
+ 安装包
  + nginx-1.16.1.tar.gz
  + pcre-8.42.tar.gz 
  + oppenssl-1.0.1.tar.gz
  + zlib-1.2.8.tar.gz
+ 安装
  + 第一步 pcre-8.42.tar.gz 
  1) 解压, ==[root@localhost opt]#== `tar -xzvf pcre-8.42.tar.gz`
  2) 运行脚本, ==[root@localhost pcre-8.42]#== `./configure`
  3) 编译并安装, ==[root@localhost pcre-8.42]#==` make && make install`
  4) 查看安装版本,==[root@localhost pcre-8.42]#== `pcre-config  --version`
  
  + 第二步 oppenssl-1.0.1.tar.gz
  + 第三步 zlib-1.2.8.tar.gz
  1) yum命令 `yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel`
  2) 一键安装4个 `yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel`
  
  + 第四步 安装nginx
  1) 解压  ,==[root@localhost opt]#== `tar -zxvf nginx-1.16.0.tar.gz`
  2) 执行脚本, ==[root@localhost nginx]#== `./configure`
  3) 编译并执行, ==[root@localhost nginx]#== `make && make install`
  4) 安装成功会有一个/usr/local/nginx文件夹,在nginx有sbin启动脚本
  5) 启动nginx,[root@localhost sbin] `./nginx`
  6) 查看进程,`ps -ef |grep nginx`
  7) linux系统浏览器访问`http://localhost:8080/`
  8) windows系统浏览器访问`http://localhost:8080/`或者`http://IP:8080/`(建议)
  9) 如果外网无法访问,可能就是防火墙的原因,需要把nginx的端口开放
## 第二章、Nginx命令
+ 命令目录: [root@localhost sbin]# (/usr/local/nginx/sbin/)
+ 查看版本,`./nginx -v`
+ 启动,`./nginx`
+ 关闭,`./nginx -s stop`
+ 重启,`./nginx -s reopen`
+ 重新载入配置文件, `./nginx -s reload`
+ 查看配置文件是否生效,`./nginx -t`
## 第三章 、Nginx配置文件
+ 位置: `*/usr/local/nginx/conf/nginx.conf
+ 组成部分:
  + 全局块
    + 从开头到events块之间,整体配置
    + worker_processes值越大,并发处理能力越大
  + events块
    + events块里的指令主要影响Nginx服务器与用户的网络连接 
  + http块 
    +  
    + http全局块
    + server块 
```shell
#第一部分:全局块
#user  nobody;
worker_processes  1;

#第二部分:events块
events {
    worker_connections  1024; #支持的最大连接数
}
#第三部分:http块
http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        location / {
            root   html;
            index  index.html index.htm;
        }
    }
}
```
### 3.1 location指令
+ 用于匹配url
+ 语法
```shell
location [= | ~ | ~* | ^~] uri{ }
```

+ `=` :  
## 第四章、Nginx的反向代理
+ 正向代理
在客户端(浏览器)配置代理服务器,通过代理服务器进行互联网访问
+ 反向代理
## 第五章、Nginx实例
### 5.1 反向代理
#### 5.1.1 示例1
+ 实现效果
  + 浏览器输入地址:`www.123.com`,跳转linux系统tomcat主页
+ 实现
  + Tomcat打开,port:8080
  + 域名映射,修改host文件,C:\Windows\System32\drivers\etc\host   
    + 增加  www.123.com    192.168.40.128 
  + 修改nginx配置
```shell
  server {
        listen       80;
        server_name  192.168.40.128;
        location / {
            root   html;
            proxy_pass  http://127.0.0.1:8080;
            index  index.html index.htm;
        }
```

  + 浏览器访问 www.123.com
#### 5.1.2 示例2
+ 实现效果
  + nginx的反向代理服务器,port=9001
  + 访问http://127.0.0.1:9001/edu/ 跳转到127.0.0.1:8080 
  + 访问http://127.0.0.1:9001/vod/ 跳转到127.0.0.1:8081
+ 实现
  + 2个Tomcat服务器,port1=8080,port2=8081
```shell
server {
        listen       9001;
        server_name 192.168.1.128;

         # proxy_pass 转发
         # ~/xx/ 正则匹配 xx 
        location ~ /edu/ {
            proxy_pass  http://127.0.0.1:8080;
        }

        location ~ /vod/ {
            proxy_pass  http://127.0.0.1:8081;
        }
    }

```
+ 问题
  + 防火墙我只开放了8080,80,22端口为什么9001也能访问?  
## 第六章 、Nginx简介
+ 高性能的HTTP和反向代理的Web服务器,俄罗斯开发的

## 第七章 、Nginx的负载均衡
### 7.1 定义
### 7.2 示例
+ 效果
  + 浏览器地址栏输入http://192.168.1.128:9001/edu/a.html, 负载均衡效果,平均到8080,8081中 
+ 实现
```shell
#http块加入方法
http{
#负载均衡方法
upstream myserver{
  server  192.168.1.128:8080;
  server  192.168.1.128:8081; 
  }

server {
  listen       80;
  server_name  192.168.1.128;
  location / {
    proxy_pass  http://myserver; #转发
    root   html;  
    index  index.html index.htm;
        }
   }
}
```
### 7.3 负载均衡机制
#### 7.3.1 轮询(默认)
+ 定义
  + 每个请求按照时间顺序逐一分配到不同的后端服务器,如果服务器宕掉,能自动剔除服务器
#### 7.3.2 加权(Weighted)
+ 设置服务器的请求数,权值越大分配的越多,默认weight=1
```shell
upstream myserver{
#假设5个请求,3个请求将定向到8080
server 192.168.1.127:8080 weight = 3;
#一个请求将定向到8081
server 192.168.1.128:8081;
#另一个请求将定向到8082
server 192.168.1.129:8082;
}
```
### 7.4 最少连接的(Least connected)
+ 不使繁忙的服务器因过多请求而过载,将新的请求分配给不太繁忙的服务器
+ 访问时间最短时,优先分配(fair)
+  minimum_conn指令激活
```shell
upstream myserver{
minimum_conn;
server 192.168.1.127:8080;
server 192.168.1.128:8081;
server 192.168.1.129:8082;
}
```
### 7.5 会话持久性(ip-hash)
+ 使用轮循或者最少连接的负载平衡,后续的请求不能保证还是分配到原先的服务器上
+ 使用ip-hash，客户端的IP地址用作哈希密钥，以确定应为客户端的请求选择服务器组中的哪个服务器
+ ip-hash指令激活
```shell
upstream myserver{
ip_hash;
server 192.168.1.127:8080;
server 192.168.1.128:8081;
server 192.168.1.129:8082;
}
```
## 第八章、Nginx的动静分离
### 8.1 示例
+ 加入一张图片和一个html文件
  + 路径
    + `/data/images/01.png`
    + `/data/www/a.html`  
+ 测试
  + http://192.168.1.128/images/  
```shell
http{
  server{
    listen       80;
    server_name  192.168.1.128;
    #
    location /www/ {
      root   /data/; 
      index  index.html index.htm;
        }
    #静态资源    
    location /images/ {
      root  /data/;
      autoindex  on; # 开启文件路径,否则返回403 Forbidden
      }
  }
}
```