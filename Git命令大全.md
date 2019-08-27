# Git[^1]
[^1]: git分布式的管理
## Git命令大全a
### 常见命令
+ `mkdir learngit`:创建名字为learngit的文件夹
+ `cd learngit`:转到learngit文件夹目录下
+ `pwd`:显示当前目录
+ `ls`:显示仓库目录
+ `ls -a`:附件显示`.git`这个子目录
+ `cat <filename>`:显示文文内容 cat  hello.txt
+ `touch <filename>`:新增文件, touch  hello.txt
###  配置命令 **git config**
+ `--global`: 参数是全局参数，也就是这些命令在这台电脑的所有Git仓库下都有用。
### **git**基础命令
+ `git init`:将一个文件夹初始化为一个git本地仓库
+ `git add <file>`:添加
  + **git add readme.txt**:添加一个*readme.txt*文件到暂存区(缓存)
+ `git add .`:添加当前项目的所有文件
+ `git commit -m '提交注释`:提交单次文件,`-m ''`代表备注信息
+ `git commit -a -m '提交注释'`:
  + 自动把所有**已经跟踪过**的文件暂存 起来一并提交从而跳过 git add 步骤.没有-m会打开编
  + 辑 器要你添加注释信息
+ `git clone [url]`:从url里下载
+ `git status`:查看你上次提交之后是否有修改(输出详细内容)
  + 可以为`git status`命令设置别名,设置方法:
  + `git config --global alias.st status`,以后简写 `git st`	
+ `git status -s`:简短的结果输出
  + **A**:添加到暂存区(缓存),__AM__:已添加到缓存又修改
  + **M**\_:1. 左边M:文件被修改并放入了暂存区(stage)/索引(index)--- git add
  +  \_**M** : 2. 右边的M:文件被修改但未放入暂存区 
  + **MM**:工作区被修改提交到暂存区后又在工作区被修改(暂存区,工作区都有修改记录)
  + staged:已暂存,对一个已修改文件的当前版本做了标记,使之包含在下次提交的快照中
### 差异diff
+ `git diff`:显示尚未暂存(缓存)的改动 -----查看执行*git status*的结果的详细信息
+ `git diff --cached`:查看已暂存的改动
+ `git diff --HEAD`:查看已暂存的与未暂存的所有改动
+ `git diff --sata`:显示摘要而非整个diff
+ `git reset HEAD`:用于取消之前*git add*添加操作
### 删除rm
+ **`git rm <file>`**:  把文件从工作区和暂存区中删除
  + 如果只是简单地从工作目录中手工删除文件，运行 git status 时就会在 Changes not staged for commit 的提示。要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除，然后提交。可以用以下命令完成此项工作 

+  `git rm -f <file>`:删除修改过并已放到暂存区的文件
  + 如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项 -f

+ `git rm --cached <file>`:把暂存区的文件删除,变成未追踪状态     
  + 如果把文件从暂存区域移除，但仍然希望保留在当前工作目录中，换句话说，仅是从跟踪清单中删除，使用 `--cached` 选项即可

+ ~~`git rm -r *`~~: ??????????
  + 递归删除，即如果后面跟的是一个目录做为参数，则会递归删除整个目录中的所有子目录和文件
+ `git mv <source> <destination>`:用于移动或重命名一个文件、目录、软连接。
  + 在工作区和暂存区移动或重命名,\<destination\>不为目录名,执行重命名,为目录名,执行移动
  + 重命名示例:
  + "**git  mv  m1.txt   README.md**"   将m1.txt修改为README.md
#### git commit, git push, git pull, git fetch, git merge 含义与区别
+ `git commit`: 将本地修改过的文件提交到本地仓库去
+ `git push`: 从本地仓库的最新信息**推送**到远程仓库去
+ `git pull`: 从远程仓库**获取**最新版本到本地仓库,并自动merge
+ `git fetch`: 从远程仓库**获取**最新版本到本地仓库,不会自动merge
+ `git merge`: 用于从指定的commit(s)合并到当前分支,用来合并两个分支

## Git分支管理[^branch]
[^branch]:git分支,缺省状态下默认当前分支为'master',未合并前各个分支文件不相干
1. 创建分支
  + `git branch`:列出本地分支
  + `git branch (branchname)`:手动创建分支,eg:git branch 'newbra1'
  + `git checkout (branchname)`:切换分支
  + `git checkout -b (branchname)`:创建新分支并立即切换到该分支下
2. 删除分支
  + `git branch -d (branchname)`:删除分支
