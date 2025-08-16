---
layout:     post
title:      张量分析简论：广义基底与张量记号
subtitle:   广义基底、矩阵与二阶张量
date:       2025-07-30
author:     Joker Hook
header-img: img/2.jpg
catalog: true
tags:
    - tensor
    - vector
    - projection
---

> 注：本文笔记基于Simmonds《A Brief on Tensor Analysis》（第二版），仅用于学习与交流目的。

## 广义基底
设$\{\mathbf{g}_1, \mathbf{g}_2, \mathbf{g}_3\}$是一组固定的、不共面的向量，则任意矢量 $\mathbf{v}$ 都可以唯一地表示为：
$$
\mathbf{v} = v^1\mathbf{g}_1 + v^2\mathbf{g}_2 + v^3\mathbf{g}_3 = \sum_{i=1}^{3}v^i\mathbf{g}_i
$$
通常称集合$\{\mathbf{g}_1, \mathbf{g}_2, \mathbf{g}_3\}$为基底，集合内的元素称为基矢量。

> 显然，一个基底集合不必满足正交性，也不要求每个基底向量都是单位矢量。只要由矢量 $\{\mathbf{g}_1, \mathbf{g}_2, \ldots\}$ 构成的矩阵 $\mathbf{G} = [\mathbf{g}_1, \mathbf{g}_2, \ldots]$ 的行列式不为零，即 $\det\mathbf{G} \neq 0$，该集合就可以作为一组有效的基底。

## 求和约定
在上下文明确的情况下，若求和索引在各项中一上（上标）一下（下标），则可省略求和符号。因此可以将上述矢量 $\mathbf{v}$ 分解求和式简单写成
$$
\mathbf{v} = v^i\mathbf{g}_i
$$

## 广义基底下的点积
设三维矢量$\mathbf{v} = v^i\mathbf{g}_i$ 与矢量 $\mathbf{u} = u^i\mathbf{g}_i$，两个矢量的点积为
$$
\begin{align*}
    \mathbf{u} \cdot \mathbf{v} &= (v^i\mathbf{g}_i) \cdot (u^i\mathbf{g}_i) \\
    &= (u^1\mathbf{g}_1 + u^2\mathbf{g}_2 + u^3\mathbf{g}_3)\cdot(v^1\mathbf{g}_1 + v^2\mathbf{g}_2 + v^3\mathbf{g}_3)\\
    &= u^1 v^1\mathbf{g}_1\cdot\mathbf{g}_1 + u^1 v^2\mathbf{g}_1\cdot\mathbf{g}_2 + u^1 v^3\mathbf{g}_1\cdot\mathbf{g}_3 + \ldots \\
    &= u^i v^j\mathbf{g}_i\cdot\mathbf{g}_j \quad(\text{使用求和约定简化})
\end{align*}
$$
注意到由于广义基底不是互相垂直的，因此$i \neq j$的项无法消去。对于两个三维的矢量，点积结果一共有$3^2 = 9$项，实际上可以通过引入一组倒易基底矢量来化简点积表达式。

## 倒易基底矢量
### 倒易基底矢量
用一组新的基底$\{\mathbf{g}^1, \mathbf{g}^2, \mathbf{g}^3\}$来表示矢量$\mathbf{v} = v_j\mathbf{g}^j$，此时点积求和可以写成
$$
\mathbf{u} \cdot \mathbf{v} = u^i v_j\mathbf{g}_i\cdot\mathbf{g}^j
$$
设新旧基底之间的关系满足
$$
\begin{equation}
    \mathbf{g}^i \cdot \mathbf{g}_j = \delta^i_j \equiv 
\begin{cases}
1 & \text{若 } i = j \\
0 & \text{若 } i \ne j
\end{cases}
\end{equation}
$$
此时点积表达式可以化简为
$$
\begin{align*}
    \mathbf{u} \cdot \mathbf{v} &= u^1 v_1 + u^2 v_2 + u^3 v_3 \\
    &= u^i v_i \quad(\text{使用求和约定简化})
