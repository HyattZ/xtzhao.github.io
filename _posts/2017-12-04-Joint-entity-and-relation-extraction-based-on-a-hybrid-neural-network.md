---
layout: post
---

**Abstract**

本文提出的是一个混合模型，包括一个通用的Bi-LSTM Encoder Decoder网络来实现实体抽取和一个CNN来做关系分类．在公开数据集ACE05上面验证了方法的可靠性．

**Introduction**

传统方法先做NER（命名实体识别），再做RC（关系分类）．联合训练将这两种任务结合起来可以降低级联错误（错误传播，在命名实体识别中识别错误的实体，在关系分类时必然不会正确）．

基于CNN的关系分类做法是：两个实体之间的语句被送到关系分类器中去做关系分类．

**Related works**

实体和关系抽取的两种主要框架：pipeline method和joint learning model.pipeline method主要分为两个步骤,NER和RC．joint learning model联合训练．

命名实体识别现存的系统大多都是传统线性模型，如HMM，CRF等．现在又提出了很多NN的方法，这些NN的方法，基本都采用Bi-LSTM作为Encoder，在Decoder上有所不同

关系抽取三种方法：基于手工特征的方法，基于NN的方法和其他一些数值方法（核方法）．

**Their Method**

![img](https://mmbiz.qpic.cn/mmbiz_png/VBcD02jFhgkjZwt9qQxA5ictEzIzVFnfBSHWia0tDSxO1IH0FFARmgzNM4UFCcibQLMhm0H0lUibGiauyXGuuSFdnDw/?tp=webp&wxfrom=5&wx_lazy=1)

在训练时，两个任务都可以通过反向传播算法来更新共享参数（即Bi-LSTM的encoder的各个参数）实现两个子任务之间的依赖．

**注**
共享参数的训练，可以尝试