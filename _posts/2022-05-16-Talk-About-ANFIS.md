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

### 混合学习方法
上述梯度下降法存在收敛速度慢、容易陷入局部最小值的问题。论文作者给出了一个同最小二乘法结合的混合学习算法，可以使的网络的收敛速度加快同时能够使的网络收敛到全局最小值。

标记I为输入变量集合，S为网络参数集合，假设输出为单个值，写成数学表达式如下:

$$
\text { output }=F(\vec{I}, S)
$$

为更好使用最小二乘法，将F利用复合函数进行线性化：

$$
H(\text { output })=H \circ F(\vec{I}, S)
$$

线性化后的输出可以写成线性的形式。考察P组训练数据，上述方程可以写成矩阵线性方程组的形式:

$$
A_{P\times M}X_{M\times 1} = B_{P\times 1}
$$

上述方程存在最小二乘解:

$$
X^{*}=\left(A^{T} A\right)^{-1} A^{T} B
$$

为满足计算需求，最小二乘解可以写成迭代的形式:

$$
\left.\begin{array}{rl}
X_{i+1} & =X_{i}+S_{i+1} a_{i+1}\left(b_{i+1}^{T}-a_{i+1}^{T} X_{i}\right) \\
S_{i+1} & =S_{i}-\frac{S_{i} a_{i+1} a_{i+1}^{T} S_{i}}{1+a_{i+1}^{T} S_{i} a_{i+1}}, \quad i=0,1, \cdots, P-1
\end{array}\right\}
$$

#### 结合法则
混合学习过程的每个阶段都由forward pass和backward pass组成。在**前向传播过程**中，给网络提供输入数据和函数信号，向前计算每个节点的输出，直到获得矩阵A和B，并且通过序列最小二乘公式计算网络输出层的参数。计算输出层网络参数集合后，继续进行前向传播，直到计算出误差度量。在**后向传播**中，利用梯度更新模糊化层（输入层）参数集合。

> **标注**
>
> 简单来说，反向传播学习规则来调整模糊化层中的参数，并通过最小二乘法识别输出层中的参数。

## 自适应网络模糊推理系统

为分析方便，设存在一个两输入一输出的自适应网络系统，该网络存在如下两个标准规则库：
```swift
// Rule 1
if (x is A1) && (y is B1) {
    f1 = p1x+q1y+r1
}

// Rule 2
if (x is A2) && (y is B2) {
    f2 = p2x+q2y+r2
}
```

遵循上述规则库可以得到如下模糊推理过程：
![模糊推理过程](https://github.com/HuangRunHua/huangrunhua.github.io/raw/master/img/ANFIS/3.png)
其等效模糊网络结构如下：
![等效模糊网络结构](https://github.com/HuangRunHua/huangrunhua.github.io/raw/master/img/ANFIS/4.png)

####  层结构分析
- **模糊化层（第一层）：** 输入x，y在第一层进行模糊化：

$$
O_{i}^{1}=\mu_{A_{i}}(x)
$$

其中隶属函数定义通常为:

$$
\begin{aligned}
\mu_{A_{i}}(x)=\frac{1}{1+\left[\left(\frac{x-c_{i}}{a_{i}}\right)^{2}\right]^{b_{i}}}
\end{aligned}
$$

或

$$
\mu_{A_{i}}(x)=\exp \left\{-\left(\frac{x-c_{i}}{a_{i}}\right)^{2}\right\}
$$

注意到隶属函数存在一定的参数，参数选取的不同则会获得不同的隶属函数。隶属函数的参数称为**前提参数**，前提参数的数值将在后向传播中使用梯度下降法来更新。

- **第二层：** 将输入信号相乘，计算触发强度：

$$
w_{i}=\mu_{A_{i}}(x) \times \mu_{B_{i}}(y), i=1,2
$$

- **第三层：** 触发强度归一化：

$$
\bar{w}_{i}=\frac{w_{i}}{w_{1}+w_{2}}, i=1,2
$$

- **第四层：** 输入特征的线性组合（参数称为结果参数，结果参数通过最小二乘法进行数值更新）与归一化触发强度进行乘积运算：

$$
O_{i}^{4}=\bar{w}_{i} f_{i}=\bar{w}_{i}\left(p_{i} x+q_{i} y+r_{i}\right)
$$

- **去模糊化（第五层）：** 去模糊化得到确切的输出:

$$
O_{1}^{5}=\text { overall output }=\sum_{i} \bar{w}_{i} f_{i}=\frac{\sum_{i} w_{i} f_{i}}{\sum_{i} w_{i}}
$$

#### 参数集合的划分
上述网络输出精确表达式实际上是前提参数与结果参数的线性函数：

$$
\begin{align}
f=& \frac{w_{1}}{w_{1}+w_{2}} f_{1}+\frac{w_{2}}{w_{1}+w_{2}} f_{2} \\
=& \bar{w}_{1} f_{1}+\bar{w}_{2} f_{2} \\
=&\left(\bar{w}_{1} x\right) p_{1}+\left(\bar{w}_{1} y\right) q_{1}+\left(\bar{w}_{1}\right) r_{1} \\
&+\left(\bar{w}_{2} x\right) p_{2}+\left(\bar{w}_{2} y\right) q_{2}+\left(\bar{w}_{2}\right) r_{2}
\end{align}
$$

将参数划分为两类

$$
S=S_{1} \oplus S_{2}
$$

其中 $S_{1}$ 为前提参数，出现在第一层，使用梯度下降法来更新数值； $S_{2}$ 为结果参数，出现在第四层，当信号传播到第四层后使用最小二乘法来计算结果参数。