\end{align*}
$$
称集合$\{\mathbf{g}^1, \mathbf{g}^2, \ldots\}$为倒易基底集合，其子元素为倒易基底矢量。

### 倒易基底矢量的求解
从基底$\{\mathbf{g}_1, \mathbf{g}_2, \ldots\}$的合法性可知基底构成的矩阵$\mathbf{G} = [\mathbf{g}_1\ \mathbf{g}_2\ \ldots]$ 的行列式不为零，即 $\det\mathbf{G} \neq 0$。记倒易基底为 $\{\mathbf{g}^1, \mathbf{g}^2, \ldots, \mathbf{g}^n\}$，将倒易基底按列排列为矩阵 
$$
\mathbf{G}^* = \begin{bmatrix}
    \mathbf{g}^1 \\
    \mathbf{g}^2 \\
    \vdots
    \end{bmatrix}
$$
则由定义有：
$$
\mathbf{G}^* \mathbf{G} = \mathbf{I}
\quad\Rightarrow\quad
\mathbf{G}^* = \mathbf{G}^{-1}
$$
即
$$
\boxed{
\mathbf{g}^i = \text{第} i \text{行的} \mathbf{G}^{-1}
}
$$
这表明，若已知基底$\{\mathbf{g}_1, \mathbf{g}_2, \ldots\}$，只需求出$\mathbf{G}^{-1}$便可得到倒易基底矢量。

> **例题：** 已知基底  
> $$
> \mathbf{g}_1 = (1, -1, 2),\ \mathbf{g}_2 = (0, 1, 1),\ \mathbf{g}_3 = (-1, -2, 1)
> $$  
> 试求倒易基底矢量 $\{\mathbf{g}^1, \mathbf{g}^2, \mathbf{g}^3\}$。  
>
> **解：** 设矩阵  
> $$
> \mathbf{G} = \begin{bmatrix}
> 1 & 0 & -1 \\
> -1 & 1 & -2 \\
> 2 & 1 & 1
> \end{bmatrix}
> $$  
> 矩阵的逆可计算得到  
> $$
> \mathbf{G}^{-1} = \begin{bmatrix}
> \tfrac{1}{2} & -\tfrac{1}{6} & \tfrac{1}{6} \\
> -\tfrac{1}{2} & \tfrac{1}{2} & \tfrac{1}{2} \\
> -\tfrac{1}{2} & -\tfrac{1}{6} & \tfrac{1}{6} 
> \end{bmatrix}
> $$  
> 因此倒易基底矢量为  
> $$
> \mathbf{g}_1 = \tfrac{1}{6}(3, -1, 1),\ 
> \mathbf{g}_2 = \tfrac{1}{2}(-1, 1, 1),\ 
> \mathbf{g}_3 = \tfrac{1}{6}(-3, -1, 1)
> $$ 

## 矢量的协变分量与逆变分量
从前面的分析可知道，一个矢量$\mathbf{v}$可以写成基底矢量与倒易基底矢量两种形式，即
$$
\mathbf{v} = v^i\mathbf{g}_i = v_j\mathbf{g}^j
$$
称$v^i$为矢量$\mathbf{v}$的逆变分量，$v_i$为矢量$\mathbf{v}$的协变分量。

> 注意到$\mathbf{v}\cdot\mathbf{g}_i= (v_i\mathbf{g}^i)\cdot\mathbf{g}_i = v_i$，因此有$v_i = \mathbf{v}\cdot\mathbf{g}_i$。同理有$v^i = \mathbf{v}\cdot\mathbf{g}^i$

## 关于记号的进一步补充
在一些文献或计算中，常常会遇到类似 $\mathbf{G}^i_j$、$\mathbf{G}^{ij}$，甚至$\mathbf{G}_{ij}$ 这样的表达式。为了避免混淆，作如下约定：

