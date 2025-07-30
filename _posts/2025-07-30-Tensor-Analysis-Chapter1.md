---
layout:     post
title:      张量分析简论：介绍——矢量与张量
subtitle:   矢量点积、张量投影与笛卡尔基底
date:       2025-07-30
author:     Joker Hook
header-img: img/1.jpg
catalog: true
tags:
    - tensor
    - vector
    - projection
---

## 点积与叉积的另一种解释

对于点积，为了方便起见以做功为例，在力 $\mathbf{F}$ 的作用下，物体移动的位移为 $\mathbf{D}$，此时力做功数值为：

$$
W = \mathbf{F}\cdot \mathbf{D}
$$

从数学角度可以认为力 $\mathbf{F}$ 是一个线性算符，任意的矢量 $\mathbf{D}$ 与该算符作用后都能变成一个标量。

对于叉积，以扭矩为例子，设力 $\mathbf{F}$ 施加在一个点 $P$ 上，该点的矢量为 $\mathbf{x}$，则扭矩可以写成：

$$
\mathbf{N} = \mathbf{x} \times \mathbf{F}
$$

可以从数学上认为 $\mathbf{x} \times$ 是一个线性算符，任意的矢量 $\mathbf{F}$ 与该算符作用后会变成另一个矢量。具有该性质的线性算符一般称为二阶张量。

## 映射与张量

考察矢量之间的投影与映射。矢量 $\mathbf{v}$ 在矢量 $\mathbf{u}$ 方向上的投影定义为：

$$
\text{Proj}_{\mathbf{u}} \mathbf{v} \triangleq (\mathbf{v}\cdot\bar{\mathbf{u}})\bar{\mathbf{u}}
$$

其中 $\bar{\mathbf{u}}$ 为矢量 $\mathbf{u}$ 方向上的单位矢量。从数学角度可以认为符号 $\text{Proj}_{\mathbf{u}}$ 可以看成是一个作用算符，该算符可以作用于任意一个矢量 $\mathbf{v}$ 上，实现将该矢量投影到矢量 $\mathbf{u}$ 上。

在物理与工程中，二阶张量是一个线性算符，因此若 $\text{Proj}_{\mathbf{u}}$ 是张量，则必然满足线性性质，对此考察：

$$
\begin{aligned}
\text{Proj}_{\mathbf{u}}\left(\beta\mathbf{v} + \gamma\mathbf{w}\right) &= \left[\left(\beta\mathbf{v} + \gamma\mathbf{w}\right) \cdot \bar{\mathbf{u}} \right] \bar{\mathbf{u}} \\
&= \left(\beta\mathbf{v}\cdot \bar{\mathbf{u}} + \gamma\mathbf{w}\cdot \bar{\mathbf{u}}\right)\bar{\mathbf{u}} \\
&=\beta\left(\mathbf{v}\cdot\bar{\mathbf{u}}\right)\bar{\mathbf{u}} + \gamma\left(\mathbf{w}\cdot \bar{\mathbf{u}}\right)\bar{\mathbf{u}} \\
&=\beta \text{Proj}_{\bar{\mathbf{u}}}(\mathbf{v}) + \gamma \text{Proj}_{\bar{\mathbf{u}}}(\mathbf{w})
\end{aligned}
$$

这表明算符 $\text{Proj}_{\mathbf{u}}$ 可以看成是张量。

## 直积与张量

上一节的一个通用化例子是直积。矢量 $\mathbf{u}$ 与矢量 $\mathbf{v}$ 的直积定义为 $\mathbf{u}\otimes\mathbf{v}$ 或直接写为 $\mathbf{u}\mathbf{v}$，其性质如下：

对于任意矢量 $\mathbf{w}$ 有：

$$
\left(\mathbf{u}\otimes\mathbf{v}\right)(\mathbf{w}) = (\mathbf{v}\cdot\mathbf{w})\mathbf{u}
$$

即直积 $\mathbf{u}\otimes\mathbf{v}$ 可以看成一个算符，任意的矢量 $\mathbf{w}$ 与该算符作用后会变成沿着 $\mathbf{u}$ 方向的另一个矢量。可以很容易证明该算符是线性的，因此直积构成一个张量。

> **Remark**  
> 注意到直积 $\mathbf{u}\otimes\mathbf{u}$ 算符实际上就是映射张量，即：
> $$
> \text{Proj}_{\mathbf{u}} = \mathbf{u}\otimes\mathbf{u}
> $$

