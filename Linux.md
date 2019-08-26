# Linux学习[^01]
[^01]: Linux是类Unix操作系统,广泛应用于服务器中,安全,开源.由林纳斯·托瓦兹(Linus Torvalds)最初创立,

+ 读法:/li'nju:ks/(“里纽克斯”)。 
## 一﹑Linux主流版本
+ Red Hat Linux
  + Red Hat 
  + **CentOS**
+ Ubuntu 
+ Debian
## 二﹑VMWare安装
官网下载:[https://www.vmware.com/cn/products/workstation-player/workstation-player-evaluation.html](https://www.vmware.com/cn/products/workstation-player/workstation-player-evaluation.html "虚拟机")

+ 需要开启电脑的Virtual(BIOS设置) 
## 三﹑Linux安装
+ 下载地址:
+ 虚拟空间分配(类似安装windos系统的磁盘分区)
  + 网络连接3种方式
  1) 桥接: linux可以和其他系统通信,但是ip易冲突
  2) NAT:ip不会冲突还可以访问外网(建议选)
  3) 仅主机模式

+ CentOS系统安装
  + 简体中文,上海时间[练习使用] 
  + **自定义分区**[分配大小以40GB为例]
  1) 选择标准分区
  2) `/`: 根分区,类似windows的C盘[推荐大小:最后剩下GB],**必须**
  3)  `/swap`:缓存分区,当当虚拟内存不够时,使用硬盘内存存储数据,大小为虚拟内存的2倍,[推荐1GB],**必须**
  4)  `/boot`:Linux内核的启动项,如果根目录损坏,系统也能运行[推荐1GB]
  5)  `home`: 用户数据存放,类似windows的C盘的user目录,默认在根分区下[推荐10GB]
  6)  `var`日志存放,默认在根分区下,[推荐1GB]

  + 创建root管理员的密码,创建一个新帐号(用户名+密码+权限勾选(可选))
  + 安装成功,重启(reboot)
## 四﹑Linux目录结构
+ `/`:根目录
  + `/bin`:常用的命令
  + `/boot`:Linux的核心启动文件
  + `/dev`:Device设备,Linux的外部设备
  + `/etc`:**所有系统管理所需要的配置文件和子目录**
    + `/etc/passwd`:用户管理工作涉及的最重要的一个文件
    + **格式: 用户名:口令:用户标识号:组标识号:注释性描述:主目录:登录Shell**
    + 示例: xlx: x:1000:1000:xlx:/home/xlx:/bin/bash
    + `/etc/shadow`:用来存储把加密后的口令字
    + **格式: 登录名:加密口令:最后一次修改时间:最小时间间隔:最大时间间隔:警告时间:不活动时间:失效时间:标志**
    + `/etc/group`:存放用户组的所有信息
    + **格式: 组名:口令:组标识号:组内用户列表**
    + 示例:group1: x:1004:xiao
  + `home`:用户的主目录,每个用户都有自己的目录,一般目录名为用户名
  + `/lib`:动态连接共享库
  + `/media`:自动识别一些媒体设备,如光盘,光驱,
  + `/mnt`: 临时挂载的文件系统,如与windows共享的文件夹
  + `/opt`:**额外软件安装目录,如安装Mysql,Redis**
  + `/proc`:虚拟目录,系统内存的映射,可以通过直接访问这个目录获取系统信息,修改配置防止ping
  + `/root`:系统管理员(超级权限者)的用户目录
  + `/sbin`:s为super user,存放的是系统管理员使用的系统管理程序
  + `/selinux`:Redhat/CentOS所特有的目录,类似windows的防火墙
  + `/srv`:存放一些服务启动之后需要提取的数据
  + `/sys`:存储了2.6内核中新出现的一个文件系统 sysfs
  + `/temp`:存放一些临时文件
  + `/usr`:**用户的很多应用程序和文件都放在这个目录下，类似于windows下的program files目录**
    + `/user/bin`:系统用户使用的应用程序
    + `/user/local`:软件安装路径,编译源码方式安装的
    + `/user/sbin`:超级用户使用的用户程序
    + `/user/src`:内核源代码默认的放置目录
  + `/var`: **存放经常修改的目录,如日志**
  + `/run`:是一个临时文件系统，存储系统启动以来的信息
  + `.`:表示当前目录,或者`./`
  + `..`:表示上一层目录,或者`../`
