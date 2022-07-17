---
title: Variational Inference
date: 2022-07-10 11:22:22
updated: 2022-07-10 11:22:22
mathjax: true
tags:
---

假设随机变量$X$和$Z$服从联合分布$p(x, z; \theta)$，其中$\theta$是分布的参数。我们可以得到$X$的观测值，但不能得到$Z$的观测值，即$X$为观测变量，$Z$为隐变量。给定$\theta$，并且有观测值$x$，我们希望得到$Z$的后验分布$p(z; x, \theta)$。通常这个后验分布无法直接计算，但可以通过一个变分分布$q(z)$近似。'
<!-- more -->

### Kullback-Leibler divergence (KL散度)

对于离散分布$P$和$Q$，KL散度定义为
$$
D_{KL}\left(P \ ||\ Q\right) = \sum_{x \in \mathcal{X}} P(x)\log\left(\frac{P(x)}{Q(x)}\right)
$$
对于连续分布$P$和$Q$，KL散度定义为
$$
D_{KL}\left(P \ ||\ Q\right) = \int_{-\infty}^{+\infty} P(x)\log\left(\frac{P(x)}{Q(x)}\right) dx
$$

### Evidence Lower Bound (ELBO)

evidence定义为$\log p(x; \theta)$。假设随机变量$Z$服从分布$q$，则可以推出evidence的一个下界。
$$
\begin{align*}
\log p(x; \theta) &= \log \int p(x, z; \theta) dz \\
&= \log \int q(z) \frac{p(x, z; \theta)}{q(z)} dz \\
&= \log \mathbb{E}_{Z\sim q}\left[\frac{p(x, Z)}{q(Z)}\right] \\
&\geq \mathbb{E}_{Z\sim q}\left[\log \frac{p(x, Z)}{q(Z)} \right] \tag{Jensen's Inequality} \\
\end{align*}
$$
**Jenson' s Inequality: **$\mathbb{E}[\varphi(X)] \geq \varphi(\mathbb{E}[X])$，其中$\varphi$是一个凸函数。

将$\mathbb{E}_{Z\sim q}\left[\log \frac{p(x, Z)}{q(Z)}\right]$ 定义为**ELBO**，显然，ELBO和evidence之间存在一个gap，而这个gap变分分布$q(z)$和真实分布$p(z;x, \theta)$的KL散度，推导如下。
$$
\begin{align*}
KL(q(z) \ || \ p(z|x, \theta)) &:= \mathbb{E}_{Z\sim q}\left[\log \frac{q(Z)}{p(Z; x, \theta)} \right] \\
&= \mathbb{E}_{Z\sim q}\left[\log q(Z) \right] - \mathbb{E}_{Z\sim q}\left[\log\frac{p(x, Z; \theta)}{p(x; \theta)} \right] \\
&= \mathbb{E}_{Z\sim q}\left[\log q(Z) \right] - \mathbb{E}_{Z\sim q}\left[\log p(x, Z; \theta) \right] + \mathbb{E}_{Z\sim q}\left[\log p(x; \theta) \right] \\
&= \log p(x; \theta) - \mathbb{E}_{Z\sim q}\left[\log \frac{p(x, Z; \theta)}{q(Z)} \right] \\
&= evidence - ELBO
\end{align*}
$$

#### Variational Inference

在得到观测数据$x$之后，我们希望做极大似然估计求$\theta$，但由于边缘分布$p(x; \theta) = \frac{p(x,z; \theta)}{p(z;x, \theta)}$无法直接计算，所以希望使用一个变分分布$q(z)$去近似$p(z; x, \theta)$，从而能够计算$p(x; \theta)$。为了使$q(z)$较好地近似$p(z; x, \theta)$，可以通过减小它们之间的KL散度实现，也即最大化ELBO。

#### Expectation-Maximization (EM算法)

+ **Expectation Step: **$q^* = \arg\max_{q} ELBO =  \arg\max_{q} \mathbb{E}_{Z\sim q}\left[\log \frac{p(x, Z; \theta)}{q(Z)} \right]$
+ **Maximization Step: **$\theta_{new} = \arg \max_{\theta} ELBO = \arg\max_{\theta}\mathbb{E}_{Z\sim {q^\star}}\left[\log \frac{p(x, Z; \theta)}{q^\star(Z)} \right]$
+ 重复这个过程直到收敛

E步相当于在给定$\theta$的情况下，找到一个最优的$q(z)$，去逼近$p(z;x, \theta)$，使得通过变分得到估计尽量准确；M步相当于，在得到了一个较为准确的估计的条件下，做极大似然估计。

参考文章：[ELBO](https://mbernste.github.io/posts/elbo/)