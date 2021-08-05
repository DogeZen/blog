---
title: pycharm连接docker
date: 2021-07-23 15:50:28
tags:
- 环境部署
---

1、修改docker服务文件

```
vi  /lib/systemd/system/docker.service
```

修改ExecStart这一行，把原来的前面加上#号注释掉

```
ExecStart=/usr/bin/dockerd    -H tcp://0.0.0.0:2375    -H unix:///var/run/docker.sock
```

2、重新加载配置

```
systemctl daemon-reload
```

3.重启服务

```
systemctl restart docker.service
```

4.设置pycahrm

新建编译器，选择docker，然后添加tcp连接。

![image-20210802135519727](1.png)

![image-20210802135417028](2.png)

5.docker设置ftp，用于同步项目，并且映射端口到外部

6.pycharm通过端口添加ftp，用于传输项目文件

ps:

1.如果连不上，需要配置防火墙打开2375端口。

2.不够安全，后续记得设置证书，防止被人黑了拿来挖矿。



