---
title: statistic
date: 2022-08-19 16:57:27
updated: 2022-08-19 16:57:27
mathjax: true
tags:
---

## 统计学知识梳理

### 多元正态分布

对于一元正态分布而言，有如下密度函数
$$
\begin{align}
f(x) &= \frac{1}{\sqrt{2\pi\sigma^2}}e^{-\left[(x-\mu)/\sigma\right]^2 / 2} \\
&= (2\pi)^{-1/2}(\sigma^2)^{-1/2}e^{-(x-\mu)'(\sigma^2)^{-1}(x-\mu) / 2}
\end{align}
$$
其中的$(x-\mu)'(\sigma^2)^{-1}(x-\mu)$刻画了点$x$到均值$\mu$经过方差加权的距离，同理可以得到多元正态分布的密度函数（令维度为p）
$$
f(\pmb{x}) = (2\pi)^{-p/2}|\pmb{\Sigma}|^{-1/2}e^{-(\pmb{x-\mu})'\pmb{\Sigma}^{-1}(\pmb{x-\mu})/2}
$$
其中$\pmb{\mu}$为均值向量，$\pmb{\Sigma}$为协方差矩阵（对称正定）

e.g. 二元正态分布的密度函数
$$
f(x_1, x_2) = \frac{1}{2\pi\sqrt{\sigma_{11}\sigma_{22}(1-\rho^2)}}\exp\left\{-\frac{1}{2(1-\rho^2)}\left[\left(\frac{x_1 - \mu_1}{\sqrt{\sigma_{11}}}\right)^2 + \left(\frac{x_2 - \mu_2}{\sqrt{\sigma_{22}}}\right)^2 + 2\rho\left(\frac{x_1 - \mu_1}{\sqrt{\sigma_{11}}}\right)\left(\frac{x_2 - \mu_2}{\sqrt{\sigma_{22}}}\right)\right]\right\}
$$
<!-- more -->

从多元正态分布密度函数的表达式可以看出，对于一个固定大小c的密度而言，所有可能的$\pmb{x}$构成一个椭圆。

**引理1**

> Contours of constant density for the p-dimensional normal distribution are ellipsoids defined by $\pmb{x}$ such the that
> $$
> (\pmb{x-\mu})'\pmb{\Sigma}^{-1}(\pmb{x-\mu}) = c^2
> $$
> These ellipsoids are centered at $\pmb{\mu}$ and have axes $\pm c\sqrt{\lambda_i}\pmb{e_i}$, where $\pmb{\Sigma}\pmb{e_i}=\lambda_i\pmb{e_i}$ for i = 1, 2, ..., p

#### 一些常用的性质

+ 性质1: $\pmb{X} \sim N_p(\pmb{\mu, \Sigma})$，则任意对于$\pmb{X}$中的随机变量的线性组合$\pmb{a'X} = a_1X_1 + a_2X_2 + \cdots + a_pX_p$服从分布$N(\pmb{a'\mu}, \pmb{a'\Sigma a})$；如果对于任意的$\pmb{a}$，都有$\pmb{a'X}$服从$N(\pmb{a'\mu}, \pmb{a'\Sigma a})$，则$\pmb{X} \sim N_p(\pmb{\mu, \Sigma})$
+ 性质2: $\pmb{X} \sim N_p(\pmb{\mu, \Sigma})$，则$\pmb{AX} \sim N_q(\pmb{A\mu, A\Sigma A'})$，其中q是$\pmb{A}$的行数；$\pmb{X + d} \sim N_p(\pmb{\mu + d}, \pmb{\Sigma)}$，其中$\pmb{d}$为常数向量
+ 性质3:$\pmb{X} \sim N_p(\pmb{\mu, \Sigma})$，$\pmb{X}$的所有子集都是多元正态分布
+ 性质4: 
  1. $\pmb{X_1} \sim N_q(\pmb{\mu_1, \Sigma_1}), \pmb{X_2} \sim N_p(\pmb{\mu_2, \Sigma_2})$，则$Cov(\pmb{X_1, X_2}) = \pmb{0}_{q\times p}$
  2. $\begin{bmatrix}\pmb{X_1} \\ \pmb{X_2}\end{bmatrix} \sim N_{q_1 + q_2}\left(\begin{bmatrix}\pmb{\mu_1} \\ \pmb{\mu_2}\end{bmatrix},\begin{bmatrix}\pmb{\Sigma_{11}} &  \pmb{\Sigma_{12}}\\ \pmb{\Sigma_{21}} & \pmb{\Sigma_{22}}\end{bmatrix}\right)$，则$\pmb{X_1}, \pmb{X_2}$互相独立当且仅当$\pmb{\Sigma_{12}} = \pmb{0}$
  3. $\pmb{X_1} \sim N_q(\pmb{\mu_1, \Sigma_{11}}), \pmb{X_2} \sim N_p(\pmb{\mu_2, \Sigma_{22}})$，且互相独立，则有$\begin{bmatrix}\pmb{X_1} \\ \pmb{X_2}\end{bmatrix} \sim N_{q_1 + q_2}\left(\begin{bmatrix}\pmb{\mu_1} \\ \pmb{\mu_2}\end{bmatrix},\begin{bmatrix}\pmb{\Sigma_{11}} &  \pmb{0}\\ \pmb{0} & \pmb{\Sigma_{22}}\end{bmatrix}\right)$
+ 性质5: $\begin{bmatrix}\pmb{X_1} \\ \pmb{X_2}\end{bmatrix} \sim N_{q_1 + q_2}\left(\begin{bmatrix}\pmb{\mu_1} \\ \pmb{\mu_2}\end{bmatrix},\begin{bmatrix}\pmb{\Sigma_{11}} &  \pmb{\Sigma_{12}}\\ \pmb{\Sigma_{21}} & \pmb{\Sigma_{22}}\end{bmatrix}\right)，给定$$\pmb{X_2 = x_2}$，则$\pmb{X_1}$的条件分布仍然为正态分布，且有$\pmb{\mu_{X_1 | X_2}} = \pmb{\mu_1 + \Sigma_{12}\Sigma_{22}^{-1}(x_2 - \mu_2)}, \pmb{\Sigma_{X_1|X_2} = \Sigma_{11} - \Sigma_{12}\Sigma_{22}^{-1}\Sigma_{21}}$
+ 性质6: $\pmb{X} \sim N_p(\pmb{\mu, \Sigma})$
  1. $(\pmb{X-\mu})'\pmb{\Sigma}^{-1}(\pmb{X-\mu}) \sim \chi_p^2)$，其中$\chi_p^2$是自由度为p的卡方分布
  1. $\pmb{X} \sim N_p(\pmb{\mu, \Sigma})$中$ \left\{(\pmb{x:} \pmb{x-\mu})'\pmb{\Sigma}^{-1}(\pmb{x-\mu}) \leq \chi_q^2(\alpha)\right\}$的概率为$1-\alpha$，其中$\chi_q^2(\alpha)$是卡方分布的$\alpha$上分位点
+ 性质7: $\pmb{X_1}, \pmb{X_2}, ..., \pmb{X_n}$互相独立且$\pmb{X_j}$服从分布$N_p(\pmb{\mu_j, \Sigma})$，则$\pmb{V_1} = c_1\pmb{X_1} + c_2\pmb{X_2} + \cdots + c_n\pmb{X_n} \sim N_p\left(\sum_{j=1}^n c_j\pmb{\mu_j}, \left(\sum_{j=1}^n c_j^2\right)\pmb{\Sigma}\right)$。令$\pmb{V_2} = b_1\pmb{X_1} + b_2\pmb{X_2} + \cdots + b_n\pmb{X_n}$，则$\pmb{V_1, V_2}$服从多元正态分布，协方差矩阵为
$$
\begin{bmatrix}
\left(\sum_{j=1}^n c_j^2\right)\pmb{\Sigma} &\pmb{(b'c)\Sigma} \\
\pmb{(b'c)\Sigma} & \left(\sum_{j=1}^n b_j^2\right)\pmb{\Sigma}
\end{bmatrix}
$$