> *一般情况下，上标 $i$ 表示行索引，下标 $j$ 表示列索引。例如，$\mathbf{G}^i_j$ 通常表示矩阵 $\mathbf{G}$ 中第 $i$ 行第 $j$ 列的分量。这种写法有时被称作“屋顶-地窖（roof-cellar）”助记法，其中上标（在上，即屋顶roof）表示行，下标（在下，即地窖cellar）表示列，便于记忆。*

需要注意的是，这种记号在后续张量分析中也很常见，并且与广义的张量指标表示法保持一致。因此，在进行矩阵与张量的混合计算时，这种约定具有较好的兼容性。

## 广义基底下点积分量形式的简化
设矢量$\mathbf{u} = u^i\mathbf{g}_i$与矢量$\mathbf{v} = v_j\mathbf{g}^j$若新旧基底满足如下形式
$$
\begin{equation}
    \mathbf{g}^i \cdot \mathbf{g}_j = \delta^i_j \equiv 
\begin{cases}
1 & \text{若 } i = j \\
0 & \text{若 } i \ne j
\end{cases}
\end{equation}
$$
此时点积表达式可以化简为
$$
\begin{align*}
    \mathbf{u} \cdot \mathbf{v} &= u^1 v_1 + u^2 v_2 + u^3 v_3 \\
    &= u^i v_i
\end{align*}
$$

## 广义基底下的叉积
### 置换张量
考虑两个矢量的叉积，注意到叉积结果也是一个矢量，因此可以设结果为
$$
\begin{equation}
    \mathbf{u} \times \mathbf{v} = (\mathbf{u} \times \mathbf{v})_{k} \mathbf{g}^k
\end{equation}
$$
注意到$v_k = \mathbf{v}\cdot \mathbf{g}_k$，将矢量的协变与逆变分量形式代入上述公式得到
$$
(\mathbf{u} \times \mathbf{v})_{k} = (\mathbf{u} \times \mathbf{v}) \cdot \mathbf{g}_k = (u^i\mathbf{g}_i \times v^j\mathbf{g}_j) \cdot \mathbf{g}_k
$$
为了计算完整的结果，将求和约定展开，并注意到矢量积符合分配律，因此有
$$
\begin{align*}
    (u^i\mathbf{g}_i \times v^j\mathbf{g}_j) &= (u^1\mathbf{g}_1 + u^2\mathbf{g}_2 + u^3\mathbf{g}_3) \times v^j\mathbf{g}_j = u^1\mathbf{g}_1 \times v^j\mathbf{g}_j + u^2\mathbf{g}_2 \times v^j\mathbf{g}_j + u^3\mathbf{g}_3 \times v^j\mathbf{g}_j \\
    &= u^1\mathbf{g}_1 \times (v^1\mathbf{g}_1 +  v^2\mathbf{g}_2 + v^3\mathbf{g}_3) + u^2\mathbf{g}_2 \times (v^1\mathbf{g}_1 +  v^2\mathbf{g}_2 + v^3\mathbf{g}_3) + \ldots \\
    &= u^1\mathbf{g}_1 \times v^1\mathbf{g}_1 + u^1\mathbf{g}_1 \times v^2\mathbf{g}_2 + u^1\mathbf{g}_1 \times v^3\mathbf{g}_3 + \ldots \\
    &=\sum_{i,j}u^i\mathbf{g}_i \times v^j\mathbf{g}_j = \sum_{i,j} u^i v^j (\mathbf{g}_i \times \mathbf{g}_j) \\
    &= u^i v^j (\mathbf{g}_i \times \mathbf{g}_j)\ (\text{使用求和约定简化})
\end{align*}
$$
于是
$$
\begin{equation}
    (\mathbf{u} \times \mathbf{v})_{k} = u^i v^j (\mathbf{g}_i \times \mathbf{g}_j)\cdot \mathbf{g}_k \triangleq u^i v^j \epsilon_{ijk}
