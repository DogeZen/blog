---
title: nvdia triton server运行pt文件
date: 2021-08-19 10:13:23
tags:
- tensorrt
- triton server
- pytorch
---

triton server默认都是tensorrt推理。

但也会出现有操作不支持，导致无法转模型为engine的情况。

可以选择直接运行pytorch的pt文件，以下为操作步骤。
## 1.pytorch后端环境编译步骤

原理是使用 pytorch C++ API运行pt文件模型。

#### 安装依赖项

```shell
apt-get install patchelf rapidjson-dev python3-dev
```

####  构建NGC 的 PyTorch 容器。

例如，构建一个使用 NGC 21.02 版本的 PyTorch 容器的后端,步骤如下:

```shell
git clone https://github.com/triton-inference-server/pytorch_backend.git
git checkout r21.02
mkdir build
cd build
cmake -DCMAKE_INSTALL_PREFIX:PATH=`pwd`/install -DTRITON_PYTORCH_DOCKER_IMAGE="nvcr.io/nvidia/pytorch:21.02-py3" ..
make install
```

以下所需的 Triton 存储库将被拉取并在构建中使用。

默认情况下，每个git分支的标签都是main。

 但以下CMake 参数可以被重新设置。

- triton-inference-server/backend: -DTRITON_BACKEND_REPO_TAG=[tag]
- triton-inference-server/core: -DTRITON_CORE_REPO_TAG=[tag]
- triton-inference-server/common: -DTRITON_COMMON_REPO_TAG=[tag]

这里如果报错cmake版本过低，则

```shell
apt remove cmake
pip install cmake --upgrade
```

编译完成后会在build文件夹里产生很多库文件，这些库文件将在后续被使用

参考自[pytorch后端配置文档](https://github.com/triton-inference-server/pytorch_backend)

## 2.使用编译后的共享库

通常情况放在triton镜像的这里

```
/opt/
tritonserver/
backends/
mybackend/
libtriton_mybackend.so
... # mybackend需要的其他文件
```

关于后端配置详细可以去看[后端配置官方文档](https://github.com/triton-inference-server/backend/blob/main/README.md#backends)

## 3.pytorch模型仓库

此时环境准备完成，准备模型仓库即可。

最简的TorchScript模型仓库如下:

```
  <模型仓库路径>/
    <模型名>/
      config.pbtxt 配置文件
      1/
        model.pt pt文件
```

model.pt需要由pth文件生成,示例如下

```python
import torch
from unet import UNet

# 读取模型
# model = UNet(n_channels=3, n_classes=1, bilinear=True)
# device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
# model = model.to(device)
# model.load_state_dict(torch.load("checkpoints/best_0.8774614781141281.pth", map_location=device))
#
# model = model.eval()

device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
model = torch.load("checkpoints/best_0.8774614781141281.pth", map_location=device)
model.to(device)
model.eval()

# 创建输入tensor
dummy_input = torch.ones(1, 3, 360, 640).to(device)
# 生成pt
trace_model = torch.jit.trace(model, dummy_input)
trace_model.save('checkpoints/model.pt')
```



最简单的config.pbtxt示例如下:

```json
  platform: "pytorch_libtorch"
  max_batch_size: 1
  input [
    {
      name: "INPUT__0"
      data_type: TYPE_FP32
      dims: [ 3, 360, 640]
    }
  ]
  output [
    {
      name: "OUTPUT__0"
      data_type: TYPE_FP32
      dims: [ 3, 360, 640 ]
    }
  ]
```
必须指定 [backend或platform](https://github.com/triton-inference-server/backend/blob/main/README.md#backends)，[max_batch_size](https://github.com/triton-inference-server/server/blob/r21.06/docs/model_configuration.md#maximum-batch-size)，[input与output](https://github.com/triton-inference-server/server/blob/r21.06/docs/model_configuration.md#inputs-and-outputs)。

详细配置可以去看 [模型配置官方文档](https://github.com/triton-inference-server/server/blob/r21.06/docs/model_configuration.md)

## 4.triton加载模型

post请求 <u>服务器ip:8000/v2/repository/models/模型名/load</u>



