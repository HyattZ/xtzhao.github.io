---
layout: post
---

**Abstract**

这篇文章提出了两类有效的Attention机制：每次关注全部源词语的全局方法和一次关注一部分的局部方法．

使用局部的Attention获得了Bleu值5个百分点的提高，跟没有使用Attention的但使用了已知技巧的比如Dropout的方法相比．

**1. Introduction**

前一个全局的方法很像Bahdanau的方法但是是一个更简单的架构.后一个方法可以看做是一个hard和soft Attention的混合,这个方法计算比全局的方法或者soft Attention计算量更小，和hard Attention相比，局部的Attention是可变的，让训练变的更容易．

**2. Neural Machine Translation**

神经网络机器翻译是一个神经网络去刻画条件概率p(y|x)将输入x翻译到输出y.

$log p(y|x) = \sum_{j=1}^m log p(y_j|y_{<j}, s)$

s是输入x经过encoder之后得到的表示

$p(y_j|y_{<j}, s) = softmax(g (h_j))$

$h_j=f(h_{j-1},s)$

**3. Attention Based Model**

$h'_t = tanh(W_c[c_t; h_t])$,$p(y_t|y_{<t}, x) = softmax(W_sh'_t)$ 

c_t是经过Attention对encoder的各个隐层计算的加权向量,h_t是decoder第t步的隐层输出．第一个表达式获得的是一个attentional vector，第二个表达式将这个vector计算之后投射到输出空间上面．

**3.1 Global Attention**

Dot:$score(h_t,h'_s)=h_th'_s$;

General:$score(h_t,h'_s)=h_t W_a h'_s$;

Concat:$score(h_t,h'_s)=W_a[h_t,h'_s]$;

1.Bahdanau的工作使用的是没有做Stack集单向的decoder;2.计算路径更加简单；3.Bahdanau只使用了concat方法，后面会证明其他的方法比concat方法更好

**3.2 Local Attention**

soft attention指的是在全部的块上都有概率．而hard attention表示的是每次都attend到被选中的一个块上，hard attention在推断阶段花费不多，但是不可微分需要一些比如变量缩减和增强学习等一些特殊的技巧去训练.

这里采用的local attention机制每次选择一个窗口，所以是可以微分的．

具体的做法，就是模型先生成一个根据时间t获得一个对齐位置pt,由经验选择一个窗口大小D，在[pt-D,pt+D]上做Attention.

Monotonic alignment:假定pt=t

Predictive alignment :根据公式$S · sigmoid(v^⊤_p tanh(W_p h_t))$,$W_p$和$v_p$都是可训练的参数．S是源句子的长度．现在的对齐权重计算加上了一个高斯分布．

**3.3 Input-feeding Approach**

主要是说，把上一步的$h'_t$和当前的输入进行拼接．

这样做的两方面的作用：1.希望模型能够感知之前的对齐选择；2.我们创建一个在水平方向和垂直方面都可扩展的深度网络．