## 五﹑文件基本属性
+ `ll`或`ls -l`:显示一个文件的属性以及文件所属的用户和组
+ **第一个字符**,代表类型
  + `d`:目录
  + `-`:文件
  + `l`:链接文档
  + `b`:装置文件里面的可供储存的接口设备
  + `c`:装置文件里面的串行端口设备,例如键盘、鼠标 
+ **接下来的字符以[rwx]三个参数为一组**,[123]属**主权限**,[456]属**组权限**,[789]**其他用户权限**
  + [r]代表可读read     r=4
  + [w]代表可写write       w=2
  + [x]代表可执行execute      x=1
  + [-]代表没有权限
## 六﹑文件与目录管理
+ 绝对路径: 根目录写起`/`,如:/usr/share/doc这个目录
+ 相对路径: 不由`/`写起,如: cd  ../doc
### ==6.1处理目录的常用命令==
+ `ls [opt]`:列出目录
+ `cd [相对或绝对路径]`:切换目录
+ `pwd [-p]`:**显示当前目录路径**,`-p`**会显示确实的路径**
+ `mkdir [opt] 目录名`:==创建一个新目录==
+ `rmdir [-p] 目录名`: **删除当前的空目录**,`-p`**会连同上一级『空的』目录也一起删除**
+ `cp [opt] 目标文件/目录  新的目标/目录`:**拷贝文件和目录**
+ `rm [opt] 文件或目录`:==移除文件或目录==
+ `mv [opt] 源  目标`: 移动文件与目录，或修改名称,[源]:你要处理的对象
### ==6.2文件内容查看==
+ `echo [opt] 内容`: 输出内容到控制台
+ `touch 文件名`:创建空文件,可多个
+ `cat [opt] 文件`: 由第一行开始显示文件内容
+ `tac [opt] 文件`: 由最后一行开始显示文件内容,与`cat`相反
+ `nl [opt] 文件`: 显示行号
+ `more [opt] 文件`: 一页一页翻动
+ `less [opt] 文件`: 一页一页翻动,用于大型文件
+ `head [opt] 文件`:默认显示前10行,加入[-n 20]表示前20行
+ `tail [opt] 文件`:**默认显示最后的10行,加入[-n,20]表示显示最后的20行**
### 6.4命令连接
+ `ln [opt] 操作文件或目录  将要生成的目录或文件链接`
  + [opt]必要
    + `-b`:删除,覆盖以前建立的链接
    + `-d`:允许超级用户制作目录的硬链接
    + `-f`:强制执行
    + `-i`:文件存在提示是否覆盖
    + `-n`:把符号链接视为一般目录
    + `-s`:软链接(符号链接)
    + `-v`:显示详细的处理过程  
+ 软链接
1)  创建软连接:`ln -s  /opt/eclipse   eclipseLink`
2) 以路径形式存在,类似windows的快捷方式
3) 软链接可以 跨文件系统 ，硬链接不可以
4) 软链接可以对目录进行链接

+ 硬链接
1) 创建硬链接: `ln aa.txt  aaLink`
2) 以文件副本的形式存在。但不占用实际空间
3) 不允许给目录创建硬链接
4) 硬链接只有在同一个文件系统中(同父目录下)才能创建