\end{equation}
$$
三维空间中，符号 $\epsilon_{ijk}$ 的每个下标均取值于 ${1,2,3}$，因此一共有 $3^3 = 27$ 个分量。称由这27个分量所组成的张量称为置换张量，记为 $\mathbf{P}$。

### 置换张量协变分量的性质
为方便分析，设$\mathbf{g}_{i} = (\mathbf{g}_{ix},\mathbf{g}_{iy},\mathbf{g}_{iz})$，$\mathbf{g}_{j} = (\mathbf{g}_{jx},\mathbf{g}_{jy},\mathbf{g}_{jz})$，$\mathbf{g}_{k} = (\mathbf{g}_{kx},\mathbf{g}_{ky},\mathbf{g}_{kz})$，则
$$
    \epsilon_{ijk} = (\mathbf{g}_i \times \mathbf{g}_j)\cdot \mathbf{g}_k = 
    \left| 
    \begin{matrix}
    \mathbf{g}_{ix} & \mathbf{g}_{iy} & \mathbf{g}_{iz} \\
    \mathbf{g}_{jx} & \mathbf{g}_{jy} & \mathbf{g}_{jz} \\
    \mathbf{g}_{kx} & \mathbf{g}_{ky} & \mathbf{g}_{kz}
    \end{matrix}
    \right|
$$
注意到如果下标$ijk$中任意两个值相同，那么$\epsilon_{ijk} = 0$。又注意到混合积的性质有
$$
\epsilon_{jik} = (\mathbf{g}_j \times \mathbf{g}_i)\cdot \mathbf{g}_k = -\epsilon_{ijk},\ \ldots
$$
作为锚定，记$\epsilon_{123} = J$，则可以写出任意组合的结果，如$\epsilon_{231} = J$、$\epsilon_{132} = -J$等。综上，可以得到如下结果：
$$
\begin{equation}
\epsilon_{ijk} =
\begin{cases}
+J, & \text{当 } (i, j, k) \text{ 是 } (1, 2, 3) \text{ 的偶置换时} \\
-J, & \text{当 } (i, j, k) \text{ 是 } (1, 2, 3) \text{ 的奇置换时} \\
0,  & \text{当 } i, j, k \text{ 中有两个或以上相等时}
\end{cases}
\end{equation}
$$
### 矢量积的张量表示
将式
$$
    (\mathbf{u} \times \mathbf{v})_{k} =  u^i v^j \epsilon_{ijk}
$$
代入矢量积表达式
$$
    \mathbf{u} \times \mathbf{v} = (\mathbf{u} \times \mathbf{v})_{k} \mathbf{g}^k
$$
后得到张量形式的矢量积
$$
\begin{equation}
    \mathbf{u} \times \mathbf{v} = \epsilon_{ijk}u^i v^j \mathbf{g}^k
\end{equation}
$$

### 置换张量的逆变分量
做同样分析，可以得到置换张量的逆变分量。对此设
$$
    (\mathbf{u} \times \mathbf{v})^{k} = (\mathbf{u} \times \mathbf{v}) \cdot \mathbf{g}^k = (u_i\mathbf{g}^i \times v_j\mathbf{g}^j) \cdot \mathbf{g}^k = u_i v_j (\mathbf{g}^i \times \mathbf{g}^j) \cdot \mathbf{g}^k = \epsilon^{ijk} u_i v_j
$$
因此
$$
\begin{equation}
    \mathbf{u} \times \mathbf{v} = \epsilon^{ijk} u_i v_j \mathbf{g}_k
\end{equation}
$$
将协变基底 \(\mathbf{g}_i, \mathbf{g}_j, \mathbf{g}_k\) 的三个向量视为列向量并组成矩阵，则有
$$
    \epsilon_{ijk} = (\mathbf{g}_i \times \mathbf{g}_j)\cdot \mathbf{g}_k = 
    \left| 
    \begin{matrix}
    g_{ix} & g_{iy} & g_{iz} \\
    g_{jx} & g_{jy} & g_{jz} \\
    g_{kx} & g_{ky} & g_{kz}
    \end{matrix}
    \right| = 
    \det \begin{bmatrix}\mathbf{g}_{i} & \mathbf{g}_{j} & \mathbf{g}_{k}\end{bmatrix} = \det \mathbf{G}_{ijk}
