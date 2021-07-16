---
title: tensorrt部署整理二.全流程总结
tags: 
- tensorrt 
- 环境部署
---

## tensorrt应用结构

nvidia官方的推理服务分为两个部分，一个是server，一个是client。

**server**用于放在gpu服务器。

部署engine，接收识别用的矩阵，返回推理结果矩阵。

**client**用于放在web应用服务器。

用于接收web请求，对请求中的数据进行预处理，发送给server推断，再将推断结果后处理后返回给请求者。

## 1.安装和部署server

[server/quickstart.md at main · triton-inference-server/server (github.com)](https://github.com/triton-inference-server/server/blob/main/docs/quickstart.md#install-triton-docker-image)

主要是docker的部署。

## 2.编写tensorrt模型

[Developer Guide :: NVIDIA Deep Learning TensorRT Documentation](https://docs.nvidia.com/deeplearning/tensorrt/developer-guide/index.html#overview)

建议参考tensorrtx这个项目，里面有各类主流模型的tensorrt项目。

[wang-xinyu/tensorrtx: Implementation of popular deep learning networks with TensorRT network definition API (github.com)](https://github.com/wang-xinyu/tensorrtx)

## 3.部署模型

[server/extension_model_repository.md at main · triton-inference-server/server (github.com)](https://github.com/triton-inference-server/server/blob/main/docs/protocol/extension_model_repository.md)

很简单

通过http请求查询模型状态，以及部署模型。

## 4.triton client编写

官方提供的一些简单例子

[client/src/python/examples at main · triton-inference-server/client (github.com)](https://github.com/triton-inference-server/client/tree/main/src/python/examples)

我实际写的例子

[tensorrt_client · 柴犬/unet项目全流程 – 码云 – 开源中国 (gitee.com)](https://gitee.com/doge_ac_cn/unet/tree/master/tensorrt_client)