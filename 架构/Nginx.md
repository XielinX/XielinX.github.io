# Nginx[^nginx]
[^nginx]: HTTP和反向代理服务器，邮件代理服务器TCP/UDP代理服务器功能
## Nginx特性
+ 简单耐操
## Nginx安装
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
12) 