### 6.4`>`指令和`>>`指令
+ `>`:输出重定向,内容覆盖
+ `>>`:末尾追加内容
## 七﹑远程操作
### 7.1 远程登录Linux
+ 必须开启ssd服务,22
+ **Xshell5**工具安装[http://www.xshellcn.com/xiazai.html](http://www.xshellcn.com/xiazai.html)
+ 协议SSH,端口22,主机Linux的ip
+ 编码设置为`utf-8`
### 7.2 远程文件传输
+ **Xftp6**工具安装[http://www.xshellcn.com/xiazai.html](http://www.xshellcn.com/xiazai.html)
+ 协议SFTP,端口22,主机Linux的ip
## 八﹑vi或vim
+ 常用的三种模式
  + 正常模式
    + 插入,删除 
  + 插入模式
  1)按下`i`,`l`,`o`,`O`,`a`,`A`,`r`,`R`等任何一个字母静茹编辑模式[按`i`] 
  2) 文本编辑 
+ 命令行模式 
  + `:q`:退出,加`!`强制
  + `:w`:保存
  + `:qw`:保存退出
## 九﹑用户和用户组管理
### 9.1用户管理
#### 9.1.0 查询用户
+ `id 用户名`:查询用户的用户id(用户名),组id(组),组,如 id root
  + 结果: uid=0(root)   gid=0(root)    组=0(root)
#### 9.1.1 添加用户
+ `useradd [opt] 用户名`
  + [opt]可选
  1)`-c`: 注释
  2)`-d`:指定用户主目录,不存在,使用`-m`可以创建主目录
  3)`-g`:指定用户所属的用户组
  4)`-G`:指定用户所属的附加组
  5)`-s`:指定用户的登录shell
  6)`-u`:指定用户的用户号,有`-o`可以重复使用其他用户的标识号
  7)`M`:不为用户创建`home`目录,默认会在/home目录下生成对应用户名目录
  8) useradd  [-d  /home/sam  -m]  sam : 创建一个用户sam,产生一个sam的主目录/home/sam
  8) useradd [-s /bin/sh  -g  group  -G adm,root] gem : 创建一个用户gem,该用户登录的shell是/bin/sh,属于group用户主组,同时又属于adm和root用户组
#### 9.1.2 删除帐号
+ `userdel [opt] 用户名
  + [opt]可选
  1)`-r`:同时删除用户的主目录
#### 9.1.3 修改账号
+ `usermod [opt] 用户名
  + [opt]可选
  1) `-c`, `-d`, `-m`, `-g`, `-G`, `-s`, `-u`,`-o`和添加用户的[opt]一样
  2) `-l`: 原来的用户名改为新的用户名
  3) usermod [-s  /bin/ksh  -d  /home/z   -g  developer  sam] : 将用户sam的登录shell修改为ksh,主目录为/home/z,用户组为developer