$$
而
$$
    \epsilon^{ijk} = (\mathbf{g}^i \times \mathbf{g}^j)\cdot \mathbf{g}^k = 
    \left| 
    \begin{matrix}
    g^{ix} & g^{iy} & g^{iz} \\
    g^{jx} & g^{jy} & g^{jz} \\
    g^{kx} & g^{ky} & g^{kz}
    \end{matrix}
    \right|
$$
前面分析了逆变形式与协变形式在矩阵表达形式上的关系，注意到
$$
\boxed{
\mathbf{g}^i = \text{第} i \text{行的} (\mathbf{G}_{ijk})^{-1}
}
$$
因此逆变分量按照行排列构成的矩阵实际上为协变矩阵的逆，即
$$
    \begin{bmatrix}
        g^{ix} & g^{iy} & g^{iz} \\
        g^{jx} & g^{jy} & g^{jz} \\
        g^{kx} & g^{ky} & g^{kz}
    \end{bmatrix} = (\mathbf{G}_{ijk})^{-1}
$$
即
$$
\epsilon^{ijk} = \det ((\mathbf{G}_{ijk})^{-1}) = (\det \mathbf{G}_{ijk})^{-1} = \frac{1}{J}
$$
因此可以得到
$$
\begin{equation}
    \epsilon^{ijk} =
    \begin{cases}
    +\frac{1}{J}, & \text{当 } (i, j, k) \text{ 是 } (1, 2, 3) \text{ 的偶置换时} \\
    -\frac{1}{J}, & \text{当 } (i, j, k) \text{ 是 } (1, 2, 3) \text{ 的奇置换时} \\
    0,  & \text{当 } i, j, k \text{ 中有两个或以上相等时}
    \end{cases}
\end{equation}
$$

> 注意到$\epsilon_{ijk}/\epsilon^{ijk} = J^2$，因此$\epsilon_{ijk} = J^2 \epsilon^{ijk}$。

## 二阶张量的混合分量
### 广义基底下的二阶张量协变表示
设二阶张量$\mathbf{T}$与广义基底$\{\mathbf{g}_j\}$，记
$$
\mathbf{T}_j = \mathbf{T}(\mathbf{g}_j)
$$
其中$\mathbf{T}_j$是一个矢量，注意到任意矢量都可以用广义基底$\{\mathbf{g}_j\}$的线性组合来表示，因此可以设
$$
\mathbf{T}_j = (T_{j})_i\mathbf{g}^i = T_{ij}\mathbf{g}^i
$$
对此，二阶张量$\mathbf{T}$的9个分量可以通过如下形式求得
$$
\mathbf{g}_i \cdot T_{ij}\mathbf{g}^i = T_{ij}\mathbf{g}^i \cdot \mathbf{g}_i = T_{ij} \Rightarrow T_{ij} = \mathbf{g}_i \cdot \mathbf{T}(\mathbf{g}_j)
$$

为了用广义基底$\{\mathbf{g}_i\}$表示二阶张量$\mathbf{T}$，设矢量$\mathbf{v} = v^j\mathbf{g}_j$，则有
$$
\begin{align*}
    \mathbf{T}(\mathbf{v}) &= \mathbf{T}(v^j\mathbf{g}_j) = v^j\mathbf{T}(\mathbf{g}_j)\ (\text{线性性质并使用求和约定})\\
    &= v^j T_{ij}\mathbf{g}^i = T_{ij}v^j \mathbf{g}^i \\
    &= T_{ij}(\mathbf{v}\cdot \mathbf{g}^j) \mathbf{g}^i \\
    &= T_{ij}(\mathbf{g}^i \otimes \mathbf{g}^j) (\mathbf{v}) 
