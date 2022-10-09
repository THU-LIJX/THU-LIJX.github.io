---
title: GAMES101
date: 2022-09-19 19:35:19
updated: 2022-09-19 19:35:19
mathjax: true
tags:
---

## GAMES101学习笔记

### Lecture 1: Introduction

课程内容：

+ Rasterization 光栅化
+ Curves and Meshes 曲线和曲面
+ Ray Tracing 光线追踪
+ Animation / Simulation 动画 / 模拟

#### Rasterization

+ 将几何单元投影到屏幕
+ 将投影结果打散成像素

#### Curves and Meshes

在计算机图形学中，如何表示几何图形

#### Ray Tracing

+ Shoot rays from the camera through each pixel
  + Calculate intersection and shading
  + Continue to bounce the rays till they hit light sources
+ Gold standard in Animations / Movies

#### Animation / Simulation

### Lecture 2: Review of Linear Algebra

**向量、向量内积**

**向量叉积: $ a\times b = -b \times a$且$||a \times b|| = ||a||||b||\sin\psi$，其中$\psi$为a与b的夹角，且$a\times b$垂直于a、b构成的平面，方向遵循右手定则**
$$
a \times b = 
\begin{pmatrix}
y_az_b - y_bz_a \\
z_ax_b - x_az_b \\
x_ay_b - y_ax_b
\end{pmatrix}
= A^*b = 
\begin{pmatrix}
0 & -z_a & y_a \\
z_a & 0 & -x_a \\
-y_a & x_a & 0
\end{pmatrix}
\begin{pmatrix}
x_b \\
y_b \\
z_b
\end{pmatrix}
$$
叉积作用：判断两个向量左/右关系，判断点在三角形的内部/外部。

按一个方向遍历三角形的顶点并取叉积，只有三个叉积结果同时为正或同时为负时，该点在三角形内部

### Lecture 3: Transformation

+ 为什么要学习变换？在图形学当中视角的转换、绘制当中都需要变换
+ **2D transformations**
  + 使用矩阵表示变换
  + Rotation, scale, shear 旋转、缩放、切变
+ **Homogeneous coordinates** 齐次坐标

**Scale Matrix**
$$
\begin{pmatrix}
s_x & 0 \\
0 & s_y
\end{pmatrix}
$$
**Reflection Matrix**
$$
\begin{pmatrix}
-1 & 0 \\
0 & 1
\end{pmatrix}
$$
**Shear Matrix**
$$
\begin{pmatrix}
1 & a \\
0 & 1 \\
\end{pmatrix}
$$
<img src="/Users/lijx/Library/Application Support/typora-user-images/image-20220919212647209.png" alt="image-20220919212647209" style="zoom:30%;" />

**Rotation Matrix**

逆时针旋转$\theta$
$$
\begin{pmatrix}
\cos\theta & -\sin\theta \\
\sin\theta & \cos\theta
\end{pmatrix}
$$

#### Homogeneous Coordinates

为什么要引入齐次坐标？平移变换无法用二维坐标的线性变化来表示，在齐次坐标下，这些变换都可以用线性变换表示

+ 2D point = $(x, y, 1)^T$
+ 2D vector = $(x, y, 0)^T$

**齐次坐标下的平移变换矩阵**
$$
\begin{pmatrix}
1 & 0 & t_x \\
0 & 1 & t_y \\
0 & 0 & 1
\end{pmatrix}
$$
**Affine Transformations 仿射变换 = linear map + translation**
$$
\begin{pmatrix}
x'\\
y'
\end{pmatrix} = 
\begin{pmatrix}
a & b \\
c & d 
\end{pmatrix}
\begin{pmatrix}
x \\
y
\end{pmatrix} + 
\begin{pmatrix}
t_x\\
t_y
\end{pmatrix}
$$

$$
\begin{pmatrix}
x'\\
y'\\
1
\end{pmatrix} =
\begin{pmatrix}
a & b & t_x \\
c & d & t_y \\
0 & 0 & 1 
\end{pmatrix}
\begin{pmatrix}
x\\
y\\
1
\end{pmatrix}
$$

线性变换的逆变换的变换矩阵是矩阵的逆。

### Lecture 4: Transformation Cont.

+ 3D transformations
+ Viewing (观测) transformation
  + View (视图) / Camera transformation
  + Projection (投影) transformation
    + Orthographic (正交) projection
    + Perspective (透视) projection

**Rotation around x-, y-, or z-axis**
$$
R_x(\alpha) = \begin{pmatrix}
1 & 0 & 0 & 0 \\
0 & \cos\alpha & -\sin\alpha & 0 \\
0 & \sin\alpha & \cos\alpha & 0 \\
0 & 0 & 0 & 1
\end{pmatrix}
$$

$$
R_y(\alpha) = \begin{pmatrix}
\cos\alpha & 0 & \sin\alpha & 0 \\
0 & 1 & 0 & 0 \\
-\sin\alpha & 0 & \cos\alpha & 0 \\
0 & 0 & 0 & 1
\end{pmatrix}
$$

$$
R_z(\alpha) = \begin{pmatrix}
\cos\alpha & -\sin\alpha & 0 & 0 \\
\sin\alpha & \cos\alpha & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 1
\end{pmatrix}
$$

#### Rodrigues' Rotation Formula

**Rotation by angle $\alpha$ around axis n**
$$
\pmb{R}(\pmb{n}, \alpha) = \cos(\alpha)\pmb{I} + (1 - \cos\alpha)\pmb{nn^T} + \sin\alpha
\begin{pmatrix}
0 & -n_z & n_y \\
n_z & 0 & -n_x \\
-n_y & n_x & 0
\end{pmatrix}
$$

#### Projection Transformation

**Orthographic Projection**
$$
M_{ortho} = 
\begin{pmatrix}
\frac{2}{r-l} & 0 & 0 & 0 \\
0 & \frac{2}{t-b} & 0 & 0 \\
0 & 0 & \frac{2}{n-f} & 0 \\
0 & 0 & 0 & 1 \\
\end{pmatrix}
\begin{pmatrix}
1 & 0 & 0 & -\frac{r+l}{2} \\
0 & 1 & 0 & -\frac{t+b}{2} \\
0 & 0 & 1 & -\frac{n+f}{2} \\
0 & 0 & 0 & 1 \\
\end{pmatrix}
$$
**Perspective Projection**
$$
M_{persp \rightarrow ortho} =
\begin{pmatrix}
n & 0 & 0 & 0\\
0 & n & 0 & 0\\
0 & 0 & n+f & -nf \\
0 & 0 & 1 & 0
\end{pmatrix}
$$

$$
M_{persp} = M_{ortho}M_{persp\rightarrow ortho}
$$



