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


![]()

加载个人及系统配置文件用了 2790 毫秒。  
(base) PS E:\Note\Paper\2024年\attachments> picgo upload Pasted image 20241128103941.png  
[PicGo WARN]: E:\Note\Paper\2024年\attachments\Pasted does not exist.  
[PicGo WARN]: E:\Note\Paper\2024年\attachments\image does not exist.  
[PicGo WARN]: E:\Note\Paper\2024年\attachments\20241128103941.png does not exist.  
[PicGo ERROR]: Error: image not found in clipboard  
at $t.upload (E:\Environment\node-v18.20.4\node_modules\picgo\dist\index.cjs.js:1:75025)  
at process.processTicksAndRejections (node:internal/process/task_queues:95:5)  
at async E:\Environment\node-v18.20.4\node_modules\picgo\dist\index.cjs.js:1:5821  
(base) PS E:\Note\Paper\2024年\attachments>

