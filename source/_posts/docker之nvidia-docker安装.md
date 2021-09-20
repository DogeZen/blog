---
title: docker之nvidia-docker安装
date: 2021-09-02 18:05:11
tags:
- docker
---

[官方文档](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker)

### 前期准备

#### nvidia 显卡驱动安装

有图形界面直接软件更新->附加驱动 就能找到nvidia驱动安装

无图形界面需要去[下载run文件自行安装]([https://www.nvidia.com/Download/index.aspx?lang=en-us)，这里我有图形界面所以不去搞了。

#### docker安装

##### 下载并安装
```
wget -qO- https://get.docker.com/ | sh
```
##### 使非root用户可以直接运行docker
``` 
sudo usermod -aG docker 你的用户名
```
##### 测试安装是否成功
```
docker run hello-world
```
#### 显卡要求

显卡必须是nvdia且高于一定算力

### 安装

Ubuntu上的Docker CE可以使用Docker的官方的语句进行设置

```
curl https://get.docker.com | sh
sudo systemctl start docker
sudo systemctl enable docker
```

设置稳定存储库和GPG密钥：

```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
   && curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add - \
   && curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```
在更新包列表后安装nvidia-docker2包（和依赖项）
```
sudo apt-get update

sudo apt-get install -y nvidia-docker2
```

设置默认运行时间后，重新启动Docker守护程序完成安装：

```
sudo systemctl restart docker
```

去[nvidia cuda仓库](https://hub.docker.com/r/nvidia/cuda/tags)下载最新的docker 并测试nvidia-smi

```
sudo docker run --rm --gpus all --name tyl-docker  nvidia/cuda:11.4.1-devel-ubuntu18.04
```

如果需要pytorch的docker，可以直接去[PyTorch | NVIDIA NGC](https://ngc.nvidia.com/catalog/containers/nvidia:pytorch/tags)

