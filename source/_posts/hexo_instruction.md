---
title: github创建hexo博客
date: 2021-06-15 07:00:00
tags: hexo搭建
---

## hexo配置

### nodejs 安装
nodejs.org 下载安装nodejs。


### npm换源

```
npm config set registry https://registry.npm.taobao.org
```

### 安装hexo

``` 
npm install -g hexo
```

## 初始化博客
```
hexo init dogeblog		# 初始化创建，会再桌面创建test文件夹
cd dogeblog				# 进入test目录
npm install			# 进一步安装hexo所需文件
hexo clean			# 清除所有记录
hexo generate		# 生成静态网页
```

## 上传git


### 安装hexo的git插件
```
npm install hexo-deployer-git --save
```
### 新建仓库
先在git新建一个仓库，名字为你的git用户名.github.io

### 修改当前项目下的_config.yml
```
deploy:
type: git					# type为git
repo: https://github.com/DogeZen/DogeZen.github.io	# 仓库的URL
```
## 上传博客
```
hexo deploy
```

此时博客已经上传完成
你的git用户名.github.io 即可访问你的博客