#### 9.1.4 用户密码
+ `passwd [opt] 用户名
  + [opt]可选
  1) `-l`:锁定账号lock
  2) `-u`:解锁账号unlock
  3) `-d`: 使账号无密码
  4) `-f`: 强迫用户下次登录时修改口令
  5) 如果是当前用户,修改的是当前用户 `passwd`(旧密码,新密码,确认新密码)
  6) 如果是管理员,可以修改任意用户 `passwd 用户名`(新密码,确认新密码)

+ 忘记root密码
1) 开机时,按enter回车键
2) 按e,界面里添加init=/bin/sh ,
3) 按<kbd>Ctrl + x </kbd>进入单用户模式
4) passwd 
#### 9.1.5 切换用户
+ `su`: 普通用户切换到管理员,需要密码
+ `su 用户名`:管理员直接切换到普通用户
+ `exit`:退出当前用户,切回原先用户
+ `whoami`:查询当前用户名(我是谁)
### 9.2 用户组管理
#### 9.2.1 添加用户组
+ `groupadd [opt] 用户组`
  + [opt]可选
  1) `-g GID`:指定用户组的标识号(GID)
  2) `-o`: 表示新用户组的GID可以与系统已有用户组的GID相同,一般与`-g`同时使用
  3) groupadd  -g 101 group1 : 向系统中添加一个新组group1,指定新组的GID为101
### 9.2.2 删除用户组
+ `groupdel 用户组`
### 9.2.3 修改用户组
+ `groupmod [opt] 用户组`
  + [opt ]
  1) `-g GID`: 用户组指定新的组织编号
  2) `-o`: 用户组的新GID可以与系统已有用户组的GID相同,与`-g`同时使用
  3) `-n`:用户组名字重命名
  4) groupmod  [-g  1000  -n  group3]  group2:修改用户组group2的标识,修改用户组名称为group3  

+ `newgrp root`: 将当前用户切换到root用户组，前提条件是root用户组确实是该用户的主组或附加组,用户可以在登录后，使用命令newgrp切换到其他用户组
##  十﹑运行级别
1) 关机[0,halt]
2) 单用户修改[1,Single user mod]
3) 多用户无网络[2,Multiuser without NFS]
4) 多用户有网络[3, Full Multiuser mod]
5) 本地保留[4,unused]
6) 图形界面[5,Xll]
7) 重启[6,reboot]
## 十一﹑压缩和解压缩
### 11.1压缩
+ **gzip**
  + gzip  aa.txt :压缩文件aa.txt为aa.txt.gz
+ **zip** 
  + zip  -r  mypackage.zip  /home/user/ :压缩/home/user目录下所有,生成的压缩包叫mypackage.zip
+ **tar**
  + `tar -czvf test.tar.gz  aa.txt`: **压缩aa.txt文件(也可目录)为test.tar.gz**  ==[**C**reate,创建]==
  + `tar -xzvf test.tar.gz`:**将压缩包bb.tar.gz解压**  ==[**E**xtract,解压]==
  + `tar -tzvf test.tar.gz`:列出压缩文件内容  ==[**L**ist,列表]==
### 11.2解压缩
+ `gunzip 文件.gz`:将格式为.gz文件解压
+ `unzip [opt] xxx.zip`:解压,`-d`指定解压的存放路径
  + unzip  -d  /opt/tmp/   mypackage.zip:将压缩包mypackage.zip解压到/opt/tmp目录下
## 十二﹑rpm包
### 12.1介绍
+ RedHat Package Manager 互联网下载包的打包及安装工具,类似windows的setup.exe
### 12.2查询
+ `rpm -qa |more`:查询所有rpm安装包 ,[|more]:用来分页
+ `rpm -qa | grep jdk`:查询是否安装jdk
+ `rpm -qi firefox`:查询安装的rpm包软件信息
+ `rpm -ql firefox`:rpm包的安装路径
+ `rpm -qf /etc/passwd`:查询文件/etc/passwd属于那个rpm包
### 12.3卸载
+ `rpm -e rpm安装包名`: 软件卸载,强制删除:`--nodeps`
### 12.4安装
+ `rpm -ivh rpm安装包`:软件安装
  + `i`:install 安装
  + `v`:verbose 提示
  + `h`:hash 进度条 
##  十三﹑yum命令
### 13.1介绍
+ yum: Yellow dog Update,Modified是一个在Fedora和RedHat以及SUSE中的Shell前端软件包管理器
+ `yum [opt] [comm] [package..]`
### 13.2 yum常用命令
+ `yum check-update`:列出所有可更新的软件清单命令
+ `yum update`:更新所有软件
+ `yum install <package_name>`:**仅安装指定的软件**
+ `yum update <package_name>`: 仅更新指定的软件
+ `yum list`:列出所有可安装的软件清单,
+ `yum remove <package_name>`:**删除软件包**
+ `yum search <keyword>`:查找软件包
+ 清除缓存命令
  + `yum clean packages`:清除缓存目录下的软件包
  + `yum clean headers`:清除缓存目录下的headers
  + `yum clean oldheaders`:清除缓存目录下旧的headers
  + `yum clean,yum clean all(=yum clean packages;yum clean oldheaders)`:清除缓存目录下的软件包及旧的headers 
## 十四﹑常用命令
+ `su`:当前为普通用户,切换为管理员,然后输入密码
+ `su 普通用户名`: 管理员切换为普通用户
+ `ps  -ef |grep redis`:查看Redis是否启动
+ `lsof -i tcp:80`:查看80端口
+ `shutdown -h now`:立刻关机
+ `shutdown -h n`: n分钟后就关机
+ `shutdown -r now`: 立刻重启
+ `halt`: 关机
+ `reboot`: 重启
+ `sync`: 把内存的数据同步到磁盘
# 编程软件安装
## 一﹑JDK安装
+ 方式一: 现有安装包解压缩
1) Oracle官网下载jdk压缩包,jdk-8u221-linux-x64.tar.gz,放在/opt/目录下
2)在/opt/目录下 解压,`tar -zxvf jdk-8u221-linux-x64.tar.gz`
3)环境变量配置(参考windows的配置),打开profile文件`vim  /etc/profile
4)在文件末尾加入如下配置:
```profile
JAVA_HOME=/opt/jdk-8u221
PATH=${JAVA_HOME}/jre:$PATH
export JAVA_HOME  PATH
```
5)使文件立即生效`source /etc/profile`,省去重启
6)验证配置生效?`java -version`或`java`或`javac`
7)编译运行Hello.java文件
```java
public class Hello{
  public static void main(String[] args){
  //编译: javac Hello.java
  //运行: java Hello
    System.out.println("hello world!");
  }
}
```

