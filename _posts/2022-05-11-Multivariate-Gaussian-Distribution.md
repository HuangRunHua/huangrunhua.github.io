---
layout:     post                       # 使用的布局（不需要改）
title:      多元正态分布
subtitle:   高斯滤波与卡尔曼滤波的核心
date:       2022-5-11                 # 时间
author:     Joker Hook                         # 作者
header-img: img/6.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - kalman filter
    - matrix
    - probability density function
---

## 数学表达式
设d维度随机变量$\pmb{x}$满足高斯分布，期望与协方差分别为

$$
E(\pmb{x}) = \pmb{\pmb{\mu}}
$$

$$
E((\pmb{x} - \pmb{\mu})(\pmb{x} - \pmb{\mu})^T) = \Sigma
$$

则随机变量的概率密度函数可以用如下形式来表示:

$$
p(\pmb{x}) = \frac{1}{(2\pi)^{\frac{d}{2}}|\Sigma|^{\frac{1}{2}}}e^{-\frac{1}{2}(\pmb{x}-\pmb{\mu})^T \Sigma^{-1}(\pmb{x}-\pmb{\mu})}
$$

## 常用性质

标记

$$
\left[\begin{array}{l}
\pmb{x} \\
\pmb{y}
\end{array}\right] \sim N\left\{\left(\begin{array}{l}
\pmb{a} \\
\pmb{b}
\end{array}\right),\left[\begin{array}{ll}
\pmb{A} & \pmb{C} \\
\pmb{C}^{\top} & \pmb{B}
\end{array}\right]\right\}
$$

则有

$$
\begin{align}
p(\pmb{x})&=N(\pmb{a}, \pmb{A}) \\
p(\pmb{y})&=N(\pmb{b}, \pmb{B}) \\
p(\pmb{x} \mid \pmb{y})&=N\left(\pmb{a}+\pmb{c} \pmb{B}^{-1}(\pmb{y}-\pmb{b}), \pmb{A}-\pmb{C} \pmb{B}^{-1} \pmb{B}^{\top}\right) \\
p(\pmb{y} \mid \pmb{x})&=N\left(\pmb{b}+\pmb{C}^{\top} \pmb{A}^{-1}(\pmb{x}-\pmb{a}), \pmb{B}-\pmb{C}^{\top} \pmb{A}^{-1} \pmb{C}\right)
\end{align}
$$
