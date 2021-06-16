---
title: tensorrt部署整理  一 . nvidia docker安装
---

安装前确认几件事：

1.电脑显卡驱动是否安装成功

2.cuda是否安装成功: nvcc -V

### 安装docker 
1.下载安装包
`wget -qO- https://get.docker.com/ | sh`
2.使非root用户可以直接运行docker
`sudo usermod -aG docker 你的用户名`
然后退出再重新登录。
3.重新打开shell,测试安装是否成功
`docker run hello-world`
成功安装，获得以下输出

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
 
To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.
 
To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash
 
Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/
 
For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

### 安装Nvidia Docker
Ubuntu上的Docker CE可以使用Docker的官方的语句进行设置

```shell
curl https://get.docker.com | sh
sudo systemctl start docker 
sudo systemctl enable docker
```

设置稳定存储库和GPG密钥：

```shell
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)

curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -

curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```

在更新包列表后安装nvidia-docker2包（和依赖项）

```shell
sudo apt-get update
sudo apt-get install -y nvidia-docker2
```

设置默认运行时间后，重新启动Docker守护程序完成安装：

`sudo systemctl restart docker`
此时，可以通过运行基本CUDA容器来测试工作设置(这里cuda:10.1-base可以改成你自己的cuda版本)

[nvidiacuda仓库](https://hub.docker.com/r/nvidia/cuda/tags) 

