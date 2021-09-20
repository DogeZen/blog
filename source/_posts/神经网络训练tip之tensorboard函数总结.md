---
title: 神经网络训练tip之tensorboard函数总结.md
date: 2021-07-15 08:00:00
tags:  
- pytorch
---



## 1.编写

先初始化

```python
from torch.utils.tensorboard import SummaryWriter
writer = SummaryWriter(comment =f'LR_{lr}_BS_{batch_size}_SCALE_{img_scale}')
```

散点图

```python
writer.add_scalar('Loss/train', loss.item(), global_step) 
```

直方图

```python
writer.add_histogram('weights/'+tag, value.data.cpu().numpy(),global_step)
```

绘图

```python
writer.add_images('masks/true', true_masks, global_step)
writer.add_images('masks/pred', torch.sigmoid(masks_pred) > 0.5, global_step)
```



## 2.查看

```shell
tensorboard --logdir=runs
```

这里的runs为tensorboard**保存log**的目录，默认为**当前文件夹下的runs文件夹**

然后进入http://localhost:6006/ 即可

