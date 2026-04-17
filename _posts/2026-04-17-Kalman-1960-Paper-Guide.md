---
layout:     post
title:      卡尔曼1960年论文浅析：线性滤波与预测问题研究
subtitle:   基于 Kalman (1960) 原文脉络的线性滤波与估计导读
date:       2026-04-17
author:     街角邂逅的猫
header-img: img/1.jpg
catalog:    true
tags:
    - Kalman filter
    - estimation
    - filtering
    - 正交投影
---

> **文献**：Kalman, Rudolph Emil. "A new approach to linear filtering and prediction problems." 《Journal of Basic Engineering》，1960, 页码35–45.  
> 本文由 LaTeX 稿《kalman-paper-guide》整理为网页版，便于与站内 [扩展卡尔曼滤波原理]({{ site.baseurl }}/2021/09/10/EKF/) 对照阅读。  
> 文稿原始日期：2025 年 8 月 16 日。

## 前言


1960 年，Kalman 在论文《A new approach to linear filtering and prediction problems》中首次提出了后来影响深远的 卡尔曼滤波算法。这篇论文不仅深刻改变了信号处理与控制领域的研究范式，也成为现代估计理论的重要基石。

在如今的教材与讲义中，卡尔曼滤波的推导往往通过矩阵求导、极小化目标函数等形式直接呈现。然而，Kalman 在原始论文中的出发点更为直观：基于最小均方误差与正交性条件的基本思想，逐步构建出滤波增益与协方差更新的公式。这种思路不仅更贴近算法本质，也更有助于理解其数学逻辑。

本文的目的，是在不脱离原始论文脉络的前提下，对其推导思路进行系统梳理。读者无需掌握过多繁复的数学知识，只需具备基础的线性代数与概率论背景，便可跟随推演过程，理解卡尔曼滤波公式背后的核心逻辑与数学直觉。希望这份笔记能够为有志于研读原文的读者提供一份清晰的引导。


## 最优估计


### 估计问题分类


在现实中，我们想要测量的往往是一个真实信号 $x_1(t)$，但仪器采集到的数据里总是混进噪声 $x_2(t)$。所以我们真正拿到的观测结果是


$$
y(t) = x_1(t) + x_2(t) ,
$$


也就是说，它同时包含了“有用的信号”和“干扰的噪声”。  

假设我们在一系列时刻 $t_0, \ldots, t_n$ 上都测量到了观测值 $y(t_0), \ldots, y(t_n)$，那么问题就来了：我们怎样才能利用这些带噪声的数据，去推测真实的信号呢？

根据我们希望推测的时刻 $t_{\mathrm{est}}$ 不同，可以把估计问题分成三类：  

- 数据平滑（插值）：如果我们关心的是过去的某一时刻 $t_{\mathrm{est}} < t_n$ 的信号，那就是在“回头看”，试着用现在已有的数据把之前的真实情况补出来。
- 滤波：如果我们关心的是当前时刻 $t_{\mathrm{est}} = t_n$ 的信号，那就是“当下估计”，目标是从最新的观测结果中尽量滤掉噪声，留下真实信号。
- 预测：如果我们关心的是未来的某个时刻 $t_{\mathrm{est}} > t_n$，那就是“向前看”，尝试利用已有的信息对未来的信号进行预测。
 

虽然名字不同，但这三类问题其实可以放在同一个数学框架下处理。为了方便，我们把它们统称为估计问题。 


### 损失函数


假设我们在时刻 $t_1$ 对信号进行估计，得到的结果为 $X_1(t_1)$，而信号的真实值是 $x_1(t_1)$。那么估计误差可以表示为：


$$
\varepsilon = x_1(t_1) - X_1(t_1) .
$$


为了比较不同的估计方法哪一个更好，我们需要一个统一的指标来衡量误差，这个指标就叫做**损失函数**（loss function）。  

简单来说，损失函数就是告诉我们“估计错了多少，该受到多大惩罚”。它通常需要满足几个要求：  

