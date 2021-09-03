---
title: docker命令整理
date: 2021-09-02 13:53:03
tags:
- docker
---

[菜鸟教程合集](https://www.runoob.com/docker/docker-command-manual.html)

#### 查看

```
docker ps 
docker ps -a
docker images
```

#### 运行容器

```
docker run -it --name 容要创建的容器名  -p 主机端口:容器端口 要使用的镜像名
```

```
docker run -it --name tyl_pytorch  -p 1227:22 c35f519fe286
```

- --name 容器名
- -p: 指定端口映射，格式为：主机端口:容器端口
- -it i 以交互模式运行容器 t 为容器重新分配一个伪输入终端
- -v /宿主机目录:/容器目录 将宿主机目录挂载到容器里

#### 更新容器配置

```
docker update [OPTIONS] 容器名
```

如

```
docker update --restart=always tyl-docker
```

#### 连接docker,退出docker

```
docker attach 容器名
```

在`docker`容器中使用`Ctrl+D`即可退出容器，同时停止容器

如果想退出容器但又不想让容器停止，使用`Ctrl+P+Q`即可

#### 拷贝文件:CP

```
docker cp 容器id:容器目录  外部目录
docker cp 外部目录  容器id:容器目录 
```

#### 根据容器创建镜像

```
 docker commit 容器名 要创建的镜像名
```

#### 镜像导出和导入

```
docker save 镜像名>导出文件名.tar
docker load -i 导出文件名.tar
```

```
docker save c372cb4b73cb>pytorch1.7-cuda11.0.tar
docker load -i pytorch1.7-cuda11.0.tar
```

#### 容器导出导入

导入的不是容器而是镜像，可以通过这个镜像再创建容器

```
docker export 容器名>导出文件名.tar
docker import 导出文件名.tar 创建的镜像名
```

```
docker export f81d6b9b8bd5>container_torch17.tar
docker import container_torch17.tar torch17
```





