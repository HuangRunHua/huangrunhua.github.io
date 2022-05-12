---
layout:     post                       # 使用的布局（不需要改）
title:      不确定性变换公式
subtitle:   已知一个随机向量的特性求取另一个随机向量的统计特性
date:       2022-5-12                 # 时间
author:     Joker Hook                         # 作者
header-img: img/7.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - kalman filter
    - matrix
    - probability density function
    - exception
    - covariance
---

## 非线性变换公式
设两个随机向量满足如下非线性关系:

$$
\pmb{y} = \pmb{f}(\pmb{x})
$$

记随机向量$\pmb{x}$的均值与协方差分别为

$$
\begin{align}
&E(\pmb{x}) = \pmb{\mu_x} \\
&E((\pmb{x} - \pmb{\mu_x})(\pmb{x} - \pmb{\mu_x})^T) = \pmb{\Sigma_{xx}}
\end{align}
$$

则随机向量$\pmb{y}$的均值与协方差分别为

$$
\begin{align}
\pmb{\mu_y} &= \pmb{f}(\pmb{\mu_x}) \\
\pmb{\Sigma_{yy}} &= \pmb{J}\pmb{\Sigma_{xx}}\pmb{J}^T
\end{align}
$$

其中向量$\pmb{J}$为

$$
\pmb{J} = \frac{d\pmb{f}}{d\pmb{x}}
$$

## 应用实例
考察如下变换:

$$
\theta = \frac{z_r - z_f}{L}
$$

写成矩阵形式为:

$$
\theta=\left[\frac{1}{L} \quad-\frac{1}{L}\right]\left[\begin{array}{l}
z_{r} \\
z_{f}
\end{array}\right]
$$

则雅可比矩阵为

$$
\pmb{J} = \left[\begin{array}{ll}
\frac{\partial \theta}{\partial z_{f}} & \frac{\partial \theta}{\partial z_{r}}
\end{array}\right] = \left[\begin{array}{ll}
\frac{1}{L} & -\frac{1}{L}
\end{array}\right]
$$


