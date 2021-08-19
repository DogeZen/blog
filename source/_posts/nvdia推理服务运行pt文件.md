---
title: nvdia triton server运行pt文件
date: 2021-08-19 10:13:23
tags:
- tensorrt
- triton server
- pytorch
---

triton server默认都是tensorrt推理，但也会出现有操作不支持，导致无法转模型为engine的情况。

此时可以直接运行pytorch的pt文件。

### pytorch模型仓库结构

pytorch模型必须命名为model.pt。

这个默认名可以在模型配置文件config.pbtxt里更改。

不同版本的pytorch可能不互相适配，因为底层操作会不同。

最简的TorchScript模型仓库如下:

```
  <模型仓库路径>/
    <模型名>/
      config.pbtxt 配置文件
      1/
        model.pt pt文件
```

详细可以去看官方文档[模型仓库配置](https://github.com/triton-inference-server/server/blob/r21.06/docs/model_repository.md)

### 最简的配置文件config.pbtxt

除了tensorrt模型可以自动获取配置，其他的都需要指定模型配置文件。

必须指定 [平台或者后端](https://github.com/triton-inference-server/backend/blob/main/README.md#backends)，最大batchsize，输入输出tensor。

示例如下:

```
  platform: "pytorch"
  max_batch_size: 8
  input [
    {
      name: "input0"
      data_type: TYPE_FP32
      dims: [ 3,360,640]
    }
  ]
  output [
    {
      name: "output0"
      data_type: TYPE_FP32
      dims: [ 3,360,640 ]
    }
  ]
```
详细配置可以去看 [模型配置官方文档](https://github.com/triton-inference-server/server/blob/r21.06/docs/model_configuration.md)

### pytorch后端环境编译步骤

原理是使用 pytorch C++ API运行pt文件模型。

#### 安装依赖项

```shell
apt-get install patchelf rapidjson-dev python3-dev
```

####  构建NGC 的 PyTorch 容器。

例如，构建一个使用 NGC 21.02 版本的 PyTorch 容器的后端,步骤如下:

```
mkdir build
cd build
cmake -DCMAKE_INSTALL_PREFIX:PATH=`pwd`/install -DTRITON_PYTORCH_DOCKER_IMAGE="nvcr.io/nvidia/pytorch:21.02-py3" ..
make install
```

以下所需的 Triton 存储库将被拉取并在构建中使用。

默认情况下，每个repo的标签都是main， 但以下CMake 参数可以被重设。

- triton-inference-server/backend: -DTRITON_BACKEND_REPO_TAG=[tag]
- triton-inference-server/core: -DTRITON_CORE_REPO_TAG=[tag]
- triton-inference-server/common: -DTRITON_COMMON_REPO_TAG=[tag]

#### 使用自定义 PyTorch 构建 PyTorch 后端

Triton的PyTorch后端，使用特别修改版本的PyTorch。

这些PyTorch版本的完整源代码和Docker镜像，都可以从NGC中获取。

例如，与Triton的21.02版本兼容的PyTorch版本，为nvcr.io/nvidia/pytorch:21.02-py3。
将 [PyTorch NGC 容器](https://ngc.nvidia.com/catalog/containers/nvidia:pytorch)中的LibTorch和Torchvision头文件和库复制到本地目录。

你可以从docker中看到哪些头文件和库是需要/复制的。

```
$ mkdir build
$ cd build
$ cmake -DCMAKE_INSTALL_PREFIX:PATH=`pwd`/install -DTRITON_PYTORCH_INCLUDE_PATHS="<PATH_PREFIX>/torch;<PATH_PREFIX>/torch/torch/csrc/api/include;<PATH_PREFIX>/torchvision" -DTRITON_PYTORCH_LIB_PATHS="<LIB_PATH_PREFIX>" ..
$ make install

```

这里如果报错cmake版本过低，则

```
apt remove cmake
pip install cmake --upgrade
```

参考自[pytorch后端配置文档](https://github.com/triton-inference-server/pytorch_backend)

关于后端配置详细可以去看[后端配置官方文档](https://github.com/triton-inference-server/backend/blob/main/README.md#backends)