- 误差越大，惩罚越大：损失函数是误差的非减函数，当估计值偏离真实值越远，损失就越大；
- 完全正确时惩罚为零：当估计值正好等于真实值（$\varepsilon = 0$）时，损失函数取值为 0；
- 正负误差同样对待：损失函数是偶函数，也就是说 $L(\varepsilon) = L(-\varepsilon)$，正向和负向的误差被认为是同等严重的。


以下是两种常见的损失函数：


$$
L(\varepsilon) = a \varepsilon^2, \quad L(\varepsilon) = a |\varepsilon| ,
$$


其中 $a > 0$ 是一个权重因子，用来控制惩罚的强度。


### 如何定义估计目标


为了设计一种方法来得到估计值 $X_1(t_1)$，一个直观的目标是：希望这种方法在多次使用的时候，得到的估计结果整体平均误差尽可能小。换句话说，我们要寻找让平均损失最小的方法：


$$
E_{\min} = \min E\left[L\left(\varepsilon\right)\right] .
$$


因为信号的真实值我们永远不知道，所以单次测量的损失值是没法直接算出来的。能做的，就是利用观测到的一系列数据 $y(t_0), \ldots, y(t_n)$，来算一个“平均意义下”的损失：


$$
\bar{L}(\varepsilon) = E\left[L\left(\varepsilon\right)\,\big|\,y(t_0),\ldots,y(t_n)\right] .
$$


把两者结合起来，就得到：


$$
E_{\min} = \min E\left[L\left(\varepsilon\right)\right] 
  = \min E\left\{E\left[L\left(\varepsilon\right)\,\big|\,y(t_0),\ldots,y(t_n)\right]\right\} .
$$


即我们要找到一种方法，它在每次观测数据的基础上，都能尽量减少平均误差；这样多次使用下来，总体的平均误差也就最小了。  

由于“平均”本身是线性的，因此如果在每一次给定观测数据的情况下，估计方法都能让误差的平均值最小，那么把这些情况综合起来时，总体的平均误差自然也是最小的。  

因此，问题可以转化为：在已知观测数据 $y(t_0),\ldots,y(t_n)$ 的条件下，找到让下面这个量最小的估计方法：


$$
E\left[L\left(\varepsilon\right)\,\big|\,y(t_0),\ldots,y(t_n)\right] .
$$


### 满足目标的估计值


可以证明如果损失函数形式为$L(\varepsilon) = \varepsilon^2$，那么满足上述估计目标的估计方法得到的信号估计值为


$$
x_1^*(t_1|t_n) = E\left[x_1(t_1)\big| y(t_0), \ldots, y(t_n)\right]
$$


换句话说，估计值就是“在现有数据条件下，最有可能的信号值”。


## 正交投影


### 向量空间


对于实值随机变量 $y(t_0), \ldots, y(t_n)$，它们的任意线性组合


$$
\sum_{i=0}^{n}a_i y(t_i)
$$


仍然是一个随机变量。改变系数$a_i$可以得到新的随机变量，这些随机变量构成了一个空间，通常用$\mathcal{Y}(t_n)$表示。类比欧几里得空间中向量的定义，可以将$\mathcal{Y}(t_n)$空间中的点看成是向量。


### 向量正交


对于$\mathcal{Y}(t_n)$空间中两个给定的向量$u$与$v$，注意到$u$与$v$是随机变量（和欧几里得空间中的点有所区分），通常将向量$u$与$v$正交定义成


$$
E(uv) = 0
$$


之所以使用期望是因为随机变量的取值具有随机性，不能直接通过单次观测来衡量它们的关系；而期望能够反映它们在平均意义下的相关性。


### 基底向量


类似欧几里得空间，可以定义$\mathcal{Y}(t_n)$空间中的基底向量$e_{t_0},\ldots, e_{t_n}$，这些基底可以通过正交化的方式得到，正交化后这些基底满足


$$
E(e_{t_i} e_{t_j}) = \delta_{t_i t_j}
$$


此时$\mathcal{Y}(t_n)$空间中的任意一个向量$\bar{x}$可以用基底向量的线性组合来表示:


