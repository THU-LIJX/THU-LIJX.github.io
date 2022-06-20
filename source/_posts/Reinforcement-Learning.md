---
title: Reinforcement Learning
date: 2022-06-20 17:29:09
updated: 2022-06-20 17:29:09
mathjax: true
tags:
---

###  有限马尔可夫决策过程（Markov Decision Process, MDP）

一个MDP可以用一个四元组表示$M = < S, A, P, R>$，其中
+ *S*: 一个有限的状态集合，$S = \{s_1, s_2, \cdots, s_n\}$
+ *A*: 一个有限的动作集合，$A = \{a_1, a_2, \cdots, a_m\}$
+ *P*: 状态转移概率，$P_{ss'}^a = Pr\left[S_{t+1} = s' | S_t = s, A_t = a\right]$
+ *R*: Reward函数，$R_s^a = E\left[R_{t+1} | S_t = s, A_t = a\right]$

定义Return为
$$
    G_t = R_{t+1} + \gamma R_{t+2} + \gamma^2R_{t+3} + \cdots = \sum_{k=0}^\infty \gamma^k R_{t+k+1}
$$
性质：$G_t = R_{t+1} + \gamma G_{t+1}$，其中$0 \leq \gamma \leq 1$, 当$\gamma=1$时，需要确保每条序列都终止。
<!-- more -->

#### Value Function

令策略为$\pi$。

$$
\begin{align*}
    &v_{\pi}(s) = \mathbb{E_\pi}\left[G_t|S_t = s\right] \\
    &q_{\pi}(s, a) = \mathbb{E_\pi}\left[G_t \ | \ S_t=s, A_t = a \right]
\end{align*}
$$

#### Bellman Equation

$$
\begin{align*}
    &v_{\pi}(s) = \sum_{a\in A} \pi(a|s) (R_s^a + \gamma\sum_{s'\in S}P_{ss'}^av_\pi(s')) \\
    &q_{\pi}(s,a) = R_s^a + \gamma\sum_{s'\in S} P_{ss'}^a \sum_{a' \in A} \pi(a'|s')q_\pi(s', a')
\end{align*}
$$

写成期望形式
$$
\begin{align*}
    &v_\pi(s) = \mathbb{E_\pi}[R_{t+1} + \gamma v_\pi(S_{t+1})| S_t = s] \\
    &q_{\pi}(s, a) = \mathbb{E}[R_{t+1} + \gamma q_\pi(S_{t+1}, A_{t+1})|S_t = s, A_t = a]
\end{align*}
$$

写成矩阵形式
$$
\begin{bmatrix}
    v(s_1) \\
    \vdots \\
    v(s_n)
\end{bmatrix} = 
\begin{bmatrix}
    \mathcal{R}_{s_1} \\
    \vdots \\
    \mathcal{R}_{s_n}
\end{bmatrix} + \gamma
\begin{bmatrix}
    P_{s_1s_1} & \cdots & P_{s_1s_n} \\
    \vdots & & \vdots \\
    P_{s_ns_1} & \cdots & P_{s_ns_n} \\
\end{bmatrix}
\begin{bmatrix}
    v(s_1) \\
    \vdots \\
    v(s_n) 
\end{bmatrix}
$$

其中$\mathcal{R}_s = \mathbb{E}[R_{t+1} | S_t = s]$, $P_{ss'} = Pr[S_{t+1}=s' | S_t = s]$。求解复杂度为$O(n^3)$，通常不会使用。

#### Optimal Policies and Optimal Value Function

$\pi \geq \pi'$当且仅当对于任意s，都满足$v_\pi(s) > v_{\pi'}(s)$

记最优策略为$\pi_{*}$，则有
$$
\begin{align*}
    &v_{\pi_*}(s) = \max_{\pi} v_{\pi}(s) \\
    &q_{\pi_*}(s, a) = \max_{\pi} q_{\pi}(s, a)
\end{align*}
$$

#### Bellman Optimal Equation

$$
\begin{align*}
    &v_*(s) = \max_{a} q_{\pi_*}(s, a) \\
    &q_*(s, a) = R_s^a + \gamma\sum_{s' \in S} P_{ss'}^a v_*(s')
\end{align*}
$$

### 使用动态规划方法求解MDP

#### 策略评估（Policy Evaluation）

策略评估指的是给定一个策略$\pi$，求出其价值函数$v_\pi$的过程。根据Bellman Equation，可以使用如下更新公式
$$
    v_{k+1}(s) = \sum_{a\in A} \pi(a|s) (R_s^a + \gamma\sum_{s'\in S}P_{ss'}^av_k(s'))
$$
具体计算时，既可以采用两个数组分别记录$v_{k+1}, v_k$，也可以只用一个数组就地更新，两种方式都可以收敛到$v_\pi$, 但收敛速度会有所不同。

#### 策略提升（Policy Improvement）

策略提升指的是根据给定策略$\pi$，得到一个新的策略$\pi'$，使得$\pi' \geq \pi$。

使用如下方式对策略进行更新
$$
\begin{align*}
    \pi'(s) &:= \text{argmax}_{a} q_\pi(s, a) \\
            &= \text{argmax}_{a} R_s^a + \gamma \sum_{s' \in S}P_{ss'}^av_\pi(s')
\end{align*}
$$

上述方法是有效的，证明如下，假设$\pi$和$\pi'$都是确定性策略，且对于任意$s\in S$，都有
$$
    q_\pi(s, \pi'(s)) \geq v_\pi(s)
$$
则
$$
\begin{align*}
    v_\pi(s) &\leq q_\pi(s, \pi'(s)) \\
             &\leq \mathbb{E}[R_{t+1} + \gamma v_\pi(S_{t+1}) | S_t=t, A_t=\pi'(s)] \\
             &\leq \mathbb{E_{\pi'}}[R_{t+1} + \gamma q_\pi(S_{t+1}, \pi'(S_{t+1})) | S_t=s] \\
             &\leq \mathbb{E_{\pi'}}[R_{t+1} + \gamma\mathbb{E}[R_{t+2} + \gamma v_\pi(S_{t+2})| S_{t+1}, A_{t+1}=\pi'(S_{t+1})] | S_t=s] \\
             &\leq \mathbb{E_{\pi'}}[R_{t+1} + \gamma R_{t+2} + \gamma^2 v_\pi(S_{t+2}) | S_t=s] \\
             &\leq \mathbb{E_{\pi'}}[R_{t+1} + \gamma R_{t+2} + \gamma^2 R_{t+2} + \gamma^3 R_{t+3} + \cdots | S_t=s, A_t=\pi'(s)] \\
             &= v_{\pi'}(s)
\end{align*}
$$

#### 策略迭代（Policy Iteration）

1. 策略评估
2. 策略提升
3. 重复1、2直到收敛

#### 价值迭代（Value Iteration）

使用Bellman Optimal Equation直接计算出$v_{*}(s)$, 迭代公式为
$$
    v_{k+1}(s) := \max_a R_s^a + \gamma \sum_{s' \in S} P_{ss'}^av_k(s)
$$

令$\pi_{*}(s)$为
$$
   \pi_{*}(s) = \text{argmax}_a q_*(s, a) 
$$