> **Remark**  
> 由两个向量 $\mathbf{u}$ 和 $\mathbf{v}$ 的直积 $\mathbf{u} \otimes \mathbf{v}$ 所构成的张量称为**双向量积张量（dyads）**。

> **Remark**  
> 今后将会看到，任何一个二阶张量都可以表示为有限个直积张量的线性组合。

## 二阶张量在笛卡尔坐标系下的形式

张量 $\mathbf{T}$ 对矢量 $\mathbf{v}$ 的作用通常写成 $\mathbf{T}\mathbf{v}$ 或 $\mathbf{T}\left(\mathbf{v}\right)$。设基底为 $\mathbf{e}_x$、$\mathbf{e}_y$ 与 $\mathbf{e}_z$，设矢量 $\mathbf{v} = \{v_x, v_y, v_z\}$，注意到张量 $\mathbf{T}$ 是线性算符可得：

$$
\mathbf{T}(\mathbf{v}) = \mathbf{T}(v_x\mathbf{e}_x + v_y\mathbf{e}_y + v_z\mathbf{e}_z) = v_x\mathbf{T}(\mathbf{e}_x) + v_y\mathbf{T}(\mathbf{e}_y) + v_z\mathbf{T}(\mathbf{e}_z)
$$

由于 $\mathbf{T}(\mathbf{e}_x)$、$\mathbf{T}(\mathbf{e}_y)$ 与 $\mathbf{T}(\mathbf{e}_z)$ 均为矢量，可设：

$$
\begin{aligned}
\mathbf{T}(\mathbf{e}_x) &= \{T_{xx}, T_{yx}, T_{zx}\} = T_{xx}\mathbf{e}_x + T_{yx}\mathbf{e}_y + T_{zx}\mathbf{e}_z \\
\mathbf{T}(\mathbf{e}_y) &= \{T_{xy}, T_{yy}, T_{zy}\} = T_{xy}\mathbf{e}_x + T_{yy}\mathbf{e}_y + T_{zy}\mathbf{e}_z \\
\mathbf{T}(\mathbf{e}_z) &= \{T_{xz}, T_{yz}, T_{zz}\} = T_{xz}\mathbf{e}_x + T_{yz}\mathbf{e}_y + T_{zz}\mathbf{e}_z
\end{aligned}
$$

> **Remark**  
> 由于笛卡尔坐标系的基底是相互垂直的，因此将张量分量与基底求点积可以得到对应的系数，如：
> $$
> T_{yz} = \mathbf{e}_y \cdot \mathbf{T}(\mathbf{e}_z)
> $$

> **Remark**  
> 显然，二阶张量在某一基底下的坐标表现是一个矩阵。

## 二阶张量基本定义

前面分析了，二阶张量在某一基底下的坐标表现是一个矩阵。因此张量 $\mathbf{T}$ 对矢量 $\mathbf{v}$ 的作用本质上可以视为矩阵与列向量之间的乘法运算。

### 相等张量

如果两个二阶张量对任意矢量的作用效果相同，则这两个张量被视为相等：

$$
\mathbf{T} = \mathbf{S} \quad \Longleftrightarrow \quad \mathbf{T}\left(\mathbf{v}\right) = \mathbf{S}\left(\mathbf{v}\right)
$$

### 二阶零张量

**二阶零张量** $\mathbf{O}$ 定义为：

$$
\mathbf{O}(\mathbf{v}) = \mathbf{0}
$$

也就是说，$\mathbf{O}$ 将所有矢量都映射为零向量。

### 二阶单位张量

**二阶单位张量** $\mathbf{I}$ 定义为：

$$
\mathbf{I}(\mathbf{v}) = \mathbf{v}
$$

即 $\mathbf{I}$ 在作用时保持矢量不变，起到类似“单位算符”的作用。

### 二阶张量的转置

标记转置张量为 $\mathbf{T}^{T}$，满足对任意矢量 $\mathbf{u}$ 和 $\mathbf{v}$，有：

$$
\mathbf{u}\cdot\mathbf{T}(\mathbf{v}) = \mathbf{v}\cdot \mathbf{T}^{T}(\mathbf{u})
$$

### 对称张量

若张量 $\mathbf{T}$ 满足 $\mathbf{T} = \mathbf{T}^{T}$，则称张量 $\mathbf{T}$ 为**对称张量**。

### 斜对称张量