$$
\bar{x} = \sum_{i=0}^{n}a_{t_i} e_{t_i}
$$


通过基底的正交性可以得到向量$\bar{x}$任意一个分量的值


$$
a_{t_j} = \sum_{i=0}^{n}a_{t_i} E(e_{t_i}e_{t_j}) = E\left(\sum_{i=0}^{n}a_{t_i} e_{t_i}e_{t_j}\right) = E\left(\bar{x}e_{t_j}\right)
$$


### 向量的正交分解


注意到在三维的欧几里得空间中，若已知两个向量，则这两个向量最多构成一个二维平面，此时三维空间中的任意一个向量可以被分解为一个在该二维平面上的向量与垂直于该平面的向量。可以将这种分解推广到$\mathcal{Y}(t_n)$空间中。

考察任意向量$x$，这个向量不一定在$\mathcal{Y}(t_n)$空间中，设这个向量可以被分解为在$\mathcal{Y}(t_n)$空间的一部分$\bar{x}$与不在该空间中的一部分$\widetilde{x}$


$$
x = \bar{x} + \widetilde{x}
$$


可以证明不在$\mathcal{Y}(t_n)$空间中的向量$\widetilde{x}$与$\mathcal{Y}(t_n)$空间的关系满足正交性:


$$
E(\widetilde{x}e_{t_i}) = 0
$$


此时类似向量的概念，将$\bar{x}$称为向量$x$在$\mathcal{Y}(t_n)$空间中的正交投影。可以这样理解这两个分量：$\bar{x}$是在我们“已知信息”（观测值）生成的空间里的部分，$\widetilde{x}$是和观测值完全“无关”的部分。这样分解后，$\bar{x}$就相当于“能从观测中看出来的成分”，而$\widetilde{x}$则是“再怎么观察也猜不到的数据”。


### 最优估计值与正交投影


现在我们用$\mathcal{Y}(t_n)$空间中的任意一个向量$\bar{w}$作为候选的估计值，它和真实数据的均方误差为


$$
E\left[(x - \bar{w})^2\right] = E\left[(x - \bar{x} + \bar{x} - \bar{w})^2\right]
$$


注意到$\widetilde{x}$与$\bar{w}$是正交的，因此$E\left[(x - \bar{x})(\bar{x} - \bar{w})\right] = 0$，期望可进一步化简为


$$
E\left[(x - \bar{w})^2\right] = E\left[(x - \bar{x})^2\right] + E\left[(\bar{x} - \bar{w})^2\right] \geq E\left[(x - \bar{x})^2\right]
$$


可以看到，均方误差总是由两部分组成：

1. 无法避免的部分：$x - \bar{x}$，无论怎么估计都存在；
2. 人为增加的部分：$\bar{x} - \bar{w}$，这是因为我们没选到正确的$\bar{w}$

为了让上述误差达到最小，就需要让候选的估计值等于$\bar{x}$，换句话说：如果我们选定损失函数为$L = \varepsilon^2$作为估计好坏的评判方式，假设最优估计值时观测值的某种线性组合，那么在给定观测值$y(t_0),\ldots,y(t_n)$下的最优估计值就是真实数据在“观测值生成的$\mathcal{Y}(t_n)$空间”里的正交投影。这实际上就是卡尔曼论文中的定理2(B)所描述的内容。定理2(A)给出了比较宽松的条件，如果真实信号和观测值满足高斯分布，即使不假设最优估计是线性组合，也无需限定平方误差，这种正交投影的直觉仍然成立。


## 随机过程模型


### 状态转移方程


想象你正在驾驶一辆小车，小车的当前状态包括它的位置、速度和方向，比如在某一时刻 $t$，我们知道小车在 $(x(t), y(t))$ 坐标、速度 $v(t)$ 和运动朝向角 $\theta(t)$。为了预测小车下一时刻的状态，需要知道当前状态。如果小车保持当前速度和方向，下一秒它的位置可以用公式得到：


$$
\begin{aligned}
x(t+1) &= x(t) + v(t) \cos(\theta(t)) \Delta t \\
  y(t+1) &= y(t) + v(t) \sin(\theta(t)) \Delta t \\
  \theta(t+1) &= \theta(t)
