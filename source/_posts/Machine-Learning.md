---
title: Machine Learning
date: 2022-08-19 20:56:15
updated: 2022-08-19 20:56:15
mathjax: true
tags:
---

## 一些经典机器学习算法

### k-means

问题定义：给定一个观测数据集$\pmb{(x_1, x_2, ..., x_n)}$，k-means的目标是将这n个数据点分为k个类，即$S = \{S_1, S_2, ...,S_k\}$，使得within-cluster sum of squares（WCSS）最小
$$
\arg\min_{S} \sum_{i=1}^k\sum_{x\in S_i}|| \pmb{x - \mu_i}||^2
$$
直接求解比较困难，通常使用启发式算法进行求解，算法流程如下

1. 随机初始化$\pmb{\mu_1, \mu_2, ..., \mu_k}$
2. 对每个数据点$\pmb{x}$，将其类标签设为$\arg \min_{k} ||\pmb{x - \mu_k}||^2$
3. 重新计算类中心点，即$\pmb{\mu_k} = \frac{\sum_{x \in S_k} x}{|S_k|}$
4. 重复2、3，直到收敛

很容易就能证明，第2、3步都会使得目标函数减小，因此重复迭代能不断减小目标函数的值，使得聚类效果更好。

缺点：得到的聚类集合通常是凸集合，对一些狭长的分布不友好；初始化的值会对最终的聚类结果有很大影响
<!-- more -->
### Guassian Mixture Model

+ 类概率：$\pi = (\pi_1, \pi_2, ..., \pi_k)$
+ 类均值：$\pmb{\mu = (\mu_1, \mu_2, ..., \mu_k)}$
+ 类协方差：$\pmb{\Sigma = (\Sigma_1, \Sigma_2, ..., \Sigma_k)}$

记$\theta = (\pi, \pmb{\mu, \Sigma})$

问题定义：给定一个观测数据集$\pmb{X} = \pmb{(x_1, x_2, ..., x_n)}$。假设数据是由高斯混合分布生成，对$\theta$做极大似然估计。

高斯混合分布的密度函数为
$$
p(\pmb{x}) = \sum_{i=1}^k \pi_i \mathcal{N}(\pmb{x|\mu_i, \Sigma_k})
$$


首先写出似然函数
$$
\log p(\pmb{X} | \theta) = \log \prod_{i=1}^n p(\pmb{x}_i|\theta) = \sum_{i=1}^n \log p(\pmb{x_i} | \theta) = \sum_{i=1}^n \log \sum_{j=1}^k \pi_j \mathcal{N}(\pmb{x_j|\mu_i, \Sigma_k})
$$
直接对似然函数求最大值点非常困难，所以在此使用EM算法进行求解
$$
\begin{align}
\log p(\pmb{x}|\theta) &= \log\left[\sum_{z}p(\pmb{x}, z|\theta)\right] \\
&= \log\left[\sum_{z}q(z)\frac{p(\pmb{x}, z|\theta)}{q(z)}\right] \\
&\geq \sum_z q(z) \log(\frac{p(\pmb{x}, z|\theta)}{q(z)}) := \mathcal{L}(q, \theta)
\end{align}
$$
EM算法：$\arg\max_{\theta}\left[\max_q\mathcal{L}(q, \theta)\right]$，迭代这个过程直到收敛，可以证明每一次迭代，似然函数都会增大

$$
\mathcal{L}(q, \theta) = -KL[q(z) \ || \ p(z|\pmb{x}, \theta)] + \log p(\pmb{x}|\theta)
$$

+ Expectation Step
  + $q^\star(z) = p(z|x, \theta_{old})$
  + 令$J(\theta) := \mathcal(q^\star, \theta) = \sum_{z} q^\star(z) \log\left(\frac{p(x,z|\theta)}{q^\star(z)}\right)$
+ Maximization Step
  + $\theta_{new} = \arg\max_{\theta} J(\theta)$

{% asset_img EM-GMM.png %} 