3. 合并分支
  + `git merge branchname`: 合并分支
  + 合并冲突:合并并不仅仅是简单的文件添加、移除的操作，Git 也会合并修改。

## Git查看提交历史
+ `git log`:查看历史记录
+ `git log --oneline`:查看历史记录(简洁,主要是提交信息)
+ `git log --oneline --graph`:查看历史记录(开启了拓扑图)
+ `git log --reverse --oneline`:逆向显示所有日志
+ `git log --author=<authorname> --oneline -n`:查看指定用户的提交日志,**-n**:显示记录条数,n=1,2,3...
+ `git log --oneline --before={3.weeks.ago} --after={2019-03-01} --no-merges`:三周前且在3月1日之后的所有提交,no-merges:隐藏合并提交
+ 更多 git log 命令可查看：<http://git-scm.com/docs/git-log>
## Git标签
1. 创建标签
  + `git tag`:查看所有标签
  + `git tag -a <tagname> -m '注解'`:创建一个带有注解信息的标签,
    + 没有-m会打开编辑器要求你添加注释信息
    + 示例: git  tag  -a  v1.0  -m 'tag标签1.0' 
  + `git tag -s <tagname> -m '注释'`:PGP签名标签
  + `git tag -a <tagname> <commit号> -m '注解'`:补加上次遗漏的标签
    +  commit号:,如1d097c9,c65e2db.....  
2. 删除标签
  + `git tag -d <tagname>`: 删除标签
3. 查看版本所修改的内容
  + `git show <tagname>`:查看指定版本所修改的内容

## 进阶~GitHub * 重点

2. 远程仓库
	+ `git remote`:查看当前配置有哪些远程仓库
	+ `git remote -v`:显示每个别名的URL地址
3. 添加远程仓库
	+ `git remote add [alias] [url]`:添加一个新的远程仓库

    > [alias]: 远程仓库别名  [url]: 链接地址
4. 从本地推送至远程仓库(**远程库更新**)
  + `git push [alias] [branch]`:将你的[branch]分支推送成为[alias]远程仓库上的[branch]分支

    > [alias]:远程仓库的别名, [branch]: 分支,?????????/
5. 从远程抓取至本地(**本地更新**)
  + `git fetch [alias]`:从远程下载最新分支与数据(本地还未同步)
  + `git merge [alias]/[branch]`:将服务器上的**更新**合并到你的当前分支(同步到本地)

    > [alias]:远程库别名, [branch]: 分支,?????
  + `git pull [alias] [branch]`:提取远程仓库到本地,自动merge

    > git pull = git fetch + git merge
    > `fatal: refusing to merge unrelated histories`:拒绝合并不相关的历史
    > 解决方法:使用`git pull [alias] [branch] --allow-unrelated-histories`
6. 删除远程仓库

  + `git remote rm [alias]`:删除远程仓库,[alias]:远程仓库的别名
7. 克隆仓库
  + `git clone url [name]`:克隆仓库,name:可选,重命名克隆仓库
    > 远程到本地
    > git clone  **git://github.com/XielinX/simple.git**	 -- GIT协议
    > git clone  **https://github.com/XielinX/simple.git **	-- HTTPS协议		
    > git clone  **git@github.com:XielinX/simple.git**	-- SSH协议  
    > 
    > 三种写法
    > git clone  **git://github.com/XielinX/simple  mygit**
    > git clone  **git://github.com/XielinX/simple.git  mygit**
    >
    > git clone  **https://github.com/XielinX/simple  mygit**
    > git clone  **https://github.com/XielinX/simple.git  mygit**
    >   
    > git clone  **git@github.com:XielinX/simple  mygit**
    > git clone  **git@github.com:XielinX/simple.git  mygit**
  + `git clone <source repository>  <destination repository>`
    > 本地与本地
    > \<source repository\>:本地仓库路径
    > \<destination repository\>:克隆到另一个地方路径,该路径必须是未创建,或已创建但为空(即一个空文件夹),不然克隆会失败
    > 如:git clone d:/git1   e:/git2   解释:将D盘下的git1目录克隆到E盘下的git2目录下


##  生成密钥
1. 创建SSH Key 
  + `ssh-keygen -t rsa -C "yourgithubemail@example.com"` :默认命名的密钥名id_rsa
  + `ssh-keygen -t rsa -C yourgithubemail@example.com -f ~/.ssh/key-name`:生成密钥名为key-name
  + `ssh -T git@github.com`:验证是否成功生成密钥
> 成功的话会在 ~/ 下生成 .ssh 文件夹，进去，打开 id_rsa.pub，复制里面的 key。
>
> `xxx.pub`:是公钥,另一个是私钥