\end{aligned}
$$


在实际运动中，道路环境、轮胎打滑或者操作延迟会引入不确定性。因此预测公式需要考虑这些随机扰动，也就是加入噪声项：


$$
\begin{aligned}
x(t+1) &= x(t) + v(t) \cos(\theta(t)) \Delta t + w_x(t) \\
  y(t+1) &= y(t) + v(t) \sin(\theta(t)) \Delta t + w_y(t) \\
  \theta(t+1) &= \theta(t) + w_\theta(t)
\end{aligned}
$$


其中 $w_x(t), w_y(t), w_\theta(t)$ 表示位置和方向预测的不确定性。

将所有方程写成矩阵形式，可以统一表示为：


$$
\begin{aligned}
\begin{bmatrix} x(t+1) \\ y(t+1) \\ \theta(t+1) \end{bmatrix} &=
\begin{bmatrix}
1 & 0 & -v(t) \sin(\theta(t))\,\Delta t \\
0 & 1 & v(t) \cos(\theta(t))\,\Delta t \\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix} x(t) \\ y(t) \\ \theta(t) \end{bmatrix} +
\begin{bmatrix} u_x(t) \\ u_y(t) \\ u_\theta(t) \end{bmatrix} \\
\mathbf{x}(t+1) &= \mathbf{\Phi}(t)\mathbf{x}(t) + \mathbf{u}(t)
\end{aligned}
$$
其中：

1. $\mathbf{x}(t)$ 称为当前状态向量（位置、速度、方向等）；
2. $\mathbf{\Phi}(t)$ 称为状态转移矩阵，描述了在没有噪声时状态如何从 $t$ 变到 $t+1$；
3. $\mathbf{u}(t)$ 称为过程噪声向量，表示预测中的随机不确定性。在实际分析中，我们通常假设过程噪声向量是独立的、高斯分布且均值为零的随机过程。也就是说，每个噪声分量都是独立产生的随机波动，且平均来看对系统没有偏移，这样的假设有助于用数学模型来描述和预测系统的行为。


这个方程称为**状态转移方程**。核心理念是：利用已知的当前状态预测下一步状态，同时考虑不确定性，为后续的状态估计、控制和导航提供基础。这正是导航、机器人、无人驾驶甚至航天器轨迹预测中广泛使用的方法。


### 观测方程


在导航中，我们不仅希望预测小车的下一步状态，还需要通过仪器获取实际观测数据来了解小车的位置。假设我们有一个传感器，它能直接测量小车的坐标 $(x(t), y(t))$。那么在任意时刻 $t$，观测值可以表示为：

$$
\mathbf{z}(t) =
\begin{bmatrix}
z_x(t) \\
z_y(t)
\end{bmatrix}
=
\begin{bmatrix}
x(t) \\
y(t)
\end{bmatrix}
$$

这里，$\mathbf{z}(t)$ 就是观测向量，它告诉我们在时刻 $t$ 小车的位置。用矩阵形式统一表示状态与观测关系，可以写成：

$$
\mathbf{z}(t) = \mathbf{H} \mathbf{x}(t)
$$

其中：

- $\mathbf{x}(t)$ 是当前状态向量（位置、速度、方向等）；
- $\mathbf{H}$ 是观测矩阵，它把状态映射到传感器可直接测量的量。对于当前位置测量，$\mathbf{H}$ 可以写作：

$$
\mathbf{H} = \begin{bmatrix} 1 & 0 & 0 \\ 0 & 1 & 0 \end{bmatrix}
$$

通过这个方程，我们可以把状态向量中的位置部分提取出来，用于导航显示或者后续的状态估计。


## 维纳问题的解


现在我们可以提出需要解决的问题了。考虑如下运动模型与观测模型


$$
\begin{aligned}
\mathbf{x}(t) &= \mathbf{\Phi}(t-1)\mathbf{x}(t-1) + \mathbf{u}(t-1)\\
  \mathbf{y}(t) &= \mathbf{M}(t) \mathbf{x}(t)