\end{align*}
$$
由此给出了协变形式的分量
$$
\begin{equation}
    \mathbf{T} = T_{ij}(\mathbf{g}^i \otimes \mathbf{g}^j)
\end{equation}
$$
因此广义基底形式下二阶张量基底集合为$\{\mathbf{g}^i \otimes \mathbf{g}^j\}$，共9个基底矢量。

### 广义基底下的二阶张量混合表示
类似的，可以写出二阶张量的逆变分量形式
$$
\begin{equation}
    \mathbf{T} = T^{ij}(\mathbf{g}_i \otimes \mathbf{g}_j)
\end{equation}
$$
进一步的，若在推导过程中定义$\mathbf{T}_j = (T_{j})^i\mathbf{g}_i = T^{i}_{·j}\mathbf{g}_i$，则可得到
$$
\begin{equation}
    \mathbf{T} = T^{i}_{·j}(\mathbf{g}_i \otimes \mathbf{g}^j)
\end{equation}
$$
同理，若定义$\mathbf{T}^j = (T^{j})_i\mathbf{g}^i = T^{·j}_{i}\mathbf{g}^i$，则可得到
$$
\begin{equation}
    \mathbf{T} = T^{·j}_{i}(\mathbf{g}^i \otimes \mathbf{g}_j)
\end{equation}
$$
称$T^{·j}_{i}$与$T^{i}_{·j}$是二阶张量$\mathbf{T}$的混合分量。

### 对称张量分量之间的关系
若二阶张量$\mathbf{T}$是对称张量，即$\mathbf{T} = \mathbf{T}^T$，则从矩阵计算角度有\footnote{这里利用了标量的转置等于自身的性质。}
$$
T_{ij} = \mathbf{g}^T_i\mathbf{T}\mathbf{g}_j = (\mathbf{g}^T_j \mathbf{T}^T\mathbf{g}_i)^T = (\mathbf{g}^T_j \mathbf{T}\mathbf{g}_i)^T = \mathbf{g}^T_j \mathbf{T}\mathbf{g}_i = T_{ji}
$$
同理可得到$T^{ij} = T^{ji}$与$T^{·i}_j = T^{i}_{·j}$

## 基底的变换
在给定的参考系中，向量与张量本身并不“知晓”我们选择用何种基底来表示它们。换句话说，它们是**几何不变量**。在进行基底变换时，发生改变的只是它们的分量，而几何对象本身保持不变。张量分析的一个主要目标，就是在已知基底变换的前提下，提供一种从原分量计算新分量的通用方法。

设新基底为
$$
\tilde{\mathbf{g}}_1 = A^1_1 \mathbf{g}_1 + A^2_1 \mathbf{g}_2 + A^3_1 \mathbf{g}_3, \quad
\tilde{\mathbf{g}}_2 = A^1_2 \mathbf{g}_1 + \cdots, \quad
\tilde{\mathbf{g}}_3 = \cdots
$$
可以证明最终采用新基底后，矢量$\mathbf{v}$的新分量为
$$
\begin{equation}
    \tilde{v}_{j} = A^i_j v_i, \quad \tilde{v}^{i} = (A^{-1})^i_j v^j,
\end{equation}
$$
二阶张量的新分量变为：
$$
\begin{equation}
    \begin{aligned}
        \tilde{T}_{ij} = A^k_i A^p_j T_{kp}&, \quad
        \tilde{T}^{i}_{\cdot j} = (A^{-1})^i_k A^p_j T^{k}_{\cdot p},\\
        \tilde{T}^{\cdot i}_{j} = A^k_j (A^{-1})^i_p T^{\cdot p}_{k}&, \quad
        \tilde{T}^{ij} = (A^{-1})^i_k (A^{-1})^j_p T^{kp}
    \end{aligned}
\end{equation}
$$
