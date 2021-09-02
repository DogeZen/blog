---
title: nps反向代理实现远程连接
date: 2021-09-02 09:33:23
tags:
- linux
---



#### 官方文档

https://ehang-io.github.io/nps

#### 服务器端

##### 1.下载并解压安装包


```
wget https://github.com/ehang-io/nps/releases/download/v0.26.10/linux_amd64_server.tar.gz
tar -zxvf linux_amd64_server.tar.gz

```

##### 2.安装并启动nps

```
sudo ./nps install
sudo nps start
```

查看启动日志

```
cat /var/log/nps.log
```

##### 3.修改配置文件并且重载

```
vim /etc/nps/conf/nps.conf
sudo nps reload
sudo nps restart
```

修改

```
bridge_port=供客户端连接的端口

web_username=网页管理端登录账号
web_password=网页管理端登录密码
web_port = 网页管理端访问端口
```

#### 网页端添加客户端

浏览器访问服务器端ip : web_port

新增客户端，设置客户端

新增tcp隧道，客户端id为新增的客户端id。

#### 客户端

```
docker pull ffdfgdfg/npc
docker run -d --name npc --net=host --restart=always ffdfgdfg/npc -server=<ip:bridge_port> -vkey=<web界面中显示的密钥> 
```

查看日志

```
docker logs npc 
```