\end{aligned}
$$


设过程噪声$\mathbf{u}$是均值为$\mathbf{0}$的高斯噪声，给定观测数据$\mathbf{y}(t_0),\ldots,\mathbf{y}(t)$，我们希望找到$t$时刻$\mathbf{x}(t)$的最优估计值$\mathbf{x}^*(t|t)$使得估计误差的最小均方值达到最小。


### 状态预测方程


观测数据$\mathbf{y}(t_0),\ldots,\mathbf{y}(t)$已知，因此观测数据构成的空间$\mathcal{Y}$已知，方便起见做如下标记


$$
\begin{array}{rl}
    \mathcal{Y}(t) &  \text{观测值} \mathbf{y}(t_0),\ldots,\mathbf{y}(t) \text{构成的向量空间}   \\
    \widetilde{\mathbf{y}}(t|t-1)    & \mathbf{y}(t) \text{在}\mathcal{Y}(t-1) \text{空间上的垂直部分} \\
    \bar{\mathbf{y}}(t|t-1)    & \mathbf{y}(t) \text{在}\mathcal{Y}(t-1) \text{空间上的正交投影}  \\
    \mathcal{Z}(t) &  \text{向量} \widetilde{\mathbf{y}}(t|t-1) \text{单独构成的向量空间}   \\
    \bar{\mathbf{x}}(t|t-1) & \mathbf{x}(t) \text{在}\mathcal{Y}(t-1) \text{空间上的正交投影}\\
    \widetilde{\mathbf{x}}(t|t)    & \mathbf{x}(t) \text{在}\mathcal{Y}(t) \text{空间上的垂直部分} \\
    \mathbf{x}^*(t|t-1) & \text{已知观测值}y(t_0),\ldots, y(t-1)\text{条件下}t-1 \text{时刻的最优估计值，即为}\bar{\mathbf{x}}(t|t-1) \\
    \mathbf{x}^*(t|t) & \text{已知观测值}y(t_0),\ldots, y(t)\text{条件下}t \text{时刻的最优估计值，即为} \bar{\mathbf{x}}(t|t)
\end{array}
$$


根据前面的证明结果


$$
\mathbf{x}^*(t|t-1) = \bar{\mathbf{x}}(t|t-1) = \hat{E}\left[\mathbf{x}(t)|\mathcal{Y}(t-1)\right]
$$


将状态转移方程代入得到


$$
\begin{aligned}
\mathbf{x}^*(t|t-1) &= \hat{E}\left[\mathbf{\Phi}(t-1)\mathbf{x}(t-1) + \mathbf{u}(t-1)|\mathcal{Y}(t-1)\right] \\
    &= \hat{E}\left[\mathbf{\Phi}(t-1)\mathbf{x}(t-1)|\mathcal{Y}(t-1)\right] + \hat{E}\left[\mathbf{u}(t-1)|\mathcal{Y}(t-1)\right]\\
    &= \mathbf{\Phi}(t-1)\hat{E}\left[\mathbf{x}(t-1)|\mathcal{Y}(t-1)\right]\ \text{(过程噪声均值为0)}\\
    &= \mathbf{\Phi}(t-1)\bar{\mathbf{x}}(t-1|t-1) \\
    &= \mathbf{\Phi}(t-1)\mathbf{x}^*(t-1|t-1)
\end{aligned}
$$


上述公式称为状态预测方程。


### 滤波增益系数


假定$\mathbf{x}^*(t|t-1)$已知，于是有


$$
\begin{aligned}
\mathbf{x}^*(t|t) &= \hat{E}[\mathbf{x}(t)|\mathcal{Y}(t)]=\hat{E}[\mathbf{x}(t)|\mathcal{Y}(t-1)]+\hat{E}[\mathbf{x}(t)|\mathcal{Z}(t)]   \\
    &=\mathbf{x}^*(t|t-1)+\hat{E}[\mathbf{x}(t)|\mathcal{Z}(t)]
\end{aligned}
$$


