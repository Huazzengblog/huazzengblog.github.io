---
title: ConsractiveLearning
date: 2024-04-21 12:00:00 +/-TTTT
categories: [AI,CV]
tags: [AI,paper-learing,CV  ]  # TAG names should always be lowercase
---


## contractive learning
[TOC]
### Instance Discrimination
* 将每个图片都当作一个类，进行个体判别的代理任务
* Memory Bank 存储图片的特征
* 动量更新 Memory Bank
* NCE loss

### Invariant Spreading
* end to end
* 进行个体判别的代理任务
* 缺陷：字典不够大

### CPC
* 生成式结构
* 预测序列接下来的输入
* 正样本为正确输入 负样本为随机的输入
  
### CMC
**Contrativeee Multiview Coding**
* 不同视角下的同一物体应该特征相近
* 正样本为同一物体的不同视角；负样本为不同的物体的任意视角
* 引入多模态 多视角
  
---
### MoCo
* 

### SimCLR
* model
  * 样本定义
    * 正样本
    * 负样本
  * 对样本进行编码 形成representation
  * Projector MLP 提升较高 只在训练时使用
* 优点
  * 数据增强类型多 测评了数据增强的方式
  * 线性层的证明

### MoCo V2
* 引入 Projector MLP、数据增强、cos learning rate  schedule
* 训练占用内存小 训练时间短 仍然是对比bankmemory 和 end to end 的优势


### SimCLR V2
* 做半监督训练时，先训练一个teacher，参考noise student
* teacher即是 SimCLR V2
  * 使用更大的模型 50-》152
  * Projector Head MLP 加深为两层
  * 动量编码器 提升不大 因为本身的batchsize就比较大

### SwAV
* 引入聚类矩阵  聚类中心为3000  矩阵大小D*K D为特征维度 K为聚类中心数量
* 把特征与聚类矩阵点乘 进行换位预测
* multi crop 使用更多的正样本 减少crop的大小 增加数量
* deep cluster
* 性能极好
---
## 不用负样本
### BYOL
Bootstrap your own latent
### SimSiam