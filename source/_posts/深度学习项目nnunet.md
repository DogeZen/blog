---
title: 深度学习项目nnunet
date: 2021-09-16 18:41:11
tags:
- 神经网络
- 深度学习
- 语义分割
---

git项目地址[MIC-DKFZ/nnUNet](https://github.com/MIC-DKFZ/nnUNet#how-to-run-nnu-net-on-a-new-dataset)

## 一.安装

```
git clone https://github.com/MIC-DKFZ/nnUNet.git
cd nnUNet
pip3 install -e .
```

其中torch直接去官网下和你环境对应的gpu版本,我这里的安装命令如下。

```
pip3 install torch==1.9.0+cu111 torchvision==0.10.0+cu111 torchaudio==0.9.0 -f https://download.pytorch.org/whl/torch_stable.html
```

如果`pip3 install -e .`显示setuptool报错,则`pip3 install -U setuptools`

**尤其需要注意**: 如果是docker 运行项目，会因为运行过程中占满内存而被kill ，因此需要在docker run 时指定`--ipc=host`



## 二.数据准备

[官方讲解文档](https://github.com/MIC-DKFZ/nnUNet/blob/master/documentation/dataset_conversion.md)

[示例数据集](https://drive.google.com/drive/folders/1HqEgzS8BV2c7xYNrZdEAnrHk7osJJ--2)

#### 1.构建总体目录

nnU-Net的默认数据 入口是nnUNet_raw_data_base文件夹

可以在安装nnU-Net前更改

修改.bashrc ，添加以下环境变量，其中/home/是你自己指定的数据目录

```
export nnUNet_raw_data_base="/home/nnUNet_raw"
export nnUNet_preprocessed="/home/nnUNet_preprocessed"
export RESULTS_FOLDER="/home/nnUNet_trained_models"
```

进入nnUNet_raw创建TaskXXX_任务名。这里Task后面的序号需要大于500，不然会导致预训练模型有问题。

```
nnUNet_raw_data_base/nnUNet_raw_data/
├── Task001_BrainTumour
    ├── dataset.json
    ├── imagesTr
    ├── (imagesTs)
    └── labelsTr
├── Task002_Heart
├── Task003_Liver
├── Task004_Hippocampus
├── Task005_Prostate
├── ...
```



#### 2.构建单个任务

把其他格式文件转为 nnunet所需的nifti 

样例代码

- [Task120](https://github.com/MIC-DKFZ/nnUNet/blob/master/nnunet/dataset_conversion/Task120_Massachusetts_RoadSegm.py): 2D png 

- [Task075](https://github.com/MIC-DKFZ/nnUNet/blob/master/nnunet/dataset_conversion/Task075_Fluo_C3DH_A549_ManAndSim.py) and [Task076](https://github.com/MIC-DKFZ/nnUNet/blob/master/nnunet/dataset_conversion/Task076_Fluo_N3DH_SIM.py): 3D tiff

- [Task089](https://github.com/MIC-DKFZ/nnUNet/blob/master/nnunet/dataset_conversion/Task089_Fluo-N2DH-SIM.py) 2D tiff

  最终目录结构

```
nnUNet_raw_data_base/nnUNet_raw_data/Task001_BrainTumour/
├── 配置文件json
├── dataset.json
├── 训练集
├── imagesTr
│   ├── 数据集名_图片序号_模态序号.nii.gz
│   ├── BRATS_001_0000.nii.gz
│   ├── BRATS_001_0001.nii.gz
│   ├── BRATS_001_0002.nii.gz
│   ├── BRATS_001_0003.nii.gz
│   ├── BRATS_002_0000.nii.gz
│   ├── BRATS_002_0001.nii.gz
│   ├── BRATS_002_0002.nii.gz
│   ├── BRATS_002_0003.nii.gz
│   ├── BRATS_003_0000.nii.gz
│   ├── BRATS_003_0001.nii.gz
│   ├── BRATS_003_0002.nii.gz
│   ├── BRATS_003_0003.nii.gz
│   ├── BRATS_004_0000.nii.gz
│   ├── BRATS_004_0001.nii.gz
│   ├── BRATS_004_0002.nii.gz
│   ├── BRATS_004_0003.nii.gz
│   ├── ...
├── 测试集
├── imagesTs
│   ├── 数据集名_图片序号_模态序号.nii.gz
│   ├── BRATS_485_0000.nii.gz
│   ├── BRATS_485_0001.nii.gz
│   ├── BRATS_485_0002.nii.gz
│   ├── BRATS_485_0003.nii.gz
│   ├── BRATS_486_0000.nii.gz
│   ├── BRATS_486_0001.nii.gz
│   ├── BRATS_486_0002.nii.gz
│   ├── BRATS_486_0003.nii.gz
│   ├── BRATS_487_0000.nii.gz
│   ├── BRATS_487_0001.nii.gz
│   ├── BRATS_487_0002.nii.gz
│   ├── BRATS_487_0003.nii.gz
│   ├── BRATS_488_0000.nii.gz
│   ├── BRATS_488_0001.nii.gz
│   ├── BRATS_488_0002.nii.gz
│   ├── BRATS_488_0003.nii.gz
│   ├── BRATS_489_0000.nii.gz
│   ├── BRATS_489_0001.nii.gz
│   ├── BRATS_489_0002.nii.gz
│   ├── BRATS_489_0003.nii.gz
│   ├── ...
├── 标签
└── labelsTr
    ├── BRATS_001.nii.gz
    ├── BRATS_002.nii.gz
    ├── BRATS_003.nii.gz
    ├── BRATS_004.nii.gz
    ├── ...

```

#### 3.构建dataset.json

[自动生成dataset.json的工具类](https://github.com/MIC-DKFZ/nnUNet/blob/master/nnunet/dataset_conversion/utils.py) 见 generate_dataset_json 函数

[工具类使用样例](https://github.com/MIC-DKFZ/nnUNet/blob/master/nnunet/dataset_conversion/Task120_Massachusetts_RoadSegm.py)

样例:

```
{ 
# 数据集名称
"name": "BRATS", 
"description": "Gliomas segmentation tumour and oedema in on brain images",
"reference": "https://www.med.upenn.edu/sbia/brats2017.html",
"licence":"CC-BY-SA 4.0",
"release":"2.0 04/05/2018",
"tensorImageSize": "4D",
# 模态
"modality": { 
	 "0": "FLAIR", 
	 "1": "T1w", 
	 "2": "t1gd",
	 "3": "T2w"
 },  
 # 标签名
 "labels": { 
	 "0": "background", 
	 "1": "edema",
	 "2": "non-enhancing tumor",
	 "3": "enhancing tumour"
 }, 
 # 数量
 "numTraining": 484, 
 "numTest": 266,
 "training":[{"image":"./imagesTr/BRATS_457.nii.gz","label":"./labelsTr/BRATS_457.nii.gz"},{"image":"./imagesTr/BRATS_306.nii.gz","label":"./labelsTr/BRATS_306.nii.gz"},{"image":"./imagesTr/BRATS_206.nii.gz","label":"./labelsTr/BRATS_206.nii.gz"}...],
 "test":["./imagesTs/BRATS_557.nii.gz","./imagesTs/BRATS_549.nii.gz","./imagesTs/BRATS_683.nii.gz"...]
 }
```

imagesTr  包含属于训练案例的图像。

imagesTs（可选）包含属于测试用例的图像。

labelsTr     包含训练用例的真实分割图的图像。 

dataset.json 包含数据集的元数据。
所有图像(包括标签)必须是 3D nifti 文件 (.nii.gz)，数据集名 _ 图片序号 _ 模态标识符.nii.gz。

标签文件必须为包含连续整数的分割图，从 0 开始：0, 1, 2, 3, ... 标签数。



## 三.预处理

如果数据集路径正确，如nnUNet_raw_data_base/nnUNet_raw_data/TaskXXX_MYTASK

可以使用以下命令运行此步骤：

```
nnUNet_plan_and_preprocess -t XXX --verify_dataset_integrity
```

XXX 是任务名称 TaskXXX_MYTASK 中的值。

运行完之后可以在 nnUNet_preprocessed/TaskXXX_MYTASK 中找到输出。 

--verify_dataset_integrity 用来验证数据集是否正确



## 四.训练模型

```
nnUNet_train CONFIGURATION TRAINER_CLASS_NAME TASK_NAME_OR_ID FOLD  --npz (additional options)
```

样例命令

```
nnUNet_train 3d_fullres nnUNetTrainerV2 TaskXXX_MYTASK FOLD --npz
nnUNet_train 3d_fullres nnUNetTrainerV2 Task001_BrainTumour 0 
```

CONFIGURATION 是一个字符串，用于标识所请求的 U-Net 配置。 

TRAINER_CLASS_NAME 是模型训练器的名称。如果您实施自定义训练器（nnU-Net 作为框架），您可以在此处指定您的自定义训练器。

TASK_NAME_OR_ID 指定应该训练哪个数据集，

FOLD 指定训练 5 折交叉验证的哪个折叠。
-c 继续之前的训练

--npz 使模型在最终验证期间保存 softmax 输出。只用于运行 nnUNet_find_best_configuration 的训练（ 自动选择最佳性能配置）



运行如果出问题，需要手动停止进程防止占用显存

```
ps | grep nnUNet | cut -c 3-8 |xargs kill -9
```



## 五.训练结束，寻找最优模型

```
nnUNet_find_best_configuration -m 2d 3d_fullres 3d_lowres 3d_cascade_fullres -t XXX --strict
```