注意到正交投影是线性的关系，并且$\mathcal{Z}(t)$空间中只有一个向量$\widetilde{\mathbf{y}}(t|t-1)$，因此可以设


$$
\hat{E}[\mathbf{x}(t)|\mathcal{Z}(t)] = \mathbf{\Delta}^*(t)\widetilde{\mathbf{y}}(t|t-1)
$$


上式右边


$$
\begin{aligned}
\widetilde{\mathbf{y}}(t|t-1)  &= \mathbf{y}(t)-\bar{\mathbf{y}}(t|t-1) \\
  &= \mathbf{y}(t)-E\left[\mathbf{y}(t)|\mathcal{Y}(t-1)\right]\\
  &= \mathbf{y}(t)-E\left[\mathbf{M}(t)\mathbf{x}(t)|\mathcal{Y}(t-1)\right]\\
  &= \mathbf{y}(t)-\mathbf{M}(t)E\left[\mathbf{x}(t)|\mathcal{Y}(t-1)\right] \\
  &= \mathbf{y}(t)-\mathbf{M}(t)\mathbf{x}^*(t|t-1)
\end{aligned}
$$


于是有


$$
\begin{aligned}
\mathbf{x}^*(t|t) &= \mathbf{x}^*(t|t-1) + \mathbf{\Delta}^*(t)\left[\mathbf{y}(t)-\mathbf{M}(t)\mathbf{x}^*(t|t-1)\right]   \tag{7}
\end{aligned}
$$


注意到$\mathbf{x}(t)$在$\mathcal{Z}(t)$空间的垂直分量


$$
\widetilde{\mathbf{x}}(t|\mathcal{Z}(t)) = \mathbf{x}(t)-\hat{E}[\mathbf{x}(t)|\mathcal{Z}(t)] = \mathbf{x}(t) - \mathbf{\Delta}^*(t)\widetilde{\mathbf{y}}(t|t-1)
$$


与$\widetilde{\mathbf{y}}(t|t-1)$是正交的，两者进行正交乘积有


$$
\begin{aligned}
0 &= E\left\{\left[\mathbf{x}(t)-\mathbf{\Delta}^*(t)\widetilde{\mathbf{y}}(t|t-1)\right]\widetilde{\mathbf{y}}^T(t|t-1)\right\} \\
    &= E\left[\mathbf{x}(t)\widetilde{\mathbf{y}}^T(t|t-1)\right]-\mathbf{\Delta}^*(t)E\left[\widetilde{\mathbf{y}}(t|t-1)\widetilde{\mathbf{y}}^T(t|t-1)\right]
\end{aligned}
$$


注意到


$$
\mathbf{x}(t)=\bar{\mathbf{x}}(t|t-1)+\widetilde{\mathbf{x}}(t|t-1)
$$


同时注意到$\bar{\mathbf{x}}(t|t-1)$在$\mathcal{Y}(t)$空间中，$\mathcal{Z}(t)$空间中的矢量是$\widetilde{\mathbf{y}}(t)$的线性组合，而$\widetilde{\mathbf{y}}(t)$是$\mathbf{y}(t)$在$\mathcal{Y}(t)$空间中的垂直分量，因此$\bar{\mathbf{x}}(t|t-1)$与$\mathcal{Z}(t)$是正交的，因此由上式可以进一步化简为


$$
\begin{aligned}
0&=E[\widetilde{\mathbf{x}}(t|t-1)\widetilde{\mathbf{y}}^T(t|t-1)]-\mathbf{\Delta}^*(t)E[\widetilde{\mathbf{y}}(t|t-1)\widetilde{\mathbf{y}}^T(t|t-1)]
\end{aligned}
$$


将


$$
\widetilde{\mathbf{y}}(t|t-1) = \mathbf{M}(t)\widetilde{\mathbf{x}}(t|t-1)
$$


代入上式有


