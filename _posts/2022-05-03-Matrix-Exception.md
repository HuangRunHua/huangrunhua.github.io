---
layout:     post                       # 使用的布局（不需要改）
title:      随机向量的均值                 # 标题 
subtitle:   矩阵期望计算常见的公式 #副标题
date:       2022-5-3                 # 时间
author:     Joker Hook                         # 作者
header-img: img/6.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - matrix
    - exception
---

设$\pmb{x}$为$n \times 1$维度的随机向量，记$\pmb{A}$为$n \times n$维度的矩阵，并设

$$
E[\pmb{x}] = \pmb{\mu}
$$

$$
Var[\pmb{x}] = \pmb{\Sigma}
$$

则由如下等式成立:

$$
E[\pmb{x}^T A \pmb{x}] = tr(\pmb{A}\pmb{\Sigma}) + \pmb{\mu}^T A \pmb{\mu}
$$
