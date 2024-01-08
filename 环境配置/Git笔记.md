---
tags:
  - git
---

Git 全局设置:

```shell
git config --global user.name "narugakuru"
git config --global user.email "raraisei@foxmail.com"
git config --global user.email "2718968976@qq.com"

git config --global http.proxy http://127.0.0.1:8848
git config --global https.proxy http://127.0.0.1:8848
```

# 配置SSH密钥

ssh-keygen -t rsa -C "raraisei@foxmail.com"
将ssh key添加到GitHub中
用自己喜欢的文本编辑器打开`id_rsa.pub`公钥文件，里面的信息即为SSH key，将这些信息复制到GitHub的`Add SSH key`页面即可

# 出现警告
```bash
warning: LF will be replaced by CRLF in <file-name>.
The file will have its original line endings in your working directory.
```
配置git config --global core.autocrlf true
可适配win，linux，mac三个平台


# clone报错
网络无法访问github，但git没有走代理，手动配置代理
![image.png](https://cdn.jsdelivr.net/gh/narugakuru/images/img/20230223212033.png)
查看配置
git config --global -l

#代理 
# 配置clash代理
```shell
git config --global http.proxy http://127.0.0.1:8848
git config --global https.proxy http://127.0.0.1:8848
```
取消代理
```shell
git config --global --unset http.proxy
git config --global --unset https.proxy
```

设置完之后可以使用这个命令查看 Git 的配置。

```bash
git config --list
```

想退出查看界面的话就按下 Q 键。
当然还可以使用这个命令直接编辑 Git 的配置文件。

```bash
git config --global --edit
```


# 删除已经push的文件

```shell
（1）预览将要删除的文件（如果不清楚该目录下是否存在不应该删除的文件）

git rm -r -n --cached 文件/文件夹名称

加上 -n 这个参数，执行命令时，是不会删除任何文件，而是展示此命令要删除的文件列表预览。

（2）确定无误后删除文件（不会删除本地的文件或文件夹）

git rm -r --cached 文件/文件夹名称

（3）提交到本地并推送到远程服务器

git commit -m "提交说明"
git push origin master
```


# Git基础
## push仓库
1. git init //初始化
2. git remote add origin “URL”//设置URL
3. git pull origin master //拉取仓库
4. git add . //添加文件
5. git commit -m "第一次提交" //注释
6. git push origin master //推送数据

## 常用命令
查看配置
	Git status //查看文件状态
	Git remote set-url origin 仓库地址
	Cat .git/config 配置状态
查看日志
	git log --oneline 
	git log --pretty=oneline
	git reflog 最常用
版本回溯
	git reset --hard HEAD值  //全回溯
	git reset --mixed HEAD值
	git reset --soft HEAD值
	git diff 对比文件//暂存区工作区
	git diff HEAD值 文件名 //暂存区本地库
SSH
	生成SSH密匙
	ssh-keygen -t rsa
	公匙id_ras.pub
更新仓库
	一.更新本地仓库
	1.进入仓库文件夹，手动加入新文件，然后用 git add 文件名 加入到缓存区
	2.用git commit -m “提交信息” 命令 将缓存区里面的内容加入到本地仓库中
	二.将本地仓库更新到远程仓库
	1.进行这个步骤之前要完成前面的步骤
	2.关联到远程仓库：git remote add origin 地址
	3.将远程库与本地同步合并：git pull --rebase origin master
	4.将本地库的内容推送到远程，使用git push 命令：git push -u origin master
	*状态查询命令：git status
仓库基本管理
	初始化一个Git仓库(以/home/gitee/test文件夹为例)
	$ cd /home/gitee/test    # 进入git文件夹
	$ git init               # 初始化一个Git仓库
将文件添加到Git的暂存区
	$ git add "readme.txt"  //git checkout
	注：使用git add -A或git add . 可以提交当前仓库的所有改动。
	查看仓库当前文件提交状态（A：提交成功；AM：文件在添加到缓存之后又有改动）
	$ git status -s
从Git的暂存区提交版本到仓库，参数-m后为当次提交的备注信息
	$ git commit -m "1.0.0"   //git reset
将本地的Git仓库信息推送上传到服务器
	$ git push https://gitee.com/***/test.git
查看git提交的日志
	$ git log


## 远程仓库管理
修改仓库名
	一般来讲，默认情况下，在执行clone或者其他操作时，仓库名都是 origin 如果说我们想给他改改名字，比如我不喜欢origin这个名字，想改为 oschina 那么就要在仓库目录下执行命令:
	git remote rename origin oschina
	这样 你的远程仓库名字就改成了oschina，同样，以后推送时执行的命令就不再是 git push origin master 而是 git push oschina master 拉取也是一样的
添加一个仓库
	在不执行克隆操作时，如果想将一个远程仓库添加到本地的仓库中，可以执行
	git remote add origin  仓库地址
	注意: 1.origin是你的仓库的别名 可以随便改，但请务必不要与已有的仓库别名冲突 2. 仓库地址一般来讲支持 http/https/ssh/git协议，其他协议地址请勿添加
查看当前仓库对应的远程仓库地址
	git remote -v
	这条命令能显示你当前仓库中已经添加了的仓库名和对应的仓库地址，通常来讲，会有两条一模一样的记录，分别是fetch和push，其中fetch是用来从远程同步 push是用来推送到远程
修改仓库对应的远程仓库地址
	git remote set-url origin 仓库地址

○ 克隆(clone)：从远程仓库URL加载创建一个与远程仓库一样的本地仓库
○ 提交(commit)：将暂存文件上传到本地仓库（我们在Finder中对本地仓库做修改后一般都得先提交一次，再推送）
○ 检出(checkout)：切换不同分支
○ 添加（add）：添加文件到缓存区
○ 移除（remove）：移除文件至缓存区
○ 暂存(git stash)：保存工作现场
○ 重置(reset)：回到最近添加(add)/提交(commit)状态
○ 合并(merge)：将多个同名文件合并为一个文件，该文件包含多个同名文件的所有内容，相同内容抵消
○ 抓取(fetch)：从远程仓库获取信息并同步至本地仓库
○ 拉取(pull)：从远程仓库获取信息并同步至本地仓库，并且自动执行合并（merge）操作，即 pull=fetch+merge
○ 推送(push)：将本地仓库同步至远程仓库，一般推送（push）前先拉取（pull）一次，确保一致
○ 分支(branch)：创建/修改/删除分枝
○ 标签(tag):给项目增添标签
○ 工作流(Git Flow):团队工作时，每个人创建属于自己的分枝（branch），确定无误后提交到master分枝
终端(terminal):可以输入git命令行


## Git配置文件规范
![image.png](https://cdn.jsdelivr.net/gh/narugakuru/images/img/20230105210043.png)



## git  rm 删除命令
[原文链接](https://www.cnblogs.com/syq816/p/9301700.html)

`用法：git` `rm` `[<选项>] [--] <文件>...`
- `git` `rm` `-h` 提示
- `-n, --dry-run         演习`
- `-q, --quiet           不列出删除的文件`
- `--cached              只从索引区删除`
- `-f, --force           忽略文件更新状态检查`
- `-r                    允许递归删除文件夹`
- `--ignore-unmatch      即使没有匹配，也以零状态退出`


## 常规更新
$ git add .
$ git commit -m "第一次提交"
$ git push origin master

### 方法1
	先将仓库clone到本地，修改后再push到 Gitee 的仓库仓库

$ git clone https://gitee.com/用户个性地址/HelloGitee.git # 将远程仓库克隆到本地
在克隆过程中，如果仓库是一个私有仓库，将会要求用户输入 Gitee 的账号和密码。按照提示输入即可。
当然，用户也可以通过配置本地的git配置信息，执行git config命令预先配置好相关的用户信息，配置执行如下：
$ git config --global user.name "你的名字或昵称"
$ git config --global user.email "你的邮箱"
	在 Gitee 平台中，强烈建议您在【设置-多邮箱管理】中的“提交邮箱”与上面配置信息中的邮箱地址保持一致，这样平台就能及时地统计您在平台中提交代码的贡献了。
修改代码后，在仓库目录下执行下面命令
$ git add . # 将当前目录所有文件添加到git暂存区
$ git commit -m "my first commit" # 提交并备注提交信息
$ git push origin master # 将本地提交推送到远程仓库

### 方法2
	本地初始化一个仓库，设置远程仓库地址后再做push

和方法1的差别，在于先创建仓库。
$ git init 
$ git remote add origin https://gitee.com/用户个性地址/HelloGitee.git
这样就完成了版本的一次初始化。
接下去，进入你已经初始化好的或者克隆仓库的目录,然后执行：
$ git pull origin master
修改/添加文件，否则与原文件相比就没有变动。
$ git add .
$ git commit -m "第一次提交"
$ git push origin master
然后如果需要账号密码的话就输入账号密码，这样就完成了一次提交。此时，你可以在你的个人面板、仓库主页查看到你的提交记录。
在新建仓库时，如果在 Gitee 平台仓库上已经存在 readme 或其他文件，在提交时可能会存在冲突，这时用户需要选择的是保留线上的文件或者舍弃线上的文件，如果您舍弃线上的文件，则在推送时选择强制推送，强制推送需要执行下面的命令(默认不推荐该行为)：
$ git push origin master -f
如果您选择保留线上的 readme 文件,则需要先执行：
$ git pull origin master

# 各类报错

## 仓库的灰色文件夹
无法点击的灰色文件夹中含有 .git 文件
即在本地初始化的仓库(使用 git init的文件夹) 中的某一个文件夹里含有 .git 文件 （有点绕口）
这里就是echecs文件夹和echecs_web_services文件夹中原本就含有.git文件,即之前别人使用过git提交代码遗留下来的.git文件

解决方法 :
删除缓存:
git rm -r --cached "灰色文件夹的名称"            
操作后gitee上的灰色文件夹被移除
这里就是
git rm -r --cached "echecs"
git rm -r --cached "echecs_web_services"
然后在本地找到灰色文件夹中原本的.git文件手动删掉,注意显示隐藏文件去找
然后重新 git add,commit,push 提交代码

## push报错

![](https://cdn.jsdelivr.net/gh/narugakuru/images/img/20230105195745.png)

出现错误的主要原因是：oschina中的README.md文件不在本地代码目录中
解决方法：合并远程与本地分支
git pull --rebase origin master

![](https://cdn.jsdelivr.net/gh/narugakuru/images/img/image-20230105202425830.png)

此时再执行语句 git push -u origin master即可完成代码上传到oschina

![image-20230105202745674](https://cdn.jsdelivr.net/gh/narugakuru/images/img/image-20230105202745674.png)




# 仓库分支

## 一 ：查看远程分支   
git branch  或者 git branch -r

## 二 ：拉取远程分支到本地

公式 1： git checkout -b 本地分支名 origin/远程分支名
（推荐使用）：原因：可以直接跳转到分支
举例子 介绍 看的会更清楚：
我们要把远程分支stable-1.1.10 拉取到本地
如果你拉取后再次拉取：就会提示已经存在
 git  checkout -b stable origin/stable-1.1.10

公式 2：  git fetch origin 远程分支名 ： 本地分支名
公式2 只能在本地新建分支，但是不会自动切换到该本地分支，需要手动checkout

## 三：切换 分支  和  查看当前所在分支
1. 切换分支公式： git checkout 分支名称

2.查看当前所在分支：git branch             (当前分支会有 *，并高亮显示  比如下面的  dev)

## 四：查看历史:   history

## 五：查看要提交的文件     git status 

## 六：提交文件 + 注释

## 七：推送到远程分支

公式： git push origin  本地分支名 ： 远程分支名

git push origin ：stable  意思是：上传本地当前分支代码到stable分支  （stable是分支名称）

## 八：更新项目   步骤
1.可以先执行git stash 将本地修改保存起来。 这样本地就干净了。git status后看不见修改的文件 
2.然后 git pull 拉取文件 
3.git stash pop恢复最新的进度到工作区。git默认会把工作区和暂存区的改动都恢复到工作区。git status后就可以看见之前修改的文件


# 忽略设置.gitignore


已经commit了，再加入gitignore是无效的，所以需要删除下缓存  
                        git rm -r --cached ignore_file  

注意： .gitignore只能忽略那些原来没有被track的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的。

    正确的做法是在每个clone下来的仓库中手动设置不要检查特定文件的更改情况。  
    git update-index --assume-unchanged PATH    在PATH处输入要忽略的文件。  
  
    另外 git 还提供了另一种 exclude 的方式来做同样的事情，不同的是 .gitignore 这个文件本身会提交到版本库中去。用来保存的是公共的需要排除的文件。而 .git/info/exclude 这里设置的则是你自己本地需要排除的文件。 他不会影响到其他人。也不会提交到版本库中去。  
  
    .gitignore 还有个有意思的小功能， 一个空的 .gitignore 文件 可以当作是一个 placeholder 。当你需要为项目创建一个空的 log 目录时， 这就变的很有用。 你可以创建一个 log 目录 在里面放置一个空的 .gitignore 文件。这样当你 clone 这个 repo 的时候 git 会自动的创建好一个空的 log 目录了。

# 获得一个新库

提供一个 在不删除原仓库的前提下，清除原仓库的所有历史提交记录(包含：分支、tag) 解决方案。
清除 git 所有历史提交记录方案
## 1.创建新分支

语法：git checkout --orphan <new_branch>

使用 --orphan 选项，可创建1个"清洁"分支(无任何的提交历史，但是当前分支的内容一应俱全。但严格意义上说，这样创建的分支还不是一个真正的分支，因为HEAD指向的引用中没有commit值，只有在进行一次提交后，它才算得上真正的分支。

注意：

新的分支名可以随意命名，但不能和以前的分支名冲突。这儿特别强调是因为很多人习惯默认将分支名创建为 master.
本文以 latest_branch 作为新分支名，这个名称没有任何特殊含义，你可自定义，只要保证和以后的使用一致即可。

## 2.添加所有文件

git add .
或 git add -A

## 3.commit代码

git commit -m "自定义提交说明"

## 4.删除原来的主分支(master)

git branch -D master

一般仓库默认的主分支为 master 分支，如果原来的主分支不是 master, 用实际的主分支名代替。

## 5.把当前分支重命名为master

 git branch -m master

## 6.最后把代码推送到远程仓库

注意： 有些仓库有 master 分支保护，不允许强制 push，需要在远程仓库项目里暂时把项目保护关掉才能推送。

git push -f origin master

注意： 推送前 需要使用 git remote -v 查看关联的远程仓库的信息（主要是远程库的别名）。虽然远程库的别名默认是 origin ,但你可能设置过其他的别名（而非 origin）.

推送前，有的情况需要设置：git branch --set-upstream-to=origin/master master。

## 7.从远程库拉取更新代码(测试)

git pull

## 8.确定清除历史记录的结果

1.查看提交日志
git log --pretty=oneline

2.查看分支信息
列出所有本地分支
$ git branch
列出所有远程分支
$ git branch -r
列出所有本地分支和远程分支
$ git branch -a

3.查看 tag 信息
查看本地标签
git tag
查看远程标签
git ls-remote --tags

可登录远程仓库再次确认。

## 清除 git 所有记录的 bat 批处理脚本
这儿将上面的步骤封装为 bat 批处脚本(针对windows)，双击即可运行。
文件名：fetch_push_clear_all_history.bat
将文本内容保存为 UTF-8 格式，文件最好放在 git 仓库外。如果放在 git 仓库内，需要将此文件在 .gitignore 中过滤。

```shell
chcp 65001
@echo off
set /p gd=输入要清除历史提交信息的仓库目录的绝对路径:
echo 待处理的路径：%gd%
set /p gm=输入提交说明：
pushd
cd /d %gd%
git checkout --orphan latest_branch
git add -A
git commit -am "%gm%"
git branch -D master
git branch -m master
git push -f origin master
git pull
echo "已清除全部的历史记录!"
echo "查看新仓库信息："
git log --pretty=oneline
git branch -a
git tag
git ls-remote --tags
pause
popd
exit
```




![image.png](https://cdn.jsdelivr.net/gh/narugakuru/images/img/20230105205701.png)