+ 方式二: yum一键式安装,默认路径/usr/local/jvm 
## 二﹑Tomcat安装
### 关闭默认防火墙firewalld,开启iptables
+ 关闭firewalld
1) 查看状态,`systemctl status firewalld`
2) 关闭firewalld,`systemctl stop firewalld`
3) 屏蔽firewalldID服务,`systemctl mask firewalld`[开启,unmask]
4)  禁止开机启动,`systemctl disable firwalld`

+ 使用iptables服务
1) 安装服务,`yum install iptables-services`
2) 开启服务,`systemctl enable iptables`
3) 启动服务,`systemctl start iptables`
4) 查看开放端口,`iptables -nL`
5) 编辑防火墙配置文件,`vim /etc/sysconfig/iptables`
6) 新增一个8080tcp端口,复制22端口那行,22改8080即可
7) 保存防火墙规则,`service iptables save`
8) 重启iptables服务`systemctl restart iptables`[stop,停止;start,启动]
9) 重复4)步骤,再次看下是否8080端口生效

+ 官网下载tar.gz压缩包
+ 解压至目录/opt下,开启tomcat(运行tomcat/bin/startup.sh),浏览器测试是否安装成功
+ windows浏览器测试tomcat或使用`telnet ip:8080`,不成功配置8080端口(参考iptables)
+ 再次windows浏览器测试,(windows开启telnet,连接依然会报在端口:23连接失败)
## 三﹑Eclipse安装
+ 官网下载tar.gz包,解压到目录/opt下
+ 打开eclipse,新建一个动态web工程测试输出
## 四﹑Mysql安装
+ 过程还是挺复杂的
+ 需要下载编译环境和cmake编译器压缩包
+ 需要mysql压缩包[mysql-5.7.27.tar.gz](https://dev.mysql.com/downloads/mysql/5.7.html#downloads "源码包下载")
+ mysql是选择Source Code , Generic Linx(Architecture Independent)
### 4.1安装编译工具
+ `yum -y install gcc gcc-c++ cmake  ncurses-devel bison-devel`
### 4.2安装cmake编译器
+ 下载压缩包[cmake-3.15.1.tar.gz](https://github.com/Kitware/CMake/releases/download/v3.15.1/cmake-3.15.1.tar.gz "源码包")
+ /opt目录解压缩,
+ 到该包/cmake-3.15.1目录下编译安装
  + `./bootstrap`:先打开
  + `make && make install`:编译并安装 
### 4.3安装mysql
+ /opt目录下解压缩,cd 到安装包目录下/mysql-5.7.21
+ 编译安装
```shell
cmake -DCMAKE_INSTALL_PREFIX=/usr/local/mysql
-DMYSQL_DATADIR=/usr/local/mysql/data -DSYSCONFDIR=/etc
-DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_INNOBASE_STORAGE_ENGINE=1
-DWITH_MEMORY_STORAGE_ENGINE=1 -DWITH_READLINE=1
-DMYSQL_UNIX_ADDR=/var/lib/mysql/mysql.sock -DMYSQL_TCP_PORT=3306
-DENABLED_LOCAL_INFILE=1 -DWITH_PARTITION_STORAGE_ENHINE=1
-DEXTRA_CHARSETS=all -DDEFAULT_CHARSET=utf8
-DDEFAULT_COLLATION=utf8_general_ci
## 如果出现boot找不到,安装好后添加
-DWITH_BOOST=/usr/local/boost/
```

+ `make && make install`
+ 安装成功在目录/usr/local/webserver/下有mysql文件夹 
+ 检查是否有叫mysql用户和mysql组(应该没有),`cat /etc/passwd`和`cat /etc/group`
+ 添加用户和组,`groupadd mysql`和`useradd -g mysql mysql`
+ 把mysql文件夹移动到目录/usr/local下
+ 有问题,没装成功
## 五﹑官网yum安装mysq
+ 官方文档:[https://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/](https://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/)l
### 5.1下载
+ 下载8.0的rpm包`wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm`
+ 安装rpm包`sudo rpm -Uvh mysql80-community-release-el7-3.noarch.rpm`
+ 社区版本选择(社区版免费)
  + 禁用8.0:`sudo yum-config-manager --disable mysql80-community` 
  + 选择5.7:`sudo yum-config-manager --enable mysql57-community`
### 5.2安装
+ 安装mysql服务:`sudo yum install mysql-community-server`
+ 检查mysql服务器状态:`sudo systemctl status mysqld.service`
+ 开启mysql服务:`sudo systemctl start mysqld.service`
+ 第一次不知道密码,可以获取一个临时密码:`sudo grep 'temporary password' /var/log/mysqld.log`
+ 登录mysql:`mysql -u root -p`
+ 修改密码:`ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!'`
  + 密码要求:至少一个大写字母，一个小写字母，一个数字和一个特殊字符，密码长度至少8位 
```shell
systemctl stop mysqld #关闭MySQL
systemctl restart mysqld #重启MySQL
systemctl status mysqld #查看MySQL运行状态
systemctl enable mysqld #设置开机启动
systemctl disable mysqld #关闭开机启动
```
# Shell
## 介绍
+ Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。
+ Linux的Shell种类多,这里使用Bash(Bourne Again Shell)
+ `#!`:是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行，即使用哪一种 Shell。
+ `#!/bin/sh`，它同样也可以改为 `#!/bin/bash`
## 创建shell脚本
+ hello.sh
```shell
#!/bin/bash
echo "hello word!"
```
## 执行
+ 分配一个可执行权限执行
1) `chmod 744 hello.sh`:默认是644
2) 执行:`./hello.sh`(相对路径) 或者`/root/shell/hello.sh`(绝对路径)

+ 强制执行
  + `sh  ./hello.sh`或者`sh /root/shell/hello.sh`
## shell 变量
### 定义
定义变量时,变量名不加美元符号,只能由

+ 英文字母，数字和下划线，首个字符不能以数字开头
+ 中间不能有空格，可以使用下划线`_`
+ 不能使用标点符号。
+ 不能使用bash里的关键字
