---
layout:     post                       # 使用的布局（不需要改）
title:      INS/DVL组合导航算法
subtitle:   间接法进行导航参数的估计
date:       2022-5-12                 # 时间
author:     Joker Hook                         # 作者
header-img: img/1.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - kalman filter
    - auv navigation
---

## 组合导航估计方法
- 直接法：将各种导航参数作为主要状态，滤波估值的主要部分为导航参数的估计值。然而直接法建立的状态方程与量测方程一般为非线性的。
- 间接法：以导航参数的误差作为估计值。通常建模过程中可以忽略二阶小量，所以一般为线性方程。

## 系统误差模型
误差模型由INS建模与DVL建模导致的误差组成。根据捷联惯性导航系统长期工作时的特点，选择位置误差、速度误差、失准角、陀螺漂移作为状态量。陀螺漂移模型用一阶马尔可夫过程描述；对于多普勒计程仪,它测量载体相对海底的速度和偏流角，测量误差主要有速度偏移误差、偏流角误差和刻度系数误差。速度偏移误差和偏流误差用一阶马尔可夫过程表示，刻度系数误差为随机常数。

### INS模型误差

$$
\begin{align}
&\delta \dot{v}_{E}=\frac{v_{N}}{R_{N}} \cdot \tan L \cdot \delta v_{E}+\left(2 \Omega \sin L+\frac{v_{E}}{R_{N}} \cdot \tan L\right) \delta v_{N}+\\
&\left(2 \Omega \cos L v_{N}+\frac{v_{N} v_{E}}{R_{N}} \cdot \sec ^{2} L\right) \delta L-\beta g+\Delta a_{E}\\
&\delta \dot{v}_{N}=-\left(2 \Omega \sin L+\frac{v_{E}}{R_{N}} \cdot \tan L\right) \cdot \delta v_{\mathrm{E}}-\\
&\left(2 \Omega \cos L v_{E}+\frac{v_{E}^{2}}{R_{N}} \cdot \sec ^{2} L\right) \delta L+\alpha g+\Delta a_{N}\\
&\dot{\alpha}=-\frac{\delta v_{N}}{R_{M}}-\gamma\left(\Omega \cos L+\frac{v_{E}}{R_{N}}\right)+\beta\left(\Omega \sin L+\frac{v_{E}}{R_{N}} \tan L\right)+\varepsilon_{E}\\
&\dot{\beta}=-\delta L \Omega \sin L+\frac{\delta v_{E}}{R_{N}}-\alpha\left(\Omega \sin L+\frac{v_{E}}{R_{N}} \tan L\right)-\gamma \frac{v_{N}}{R_{M}}+\varepsilon_{N}\\
&\dot{\gamma}=\delta L\left(\Omega \cos L+\frac{v_{E}}{R_{N}} \sec ^{2} L\right)+\frac{\delta v_{E}}{R_{N}} \tan L+\beta \frac{v_{N}}{R_{M}}+\alpha\left(\Omega \cos L+\frac{v_{E}}{R_{N}}\right)+\varepsilon_{U}\\
&\delta \dot{L}=\frac{\delta v_{N}}{R_{M}}\\
&\delta \dot{\lambda}=\frac{\delta v_{E}}{R_{N}} \sec L+\frac{v_{E}}{R_{N}} \tan L \sec L \delta L\\
&\dot{\varepsilon}_{E}=-\beta_{E} \varepsilon_{E}+w_{E}\\
&\dot{\varepsilon}_{N}=-\beta_{N} \varepsilon_{N}+w_{N}\\
&\varepsilon_{U}=-\beta_{U} \varepsilon_{U}+w_{U}
\end{align}
$$

### DVL模型误差

$$
\left\{\begin{array}{l}
\delta \dot{v}_{d}=-\beta_{d} \delta v_{d}+w_{d} \\
\delta \dot{\Delta}=-\beta_{\Delta} \delta \Delta+w_{\Delta} \\
\delta \dot{C}=0
\end{array}\right.
$$

## 系统状态方程与量测方程

### 系统状态方程
基础建模如下:

$$
\dot{\pmb{X}}=\pmb{A} \pmb{X}+\pmb{B} \pmb{W}
$$

其中状态向量与系统噪声如下：

$$
\boldsymbol{X}=\left[\begin{array}{lllllllllllll}\delta L & \delta \lambda & \delta v_{E} & \delta v_{N} & \alpha & \beta & \gamma & \varepsilon_{E} & \varepsilon_{N} & \varepsilon_{U} & \delta v_{d} & \delta \Delta & \delta C\end{array}\right]^{\mathrm{T}}
$$

$$
\boldsymbol{W}=\left[\begin{array}{lllllllllllll}
0 & 0 & a_{E} & a_{N} & 0 & 0 & 0 & w_{E} & w_{N} & w_{U} & w_{d} & w_{\Delta} & 0
\end{array}\right]^{\mathrm{T}}
$$

系统状态转移矩阵与系统噪声矩阵为:

$$
\boldsymbol{A}=\left[\begin{array}{ccccc}
\boldsymbol{A}_{\mathrm{SINS}_{7 \times 7}} & \vdots & \mathbf{K}_{7 \times 3} & \vdots & \boldsymbol{0}_{7 \times 3} \\
\vdots & \vdots & \vdots & \vdots & \vdots \\
\mathbf{0}_{3 \times 7} & \vdots & \boldsymbol{A}_{\mathrm{Gyra} \times 3} & \vdots & \mathbf{0}_{3 \times 3} \\
\vdots & \vdots & \vdots & \vdots & \vdots \\
\mathbf{0}_{3 \times 7} & \vdots & \mathbf{0}_{3 \times 3} & \vdots & \boldsymbol{A}_{\mathrm{DVI3} \times 3}
\end{array}\right], \boldsymbol{B}=\boldsymbol{I}_{13 \times 13}
$$

其中

$$
\mathbf{K}_{7 \times 3} = \left[\begin{array}{c}
\mathbf{0}_{4 \times 3} \\
\mathbf{I}_{3 \times 3}
\end{array}\right]
$$

### 量测方程

$$
\boldsymbol{Z}=\left[\begin{array}{l}
v_{c E}-v_{d E}^{\prime} \\
v_{c N}-v_{d N}^{\prime}
\end{array}\right]=\left[\begin{array}{l}
\delta v_{E}-\delta v_{d E} \\
\delta v_{N}-\delta v_{d N}
\end{array}\right]=\boldsymbol{H} \boldsymbol{X}+\boldsymbol{V}
$$

其中

$$
\begin{align}
&\boldsymbol{H}=\left[\begin{array}{lllllllllllll}
0 & 0 & 1 & 0 & 0 & 0 & -v_{N} & 0 & 0 & 0 & -\sin K_{d} & -v_{N} & -v_{E} \\
0 & 0 & 0 & 1 & 0 & 0 & v_{E} & 0 & 0 & 0 & -\cos K_{d} & v_{E} & -v_{N}
\end{array}\right]\\
&\boldsymbol{v}=\left[\begin{array}{ll}
v_{E} & v_{N}
\end{array}\right]^{\mathrm{T}}
\end{align}
$$

$K_d$表示考虑偏流角的航迹向。
