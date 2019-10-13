# Git[^1]
[^1]: git分布式的管理
## 一、常见命令
+ `mkdir learngit`:创建名字为learngit的文件夹
+ `cd learngit`:转到learngit文件夹目录下
+ `pwd`:显示当前目录
+ `ls`:显示仓库目录
+ `ls -a`:附件显示`.git`这个子目录
+ `cat <filename>`:显示文文内容 cat  hello.txt
+ `touch <filename>`:新增文件, touch  hello.txt
## 二、 配置命令 **git config**
### 3个配置路径
#### C 盘用户目录下
+ `~/.gitignore`文件: 只针对当前用户
+ `--global` :读取此配置
> 参数是全局参数，也就是这些命令在这台电脑的所有Git仓库下都有用。
> git config  --gobal  user.name  "zhang"
> git config  --gobal  user.email  12@qq.com

#### git安装目录下
+ `/etc/gitconfig`文件: 包含系统上每一个用户及他们仓库的通用配置
+ `--system`:读取此配置
#### 本地git仓库目录下
+ `.git/config`文件
+ 不加选项,只针对该仓库不加,就是某个仓库指定特别的用户和邮箱
### 编辑器配置
+ `git config --gloabal core.editor`: 查看编辑器
+ `git config --gloabal core vim`: 设置编辑器

## 三、 **git**基础命令
### 1.初始化仓库
+ `git init`:将一个存在的文件夹初始化为一个git本地仓库
+ 克隆方式
### 2.添加add 
+ `git add <file>`:添加文件到暂存区(追踪)
+ `git add .`:添加当前项目的所有文件,`.`代表目录路径
### 3.提交commit
+ `git commit -m "提交注释"`: 提交单次文件,`-m ''`代表备注信息
+ `git commit -a -m '提交注释'`:
  + 自动把所有**已经跟踪过**的文件暂存 起来一并提交从而跳过 git add 步骤.没有-m会打开编
  + 辑 器要你添加注释信息
### 4.状态status
+ `git status`:查看你上次提交之后是否有修改(输出详细内容)
  + 可以为`git status`命令设置别名,设置方法:
  + `git config --global alias.st status`,以后简写 `git st`	
+ `git status -s`:简短的结果输出
  + **A**:添加到暂存区(缓存),__AM__:已添加到缓存又修改
  + **M**\_:1. 左边M:文件被修改并放入了暂存区(stage)/索引(index)--- git add
  +  \_**M** : 2. 右边的M:文件被修改但未放入暂存区 
  + **MM**:工作区被修改提交到暂存区后又在工作区被修改(暂存区,工作区都有修改记录)
  + staged:已暂存,对一个已修改文件的当前版本做了标记,使之包含在下次提交的快照中
### 5.差异diff
+ `git diff` : 只显示尚未暂存的改动,即文件修改后还没暂存的变化内容
+ `git diff --cached`:查看已暂存的改动
+ `git diff --staged`: 支持Git 1.6.1+的版本 效果是相同的，但更好记些
+ `git diff --HEAD`:查看已暂存的与未暂存的所有改动
+ `git diff --sata`:显示摘要而非整个diff
+ `git reset HEAD`:用于取消之前*git add*添加操作
### 6. 删除rm
+ **`git rm <file>`**:  把文件从工作区和暂存区中删除
  + 如果只是简单地从工作目录中手工删除文件，运行 git status 时就会在 Changes not staged for commit 的提示。要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除，然后提交。可以用以下命令完成此项工作 

+  `git rm -f <file>`:删除修改过并已放到暂存区的文件
  + 如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项 -f

+ `git rm --cached <file>`:把暂存区的文件删除,变成未追踪状态(保留再本地磁盘)    
  + 如果把文件从暂存区域移除，但仍然希望保留在当前工作目录中，换句话说，仅是从跟踪清单中删除，使用 `--cached` 选项即可