$$
\begin{aligned}
0&=E[\widetilde{\mathbf{x}}(t|t-1)\widetilde{\mathbf{y}}^T(t|t-1)]-\mathbf{\Delta}^*(t)E[\widetilde{\mathbf{y}}(t|t-1)\widetilde{\mathbf{y}}^T(t|t-1)] \\
  &=E\left\{\widetilde{\mathbf{x}}(t|t-1)\left[\widetilde{\mathbf{x}}^T(t|t-1)\mathbf{M}^T(t)\right]\right\}-\mathbf{\Delta}^*(t)E[\mathbf{M}(t)\widetilde{\mathbf{x}}(t|t-1)\widetilde{\mathbf{x}}^T(t|t-1)\mathbf{M}^T(t)]
\end{aligned}
$$


记


$$
\mathbf{P}^*(t|t-1) = E\left[\widetilde{\mathbf{x}}(t|t-1)\widetilde{\mathbf{x}}^T(t|t-1)\right]
$$


由上式可以化简为


$$
0 = \mathbf{P}^*(t|t-1)\mathbf{M}^T(t) - \mathbf{\Delta}^*(t)\mathbf{M}(t)\mathbf{P}^*(t|t-1)\mathbf{M}^T(t)
$$


解得卡尔曼增益


$$
\mathbf{\Delta}^*(t) = \mathbf{P}^*(t|t-1)\mathbf{M}^T(t)\left[ \mathbf{M}(t)\mathbf{P}^*(t|t-1)\mathbf{M}^T(t) \right]^{-1}  \tag{12}
$$


### 状态更新方程


注意到


$$
\begin{aligned}
\mathbf{x}^*(t|t)
    &=\mathbf{x}^*(t|t-1)+\hat{E}[\mathbf{x}(t)|\mathcal{Z}(t)]   \\
    &= \hat{E}[\mathbf{x}(t)|\mathcal{Y}(t-1)]+\mathbf{\Delta}^*(t)\widetilde{\mathbf{y}}(t|t-1) \\
    &= \mathbf{\Phi}(t)\mathbf{x}^*(t-1|t-1)+\mathbf{\Delta}^*(t)\widetilde{\mathbf{y}}(t|t-1) \tag{13}
\end{aligned}
$$


上述式子为状态更新方程，这个式子给出了最优估计的计算方式。


### 先验估计误差协方差矩阵


过程噪声协方差记为


$$
\mathbf{Q}(t-1)=E[\mathbf{u}(t-1)\mathbf{u}^T(t-1)]
$$


并记


$$
\mathbf{P}(t-1|t-1) = E\left[\widetilde{\mathbf{x}}(t-1|t-1)\widetilde{\mathbf{x}}^T(t-1|t-1)\right]
$$


由


$$
\begin{aligned}
\widetilde{\mathbf{x}}(t|t-1) &= \mathbf{x}(t) - \bar{\mathbf{x}}(t|t-1) \\
  &= \mathbf{\Phi}(t-1)\mathbf{x}(t-1) + \mathbf{u}(t-1) - \mathbf{\Phi}(t-1)\mathbf{x}^*(t-1|t-1) \\
  &=\mathbf{\Phi}(t-1)\widetilde{\mathbf{x}}(t-1|t-1) + \mathbf{u}(t-1)
\end{aligned}
$$


得到


$$
\begin{aligned}
\mathbf{P}^*(t|t-1) &= E\left[\widetilde{\mathbf{x}}(t|t-1)\widetilde{\mathbf{x}}^T(t|t-1)\right] \\
    &= E\left\{\left[\mathbf{\Phi}(t-1)\widetilde{\mathbf{x}}(t-1|t-1) + \mathbf{u}(t-1)\right]\left[\mathbf{\Phi}(t-1)\widetilde{\mathbf{x}}(t-1|t-1) + \mathbf{u}(t-1)\right]^T\right\} \\
    &= \mathbf{\Phi}(t-1)\mathbf{P}^*(t-1|t-1)\mathbf{\Phi}^T(t-1) + \mathbf{Q}(t-1)
\end{aligned}
$$


### 后验估计误差协方差矩阵


根据定义


