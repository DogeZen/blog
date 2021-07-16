---
title: git 操作备忘录
tags: git
---



```shell
1.创建项目
git init
将此目录转换为git可管理的仓库
git config –global user.name “xx” 
git config –global user.email “xx”（都配也行）
git add.文件添加到暂存区(stage)
git commit - m “xxx” 文件提交至本地仓库

2.上传更改

git status
git add.
git commit - m ""
git push origin master
origin是远程主机，master表示是远程服务器上的master分支，分支名是可以修改的

3.下拉项目

git fetch
git merge
或
git pull origin master

git pull = git fetch + git merge

4.分支操作

git branch // 查看本地所有分支

git branch - r // 查看远程所有分支

git branch - a // 查看本地和远程的所有分支

git branch < branchname > // 新建分支

git branch - d < branchname > // 删除本地分支

git branch - d - r < branchname > // 删除远程分支，删除后还需推送到服务器
git push origin: < branchname > // 删除后推送至服务器

git branch - m < oldbranch > < newbranch > // 重命名本地分支

5.查看历史版本
git log
```

