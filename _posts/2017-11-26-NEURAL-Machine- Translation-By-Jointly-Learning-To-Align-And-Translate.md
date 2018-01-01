---
layout: post
---

**Abstract**

与传统的统计机器翻译不同，神经网络翻译的目标是构建一个可以联合调试的的神经网络去极大化翻译表现。

这篇文章推测，以往的Encoder-Decoders的瓶颈在于只使用了对输入编码的最终结果，提出了一个方法要每次搜索输入的一部分去预测目标单词（Attention）。

**1.INTRODUCTION**

encoder-decoders模型的一个基本假设是神经网络可以把输入句子的必要信息全部编码到定长向量表示里面。但是神经网络很难去应付长句子，尤其是比训练集中的句子更长的句子。文献显示，传统的encoder-decoder架构随着句子长度的提高而迅速降低。

依据源句子中的位置：有Attention计算来保证；依据之前生成的单词：用来计算Attention的向量是这一步之前的隐层输出。

与传统的encoder-decoder的重要不同在于：此方法将源句子编码成一系列的向量，每次选择一个子集。这使得神经网络模型不必要每次不论长度就将所有的信息压缩到一个定长的向量里面。

**2.BACKGROUND:NEURAL MACHINE TRANSLATION**

基于LSTM的RNN神经网络模型已经可以接近常规的基于短语的英法机器翻译系统。

RNN的最常用公式：$h_{t}=f(x_t,h_{t-1})$，$c=q(\{h_1,h_2,...,h_{T_{x}}\})$，在LSTM里面$q(\{h_1,h_2,...,h_{T_{x}}\})=h_{T_{x}}$

**encoder-decoder里面的Decoder刻画的一个条件概率**：

$p(y)=\prod_{t=1}^{T} p(y \mid \{y_1,...,y_{t-1}\},c)$，$p({y_t \mid \{y_1,...,y_{t-1}\},c})=g(y_{t-1},s_t,c)$

这里的$g(y_{t-1},s_t,c)​$中$y_{t-1}​$是前一个单词，$s_t​$是隐状态，c是Encoder的输出

**3.LEARNING TO ALIGN AND TRANSLATE**

$e_{ij}=a(s_{i-1},h_j)$，其中$s_{i-1}$是前一个时间的隐状态，$h_j$是encoders的编码序列中位置为j的向量

$a_{ij}=\frac{exp(e_{ij})}{\sum_{k=1}^{T_x}exp(e_{ik})}$这这里就是一个softmax函数

$c_{ij}=\sum_{j=1}^{T_x}a_{ij}h_{j}$这里是做一个加和

**4.EXPERIMENT SETTINGS**

beam-search是在训练好的模型上面进行最大化的条件概率