若张量 $\mathbf{T}$ 满足 $\mathbf{T} = -\mathbf{T}^{T}$，则称张量 $\mathbf{T}$ 为**斜对称张量**。

### 奇异张量

若张量 $\mathbf{T}$ 满足对任意非零矢量 $\mathbf{v}$ 都有 $\mathbf{T}(\mathbf{v}) = \mathbf{0}$，则称张量 $\mathbf{T}$ 为**奇异张量**。

> **Remark**  
> 任意一个二阶张量均可以写成一个对称张量与斜对称张量的和：
> $$
> \mathbf{T} = \frac{1}{2}(\mathbf{T} + \mathbf{T}^T) + \frac{1}{2}(\mathbf{T} - \mathbf{T}^T)
> $$

## 二阶张量的笛卡尔基底

对于矢量 $\mathbf{v} = \{v_x, v_y, v_z\}$，可以写成：

$$
\mathbf{v} = v_x\mathbf{e}_x + v_y\mathbf{e}_y + v_z\mathbf{e}_z
$$

考虑张量作用：

$$
\mathbf{T}(\mathbf{v}) = v_x\mathbf{T}(\mathbf{e}_x) + v_y\mathbf{T}(\mathbf{e}_y) + v_z\mathbf{T}(\mathbf{e}_z)
$$

将分量代入后整理：

$$
\begin{aligned}
\mathbf{T}(\mathbf{v}) = &v_x(T_{xx}\mathbf{e}_x + T_{xy}\mathbf{e}_y + T_{xz}\mathbf{e}_z) \\
&+ v_y(T_{yx}\mathbf{e}_x + T_{yy}\mathbf{e}_y + T_{yz}\mathbf{e}_z) \\
&+ v_z(T_{zx}\mathbf{e}_x + T_{zy}\mathbf{e}_y + T_{zz}\mathbf{e}_z)
\end{aligned}
$$

注意到：

$$
(\mathbf{v} \cdot \mathbf{e}_x)\mathbf{e}_y = (\mathbf{e}_x \cdot \mathbf{v})\mathbf{e}_y = (\mathbf{e}_y\otimes\mathbf{e}_x)\mathbf{v}, \quad \text{等}
$$

由此得到：

$$
\begin{aligned}
\mathbf{T}(\mathbf{v}) = &[(T_{xx}\mathbf{e}_x\otimes\mathbf{e}_x + T_{xy}\mathbf{e}_y\otimes\mathbf{e}_x + T_{xz}\mathbf{e}_z\otimes\mathbf{e}_x) \\
&+ (T_{yx}\mathbf{e}_x\otimes\mathbf{e}_y + T_{yy}\mathbf{e}_y\otimes\mathbf{e}_y + T_{yz}\mathbf{e}_z\otimes\mathbf{e}_y) \\
&+ (T_{zx}\mathbf{e}_x\otimes\mathbf{e}_z + T_{zy}\mathbf{e}_y\otimes\mathbf{e}_z + T_{zz}\mathbf{e}_z\otimes\mathbf{e}_z)](\mathbf{v})
\end{aligned}
$$

因此二阶张量 $\mathbf{T}$ 可写为：

$$
\begin{aligned}
\mathbf{T} = &(T_{xx}\mathbf{e}_x\otimes\mathbf{e}_x + T_{xy}\mathbf{e}_y\otimes\mathbf{e}_x + T_{xz}\mathbf{e}_z\otimes\mathbf{e}_x) \\
&+ (T_{yx}\mathbf{e}_x\otimes\mathbf{e}_y + T_{yy}\mathbf{e}_y\otimes\mathbf{e}_y + T_{yz}\mathbf{e}_z\otimes\mathbf{e}_y) \\
&+ (T_{zx}\mathbf{e}_x\otimes\mathbf{e}_z + T_{zy}\mathbf{e}_y\otimes\mathbf{e}_z + T_{zz}\mathbf{e}_z\otimes\mathbf{e}_z)
\end{aligned}
$$

可以简单的证明，这是唯一的基底表示形式。集合 $\left\{\mathbf{e}_x\otimes\mathbf{e}_x, \mathbf{e}_x\otimes\mathbf{e}_y, \ldots \right\}$ 称为笛卡尔坐标系下二阶张量的基底集合，即任意一个二阶张量都可以用这9个基底来表示。

> **免责声明**  
> 本文内容为作者学习与总结所用，仅供参考，不构成任何专业建议。文中若引用了他人观点或内容，版权归原作者所有。如有不妥之处，请联系删除或更正。
