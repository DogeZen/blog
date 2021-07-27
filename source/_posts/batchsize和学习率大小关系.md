---
title: batchsize和学习率大小关系
date: 2021-07-27 15:58:20
tags:
- 深度学习
---

很多时候运行的项目，都是多卡训练出的，batchsize较大。

在调小batchsize的同时，要记得调小学习率。

[参考论文:One weird trick for parallelizing convolutional neural networks](https://arxiv.org/pdf/1404.5997.pdf)
具体可看第5页中。

一般来说，我们batch size 大一些，则learning rate也要大一些

batch-size增大K倍，然后学习率增大$\sqrt{K}$倍

举例来说，原先别人的batch size 为128， learning rate为0.0005

 那么当我们把batch size改为1024时，则新的学习率有这样的推荐值：

$0.0005 ∗ \sqrt{1024 / 128} = 0.0005 ∗ \sqrt{8} = 0.001412 $



