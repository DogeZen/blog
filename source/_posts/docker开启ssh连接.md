---
title: docker开启ssh连接
date: 2021-09-02 17:08:40
tags:
- docker
- linux
---

#### 1.创建docker

创建docker时把22端口绑定到宿主机端口

```
docker run -it --name 容要创建的容器名  -p 主机端口:容器端口 要使用的镜像名
```

#### 2.安装docker的ssh

更新源

```
apt-get update
```

安装ssh-server

```
apt install openssh-server
```

#### 3.设置ssh，允许root登录

即往/etc/ssh/sshd_config添加一行PermitRootLogin yes

然后重启ssh服务

```
echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
service ssh restart
```

#### 4.初始化root密码

```
passwd root
```

