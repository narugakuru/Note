# Raisei Note

#### 介绍
个人markdown学习笔记备份

#### 简易的命令行入门教程:

Git 全局设置:

```shell
git config --global user.name "narugakuru"
git config --global user.email "raraisei@foxmail.com"
```
创建 git 仓库:

```shell
mkdir note
cd note
git init 
touch README.md
git add README.md
git commit -m "first commit"
git remote add origin git@gitee.com:diaborosu/note.git
git push -u origin "master"
```

已有仓库?

```shell
cd existing_git_repo
git remote add origin git@gitee.com:diaborosu/note.git
git push -u origin "master"
```

直接拉取整个仓库

```shell
git clone git@gitee.com:diaborosu/note.git
```
