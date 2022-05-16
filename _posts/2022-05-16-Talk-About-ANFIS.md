---
layout:     post                       # 使用的布局（不需要改）
title:      浅析自适应网络模糊推理系统(ANFIS)
subtitle:   模糊推断与神经网络的结合
date:       2022-5-16                 # 时间
author:     Joker Hook                         # 作者
header-img: img/6.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - adaptive network
    - fuzzy system
---

> **摘要**
> 
> 本文主要介绍分析由Jyh-Shing Roger Jang于1993年发表的开创性论文[ANFIS: adaptive-network-based fuzzy inference system](https://ieeexplore.ieee.org/abstract/document/256541)

## 模糊IF-Then准则
模糊IF-Then准则有以下两种常用的形式：
- **IF A THEN B**, 其中A与B都是**模糊用词**。例如`If pressure is high, then volume is small`，本例子中关键词`high`与`small`均为模糊的数学概念，可由人为划分界限。
- **IF A THEN C**, 其中A为模糊用词， C为精确的数学表达式。例如`If velocity is high, then force = 3.14`。

## 模糊推理系统(Fuzy Inference Systems)

### 模糊推理系统的结构
标准模糊推理系统如下图所示:
![模糊推理系统](https://github.com/HuangRunHua/huangrunhua.github.io/raw/master/img/ANFIS/1.png)

根据定义，模糊推理系统由五个部分组成：
- 包含大量模糊if-then规则的规则库
- 定义模糊规则中使用的模糊集的隶属函数的数据库
- 决策单元，对规则执行推理操作
- 模糊化接口，将清晰的输入转换为与语言值匹配的程度
- 反模糊接口，将推理的模糊结果转换为清晰的输出

其中规则库与数据库的联合称为知识库。

### 模糊推理步骤
- **模糊化：** 将输入变量与前提部分的隶属函数进行比较，以获得每个语言标签的隶属度值
- **计算规则权重：** 组合前提部分的成员值，以获得每个规则的权重
- **计算结果：** 根据权重生成每个规则的合格结果（模糊或清晰）
- **去模糊化：** 对合格的结果进行汇总，以产生清晰的输出

## 自适应网络(Adaptive Networks)基础

### 网络结构与学习法则
自适应网络是一种多层前馈网络，其中每个节点对输入信号以及与该节点有关的一组参数执行特定功能（节点功能）。标准的自适应网络结构如下图所示:
![标准的自适应网络结构](https://github.com/HuangRunHua/huangrunhua.github.io/raw/master/img/ANFIS/2.png)

上述网络结构中，每个节点函数的选择取决于自适应网络需要执行的总体输入输出函数。自适应网络中的链路仅指示节点之间信号的流向；没有权重与链接关联。方形节点为自适应节点，具备参数；圆形节点为固定节点，没有参数。

#### 基本原理
定义一个L层网络，其中假设第k层有#k个节点，则第k层第i个节点的函数与实际输出为：

$$
O_{i}^{k}=O_{i}^{k}\left(O_{1}^{k-1}, \ldots O_{\#(k-1)}^{k-1}, a, b, c, \ldots\right)
$$

其中abc为网络节点参数。

采用第p组数组进行网络训练，标记

$$
T_{m,p}
$$

为目标输出(正确的输出)的第m个分量，则本次数据**训练误差度量**为

$$
E_{p}=\sum_{m=1}^{\#(L)}\left(T_{m, p}-O_{m, p}^{L}\right)^{2}
$$

则所有数据训练后**总体误差测量值**表达式为:

$$
E=\sum_{p=1}^{P} E_{p}
$$

为使得训练误差度量与总体误差测量值达到最小，并注意到训练误差度量与总体误差测量值为网络实际输出与网络节点参数的函数，则可以使用梯度下降法使的误差达到理论最小值。

训练数据和每个节点输出的错误率定义为:

$$
\frac{\partial E_{p}}{\partial O_{i, p}^{L}}=-2\left(T_{i, p}-O_{i, p}^{L}\right)
$$

训练数据和内部节点的错误率定义为:

$$
\frac{\partial E_{p}}{\partial O_{i, p}^{k}}=\sum_{m=1}^{\#(k+1)} \frac{\partial E_{p}}{\partial O_{m, p}^{k+1}} \frac{\partial O_{m, p}^{k+1}}{\partial O_{i, p}^{k}}
$$

综合上述两式子可以看出内部节点的错误率可以表示为下一层中节点的错误率的线性组合。

训练数据和参数的错误率定义为:

$$
\frac{\partial E_{p}}{\partial \alpha}=\sum_{O^{*} \in S} \frac{\partial E_{p}}{\partial O^{*}} \frac{\partial O^{*}}{\partial \alpha}
$$

总参数错误率定义为上述错误率达累加和:

$$
\frac{\partial E}{\partial \alpha}=\sum^P_{p=1} \frac{\partial E_{p}}{\partial \alpha}
$$

通过求解上述表达式可以得到参数更新公式：

$$
\Delta \alpha=-\eta \frac{\partial E}{\partial \alpha}
$$

其中

$$
\eta=\frac{k}{\sqrt{\sum_{\alpha}\left(\frac{\partial E}{\partial \alpha}\right)^{2}}}
$$

改变k的值可以改变网络收敛速度。