+ `git rm -r -n *: ??????????
  + 递归删除，即如果后面跟的是一个目录做为参数，则会递归删除整个目录中的所有子目录和文件
### 7.移动mv
+ `git mv <origin> <new>`:用于移动或重命名一个文件、目录、软连接。
  + 在工作区和暂存区移动或重命名:\<new\>为目录名,执行移动,为文件名,执行重命名
  + 重命名示例:
  + "**git  mv  a.txt   README.md**"   将a.txt修改为README.md
    + 等价于此3句
    + mv  a.txt    README.md
    + git rm  a.txt
    + git add  README.md 
### 8.撤销操作
#### 撤销提交
+ `git commit --amend`
  + 提交后,忘记要暂存一些文件,在暂存后,可以使用amend,最终你只会有一个提交 - 第二次提交将代替第一次提交的结果。
#### 取消暂存的文件
+ `git reset HEAD <file>`
当两个文件被同时暂存后,想各自独自提交,
#### 撤消对文件的修改
+ `git checkout --  <file>`
  + 这个操作会使原来的修改消失 
## 四、Git分支管理
+ branch:git分支,缺省状态下默认当前分支为'master',未合并前各个分支文件不相干
### 查看分支
+ `git branch`:列出本地分支
+ `git branch -v`:列出分支和最后一次提交
### 创建分支
+ `git branch (branch_name)`:手动创建分支,如:git branch  dev
+ `git checkout (branch_name)`:切换分支
+ `git checkout -b (branch_name)`:创建新分支并立即切换到该分支下
### 删除分支
+ `git branch -d (branch_name)`:删除本地分支
+ `git push [alias] --delete branch_name` : 删除远程分支
### 合并分支
+ `git merge branch_name`: 合并分支
+ 合并冲突:合并并不仅仅是简单的文件添加、移除的操作，Git 也会合并修改。

## 五、历史
+ `git log`:查看提交历史记录
+ `git log --oneline`:查看历史记录(简洁,主要是提交信息)
+ `git log --oneline --graph`:查看历史记录(开启了拓扑图)
+ `git log --reverse --oneline`:逆向显示所有日志
+ `git log --author=<authorname> --oneline -n`:查看指定用户的提交日志,**-n**:显示记录条数,n=1,2,3...
+ `git log --oneline --before={3.weeks.ago} --after={2019-03-01} --no-merges`:三周前且在3月1日之后的所有提交,no-merges:隐藏合并提交
+ 更多 git log 命令可查看：<http://git-scm.com/docs/git-log>
## 六、打标签tag
### 查看标签
+ `git tag`:查看所有标签
### 创建标签
#### 轻量标签
+ `git tag <标签名>`:不会记录任何信息
#### 附注标签 
+ `git tag -a <tagname> -m '注解'`: 会存储用户名,邮箱,推荐使用
  + 没有-m会打开编辑器要求你添加注释信息
  + 示例: git  tag  -a  v1.0  -m 'tag标签1.0' 
+ `git tag -s <tagname> -m '注释'`:PGP签名标签
#### 后期打标签 
+ `git tag -a <tagname> <commit号> -m '注解'`:补加上次遗漏的标签
  +  commit号:,如1d097c9,c65e2db.....  
### 删除标签
+ `git tag -d <tagname>`: 删除标签(只删除本地)
+ `git push [alias] :refs/tags/<tagname>`:更新远程仓库标签 
### 查看版本所修改的内容
+ `git show <tagname>`:查看指定版本所修改的内容
### 共享标签
默认情况下,git push 命令不会穿送标签到远程服务器上,需要自己显式推送

+ `git push [alias] [tagname]`
+ `git push [alias] --tags` : 把所有不在远程仓库 服务器上的标签全部传送到那里。
### 生成一个构建号
+ 只适用于有注解的标签（即使用 -a 或 -s 选项创建的标签），
+ `git describe master`
  + `v1.6.2-rc1-20-g8c5b85c` 
### 检出标签
## 七、远程仓库Github
### 远程仓库查看
+ `git remote`:查看当前配置有哪些远程仓库
+ `git remote -v`:显示每个别名的URL地址
+ `git remote show [alias]`:查看远程仓库[指定仓库]的信息
### 添加远程仓库
+ `git remote add [alias] [url]`:添加一个新的远程仓库
> [alias]: 远程仓库别名  [url]: 链接地址

### 推送
+ `git push [alias] [branch]`
> 将你的[branch]分支推送成为[alias]远程仓库上的[branch]分支
> [alias]:远程仓库的别名, [branch]: 分支,?????????/

### 拉取
+ `git fetch [alias]`:从远程下载最新分支与数据(本地还未同步)
+ `git merge [alias]/[branch]`:将服务器上的**更新**合并到你的当前分支
> [alias]:远程库别名, [branch]: 分支,?????

+ `git pull [alias] [branch]`:提取远程仓库到本地,自动merge
> git pull = git fetch + git merge
> `fatal: refusing to merge unrelated histories`:拒绝合并不相关的历史
> 解决方法:使用`git pull [alias] [branch] --allow-unrelated-histories`

### 删除远程仓库和重命名
+ `git remote rm [alias]`:删除远程仓库,[alias]:远程仓库的别名
+ `git remote rename 原名 新名`: 远程仓库重命名
### 克隆仓库
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

## 变基
### merge
> 主分支master,开发分支dev,现在要把dev分支合并到主分支上
> 使用 git merge dev (当前分支为master),会把dev分支合并到master
> merge将两个分支的最新快照合并

### rebase
+ `git rebase [basebranch]  [topicbranch]` : 无需切换分支
  + `git checkout topicbranch` :切换到topic分支
  + `git rebase basebranch` : 变基到主分支base上

原理:
git checkout dev (先切换为当前分支dev)
git rebase master 
找到两个分支(当前分支dev,基底分支master)的最近共同祖先,
对比当前分支相对于祖先分支的的历史提交,提取相应的修改保存为临时文件,
将当前分支指向目标基底分支
切换到基底分支(git checkout master),快进合并分支dev, git merge dev

#### 特性rebase
<img src="D:\JAVA\XielinX.github.io\git-shortcut\rebase1.png" alt="特性" title="案例图片"  />

+ 一、将 client 中的修改合并到主分支并发布，但暂时并不想合并 server 中的修改
+ `git rebase --onto master server client` 
  > 取出 client 分支，找出处于 client 分支和 server 分支的共同祖先之后的修改
  > 然 后把它们在 master 分支上重放一遍”。 这理解起来有一点复杂，不过效果非常酷。

+ 快进合并到主分支(合并client)
  + `git checkout master` (切换到master分支)
  + `git merge client` (合并client分支到master分支上去)
![rebase2](D:\JAVA\XielinX.github.io\git-shortcut\rebase2.png)

+ 合并server分支
  
  + `git rebase master server` : 省去先切换到server分支,然后rebase操作
<img src="D:\JAVA\XielinX.github.io\git-shortcut\r3.png" alt="r3" style="zoom: 80%;" />

+ 快进合并到主分支(合并server)
  + `git checkout master`
  + `git merge server`
![r4](D:\JAVA\XielinX.github.io\git-shortcut\r4.png)

#### 变基风险
+ **不要对在你的仓库外有副本的分支执行变基。** 

### git commit, git push, git pull, git fetch, git merge 含义与区别
+ `git commit`: 将本地修改过的文件提交到本地仓库去
+ `git push`: 从本地仓库的最新信息**推送**到远程仓库去
+ `git pull`: 从远程仓库**获取**最新版本到本地仓库,并自动merge
+ `git fetch`: 从远程仓库**获取**最新版本到本地仓库,不会自动merge
+ `git merge`: 用于从指定的commit(s)合并到当前分支,用来合并两个分支

##  八、生成密钥
### 创建SSH Key 
+ `ssh-keygen -t rsa -C "yourgithubemail@example.com"` :默认命名的密钥名id_rsa
+ `ssh-keygen -t rsa -C yourgithubemail@example.com -f ~/.ssh/key-name`:生成密钥名为key-name
+ `ssh -T git@github.com`:验证是否成功生成密钥
> 成功的话会在 ~/ 下生成 .ssh 文件夹，进去，打开 id_rsa.pub，复制里面的 key到github上
> `xxx.pub`:是公钥,另一个是私钥

## Git别名(快捷键)
```bash
# git co = git checkout
git config --global alias.co checkout
# git br = git branch
git config --global alias.br branch
# git ci = git commit
git config --global alias.ci commit
# git st = gir status
git config --global alias.st status
# git unstage = git reset HEAD -- 
git config --global alias.unstage 'reset HEAD --'
# git last = git log -1 HEAD
git config --global alias.last 'log -1 HEAD'
```
