---
title: 备份与修改在github的hexo博客
---
## 思路
思路很简单，另外建个分支用来存hexo的代码。
要用的时候直接git clone下来改，然后再push回去同步就行。
## 新建分支备份hexo项目代码
```
git init 
git remote add origin git@github.com:DogeZen/DogeZen.github.io.git
git checkout -b backup_hexo
git add .
git commit -m ''
git push origin backup_hexo
```
## 修改hexo项目
```
git clone git@github.com:DogeZen/DogeZen.github.io.git
git checkout backup_hexo
git pull origin backup_hexo
```
然后修改source/_posts里的md文件即可实现新增与修改文章。

## 提交修改
### 修改完毕后先提交静态页面
```
hexo generate
hexo deploy
```
### 再提交hexo项目更改
```
git add .
git commit -m ''
git push origin backup_hexo
```