$$
\begin{aligned}
\widetilde{\mathbf{x}}(t|t)&=\mathbf{x}(t)-\mathbf{x}^*(t|t) \\
    &= \mathbf{\Phi}(t-1)\mathbf{x}(t-1) - \mathbf{\Phi}(t-1)\mathbf{x}^*(t-1|t-1) + \mathbf{u}(t-1)-\mathbf{\Delta}^*(t)\mathbf{M}(t)\widetilde{\mathbf{x}}(t|t-1) \\
    &=\mathbf{\Phi}(t-1)\left[\mathbf{x}(t-1) - \mathbf{x}^*(t-1|t-1)\right] -\mathbf{\Delta}^*(t)\mathbf{M}(t)\widetilde{\mathbf{x}}(t|t-1) + \mathbf{u}(t-1) \\
    &=\mathbf{\Phi}(t-1)\widetilde{\mathbf{x}}(t-1|t-1)-\mathbf{\Delta}^*(t)\mathbf{M}(t)\mathbf{\Phi}(t-1)\widetilde{\mathbf{x}}(t-1|t-1) + \mathbf{u}(t-1) \\
    &=\left[\mathbf{I} - \mathbf{\Delta}^*(t)\mathbf{M}(t)\right]\mathbf{\Phi}(t-1)\widetilde{\mathbf{x}}(t-1|t-1) + \mathbf{u}(t-1) \\
    &=\left[\mathbf{I} - \mathbf{\Delta}^*(t)\mathbf{M}(t)\right]\widetilde{\mathbf{x}}(t|t-1)
\end{aligned}
$$


由协方差的定义得到


$$
\begin{aligned}
\mathbf{P}(t|t) &= E\left[\widetilde{\mathbf{x}}(t|t)\widetilde{\mathbf{x}}^T(t|t)\right]   \\
    &=\left[\mathbf{I} - \mathbf{\Delta}^*(t)\mathbf{M}(t)\right] E\left\{\widetilde{\mathbf{x}}(t|t-1)\widetilde{\mathbf{x}}^T(t|t-1)\right\} \left[\mathbf{I} - \mathbf{\Delta}^*(t)\mathbf{M}(t)\right]^T \\
    &=\left[\mathbf{I} - \mathbf{\Delta}^*(t)\mathbf{M}(t)\right] \mathbf{P}^*(t|t-1) \left[\mathbf{I} - \mathbf{\Delta}^*(t)\mathbf{M}(t)\right]^T
\end{aligned}
$$


## 线性卡尔曼滤波公式汇总

在给定观测值 $\mathbf{y}(t_0),\ldots,\mathbf{y}(t)$ 的条件下，$t$ 时刻状态变量 $\mathbf{x}(t)$ 的最优估计 $\mathbf{x}^*(t|t)$ 由如下公式给出：


$$
\mathbf{x}^*(t|t) = \mathbf{\Phi}(t)\mathbf{x}^*(t-1|t-1)+\mathbf{\Delta}^*(t)\widetilde{\mathbf{y}}(t|t-1)
$$


其中$\mathbf{\Delta}^*(t)$由如下公式给出


$$
\mathbf{\Delta}^*(t) = \mathbf{\Phi}(t)\mathbf{P}^*(t|t-1)\mathbf{M}^T(t)\left[ \mathbf{M}(t)\mathbf{P}^*(t|t-1)\mathbf{M}^T(t) \right]^{-1}
$$


先验误差协方差矩阵$\mathbf{P}^*(t|t-1)$与后验误差协方差矩阵$\mathbf{P}(t|t)$分别由下列公式给出


$$
\begin{cases}
      &\mathbf{P}^*(t|t-1) = \mathbf{\Phi}(t)\mathbf{P}^*(t-1|t-1)\mathbf{\Phi}^T(t) + \mathbf{Q}(t-1) \\
      &\mathbf{P}(t|t)=\left[\mathbf{I} - \mathbf{\Delta}^*(t)\mathbf{M}(t)\right] \mathbf{P}^*(t|t-1) \left[\mathbf{I} - \mathbf{\Delta}^*(t)\mathbf{M}(t)\right]^T
    \end{cases}
$$