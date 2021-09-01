---
title: autossh反向代理实现远程开发
date: 2021-09-01 16:32:38
tags:
- linux
---

实验室有一台内网服务器，但是没有公网IP。

于是用有公网ip的服务器做反向代理，实现内网穿透。

通过公网服务器连接实验室的服务器，实现在宿舍就能跑代码。

步骤如下:

#### 1.公网服务器打开 GatewayPort

```shell
sudo vi /etc/ssh/sshd_config
```

将其中的

```
#GatewayPort no
```

设置 为

```
GatewayPort yes
```

重启sshd服务

```shell
systemctl restart sshd
```

#### 2.设置内网主机autossh

```shell
sudo apt install autossh
```



```shell
autossh -M 6666 -NR 6666:localhost:22 username@xxx.xxx.xxx.xxx -p 22
```

如:

```shell
autossh -M 6666 -NR 6666:localhost:22 root@119.45.239.194 -p 22
```

- -M 6666 定期检查内网主机的6666端口，连接出问题了会自动重连
- -N意思是不执行远程命令
- -R 6666:localhost:22 通过公网主机的6666端口可以访问内网主机的22端口
- username@xxx.xxx.xxx.xxx -p 22 公网主机的ip,用户名和ssh端口号

#### 3. 设置ssh免密登录

用来方便重连时不用输入账号密码

#### 4.内网主机开机自启动

使用systemctl来注册一个能够开机自动启动的服务。

创建文件

```shell
sudo vim /lib/systemd/system/autossh.service
```

编写内容

```shell
Description=Auto SSH Tunnel
After=network-online.target
[Service]
User=本地服务器用户名
Type=simple
ExecStart=/usr/bin/autossh -NR 在公网服务器开启的端口:本地ip地址:本地端口 -i 内网服务器用户的id_rsa绝对路径 公网服务器用户@公网服务器ip地址 -p 公网服务器端口 >> /dev/null 2>&1
ExecReload=/bin/kill -HUP $MAINPID
ExecStop=/bin/kill -TERM $MAINPID
KillMode=process
Restart=no
[Install]
WantedBy=multi-user.target
WantedBy=graphical.target
```

启动服务

```shell
systemctl enable autossh 
systemctl start autossh
```

#### 5.远程连接

直接ssh连公网主机的指定端口，用户名是内网主机的
