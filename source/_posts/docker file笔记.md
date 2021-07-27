---
title: docker file 笔记
date: 2021-07-27 17:58:00
tags: docker
---

## docker file 使用示例

docker build命令用于从Dockerfile构建映像。

**-f :**指定要使用的Dockerfile路径；

**--tag, -t:** 镜像的名字及标签;

示例:

```
docker build -f /path/to/a/Dockerfile
docker build -t SOLO docker/
```

## docker file结构

### **FROM：指定基础镜像，必须为第一个命令**

```
FROM pytorch/pytorch:1.3-cuda10.1-cudnn7-devel
```

### **MAINTAINER: 维护者信息**

```
MAINTAINER Dodge <83942052@qq.com>
```

### **RUN：构建镜像时执行的命令**

**1.shell执行**

```
RUN apk update
```

**2.exec执行**

```
RUN ["/etc/execfile", "arg1", "arg1"]
```

注:RUN指令创建的中间镜像会被缓存，并会在下次构建中使用。
如果不想使用这些缓存镜像，可以在构建时指定--no-cache参数，如：docker build --no-cache

### **ADD：将本地文件添加到容器中**

```
ADD hom* /mydir/          # 添加所有以"hom"开头的文件
```

将本地文件添加到容器中，tar类型文件会自动解压(网络压缩资源不会被解压)，可以访问网络资源，类似wget

**copy与add完全一致**，但是不能使用网络资源

### **CMD：容器启动时调用的命令。**

```
CMD ["/usr/bin/wc","--help"]
```

### **EXPOSE：指定于外界交互的端口**

```
EXPOSE 8080
```

### **WORKDIR：工作目录**

```
WORKDIR /path/to/workdir
通过WORKDIR设置工作目录后，Dockerfile中其后的命令RUN、CMD、ENTRYPOINT、ADD、COPY等命令都会在该目录下执行。
在使用docker run运行容器时，可以通过-w参数覆盖构建时所设置的工作目录。
```

### **ARG：用于指定传递给构建运行时的变量**

```
ARG PYTORCH="1.3"
ARG CUDA="10.1"
ARG CUDNN="7"

FROM pytorch/pytorch:${PYTORCH}-cuda${CUDA}-cudnn${CUDNN}-devel
```

## 示例

```
ARG PYTORCH="1.3"
ARG CUDA="10.1"
ARG CUDNN="7"

FROM pytorch/pytorch:${PYTORCH}-cuda${CUDA}-cudnn${CUDNN}-devel

ENV TORCH_CUDA_ARCH_LIST="6.0 6.1 7.0+PTX"
ENV TORCH_NVCC_FLAGS="-Xfatbin -compress-all"
ENV CMAKE_PREFIX_PATH="$(dirname $(which conda))/../"

RUN apt-get update && apt-get install -y libglib2.0-0 libsm6 libxrender-dev libxext6 \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# Install SOLO
RUN conda install cython -y && conda clean --all
RUN git clone https://gitee.com/doge_ac_cn/SOLO /SOLO
WORKDIR /SOLO
RUN pip install --no-cache-dir -e .
```