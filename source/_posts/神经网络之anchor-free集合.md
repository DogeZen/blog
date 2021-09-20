---
title: 神经网络之anchor-free集合
date: 2021-09-18 10:55:18
tags:
- 深度学习
- 神经网络
---

# 文章

anchor 文章

[目标检测中的Anchor - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/55824651)

[锚框：Anchor box综述 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/63024247)

anchor-free 文章

[目标检测：Anchor-Free时代 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/62103812)

[物体检测的轮回: anchor-based 与 anchor-free - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/62372897)



# yolov1

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



# RetinaNet （focal loss)

未完全看完

[RetinaNet(Focal Loss) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/59910080)

#### 场景

one stage 的神经网络训练过程中类别失衡，即简单负样本数量过多。

会有置信度很低，但是数量很多的目标，在loss中占比很大。

从而使得那些数量很少但是置信度很高的识别结果，在反向传播时起到很小的作用。影响整体收敛。

#### focal loss

![[公式]](https://www.zhihu.com/equation?tex=FL%28p_t%29%3D%5Calpha_t%281-p_t%29%5E%5Cgamma+log%28p_t%29)

![[公式]](https://www.zhihu.com/equation?tex=p_t) 是不同类别的分类概率， ![[公式]](https://www.zhihu.com/equation?tex=%5Cgamma) 是个大于0的值， ![[公式]](https://www.zhihu.com/equation?tex=%5Calpha_t) 是个[0，1]间的小数， ![[公式]](https://www.zhihu.com/equation?tex=%5Cgamma) 和 ![[公式]](https://www.zhihu.com/equation?tex=%5Calpha_t) 都是固定值，不参与训练。从表达式可以看出：

1. 无论是前景类还是背景类， ![[公式]](https://www.zhihu.com/equation?tex=p_t) 越大，权重 ![[公式]](https://www.zhihu.com/equation?tex=%281-p_t%29) 就越小。也就是说easy example可以通过权重进行抑制。换言之，当某样本类别比较明确些，它对整体loss的贡献就比较少；而若某样本类别不易区分，则对整体loss的贡献就相对偏大。这样得到的loss最终将集中精力去诱导模型去努力分辨那些难分的目标类别，于是就有效提升了整体的目标检测准度。
2. ![[公式]](https://www.zhihu.com/equation?tex=%5Calpha_t+) 用于调节positive和negative的比例，前景类别使用 ![[公式]](https://www.zhihu.com/equation?tex=%5Calpha_t) 时，对应的背景类别使用 ![[公式]](https://www.zhihu.com/equation?tex=1-%5Calpha_t) ；
3. ![[公式]](https://www.zhihu.com/equation?tex=%5Cgamma) 和 ![[公式]](https://www.zhihu.com/equation?tex=%5Calpha_t) 的最优值是相互影响的，所以在评估准确度时需要把两者组合起来调节。作者在论文中给出 ![[公式]](https://www.zhihu.com/equation?tex=%5Cgamma%3D2+) 、 ![[公式]](https://www.zhihu.com/equation?tex=%5Calpha_t%3D0.25) 时，ResNet-101+FPN作为backbone的结构有最优的性能。



之后语义分割中，对于物体和背景之间面积不平衡的问题，也可以用focal loss解决。



# Fcos 

[FCOS:一阶全卷积目标检测 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/63868458)   
[论文](https://arxiv.org/pdf/1904.01355.pdf)

### 1.backbone

fpn的backbone

### 2.head

对fpn提取到的特征图，对特征图上每个点都进行预测。

预测包括类别信息和坐标信息。

其中类别信息为每个类的置信度,坐标信息为和中心点的左右上下偏离坐标。

即最终预测结果为 特征图大小 * (类别数 + 4 )。

### 3.centerness loss 过滤低质量预测框

![image-20210918162310459](image-20210918162310459.png)

因为特征图上每个点都会进行预测，会产生很多偏离中心的低质量预测框，比如上面的蓝色框。

于是设计了一个loss用于表示与中心的偏离程度,即centerness(中心度)。
在预测时，centerness loss 与分类置信度相乘。使偏离中心的预测结果,置信度降低,被过滤掉。
$$
centerness = \sqrt{\frac{min(l,r)}{max(l,r)}*\frac{min(t,b)}{max(t,b)}}
$$





# CornerNet(待看)

[CornerNet 和 CornerNet-Lite - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/73422357)

转换了回归任务的目标,通过检测一对关键点（左上角点和右下角点）来检测物体。

此外，作者还提出了一种新的池化方式——corner pooling，来帮助网络更好的定位角点的位置。





# YOLOX （最后看）

#### 1.decoupled head 

解耦了分类和回归目标框的任务。

而在yolov3 v4 v5中，分类和回归最后都是在特征图上一起得出的。

