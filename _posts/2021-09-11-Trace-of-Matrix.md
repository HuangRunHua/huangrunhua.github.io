---
layout:     post                       # 使用的布局（不需要改）
title:      矩阵迹的性质
subtitle:   一些常用的矩阵期望计算准则
date:       2021-09-11
author:     Joker Hook                         # 作者
header-img: img/2.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - matrix
    - exception
    - trace of matrix
---

## 基础性质
设$r$为标量，则有

$$
tr(\pmb{A}+\pmb{B}) = tr(\pmb{A}) + tr(\pmb{B})
$$

$$
tr(r\pmb{A}) = r \cdot tr(\pmb{A})
$$

$$
tr(\pmb{A}) = tr(\pmb{A}^T)
$$

## 矩阵乘积的迹

$$
tr(\pmb{A}\pmb{B}) = tr(\pmb{B}\pmb{A})
$$

$$
tr(\pmb{A}\pmb{B}\pmb{C}) = tr(\pmb{B}\pmb{C}\pmb{A}) = tr(\pmb{C}\pmb{A}\pmb{B})
$$

当矩阵$\pmb{A}, \pmb{B}, \pmb{C}$为$n \times n$的对称矩阵时，如下矩阵乘积转换成立：

$$
\begin{align}
tr(\pmb{A}\pmb{B}\pmb{C}) &= tr(\pmb{B}\pmb{C}\pmb{A}) = tr(\pmb{C}\pmb{A}\pmb{B}) \\
&=tr(\pmb{A}\pmb{C}\pmb{B}) = tr(\pmb{C}\pmb{B}\pmb{A}) = tr(\pmb{B}\pmb{A}\pmb{C})
\end{align}
$$
