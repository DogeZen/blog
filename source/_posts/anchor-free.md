---
title: anchor_free
date: 2021-09-18 10:55:18
tags:
- 深度学习
- 神经网络
---

## 文章

anchor 文章

[目标检测中的Anchor - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/55824651)

[锚框：Anchor box综述 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/63024247)

anchor-free 文章

[目标检测：Anchor-Free时代 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/62103812)

[物体检测的轮回: anchor-based 与 anchor-free - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/62372897)



## 论文

### yolov1

论文原文：https://arxiv.org/pdf/1506.02640.pdf

#### 输出矩阵的构成

![image-20210918165857975](image-20210918165857975.png)

7 * 7 * 30 = 7 * 7 * ( 20 + 2 * 5 ) 

7 分割成7×7的网格

2 一个格子会预测两个框

20 预测的种类数量

5 是x,y,w,h,是否存在物体的置信度

#### loss结构

![image-20210918165753432](image-20210918165753432.png)

7 * 7个格子，每个格子两个框的loss

1.每个框的中心点坐标与宽高loss

2.每个**预测对**的框内部是否存在物体的loss

3.每个**预测错**的框内部是否存在物体的loss 

### Fcos 
[FCOS:一阶全卷积目标检测 - 知乎 (zhihu.com) ](https://zhuanlan.zhihu.com/p/63868458)   
[论文](https://arxiv.org/pdf/1904.01355.pdf)

#### 1.backbone

fpn的backbone

#### 2.head

对fpn提取到的特征图，对特征图上每个点都进行预测。

预测包括类别信息和坐标信息。

其中类别信息为每个类的置信度,坐标信息为和中心点的左右上下偏离坐标。

即最终预测结果为 特征图大小 * (类别数 + 4 )。

#### 3.centerness loss 过滤低质量预测框

![image-20210918162310459](image-20210918162310459.png)

因为特征图上每个点都会进行预测，会产生很多偏离中心的低质量预测框，比如上面的蓝色框。

于是设计了一个loss用于表示与中心的偏离程度,即centerness(中心度)。
在预测时，centerness loss 与分类置信度相乘。使偏离中心的预测结果,置信度降低,被过滤掉。
$$
centerness = \sqrt{\frac{min(l,r)}{max(l,r)}*\frac{min(t,b)}{max(t,b)}}
$$





