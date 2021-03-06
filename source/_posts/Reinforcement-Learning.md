---
title: Reinforcement Learning
date: 2022-06-20 17:29:09
updated: 2022-07-17 21:42:21
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

### Temporal Difference Learning (时序差分学习)

与蒙特卡洛方法一致，时序差分方法也可以直接从与环境互动的经验中学习策略，而不需要构建关于环境动态特性的模型。与动态规划相一致的是，时序差分方法也可以基于已得到的其他状态的估计值来更新当前状态的价值函数。在时序差分学习算法中，n-步算法将TD方法和MC方法联系在一起、TD（$\lambda$）能无缝地统一TD方法和MC方法。

MC方法更新公式：
$$
V(S_t) \gets V(S_t) + \alpha \left[G_t - V(S_t)\right]
$$
TD方法更新公式：
$$
V(S_t) \gets V(S_t) + \alpha \left[R_{t+1} + \gamma V(S_{t+1}) - V(S_t) \right]
$$
其中$\delta_t = R_{t+1} + \gamma V(S_{t+1}) - V(S_t)$也称作TD error。

#### TD(0)

更新公式：$V(S_t) \gets V(S_t) + \alpha \left[R_{t+1} + \gamma V(S_{t+1}) - V(S_t) \right]$

{% asset_img TD(0).png %}

#### Sarsa

更新公式：$Q(S_t, A_t) \gets Q(S_t, A_t) + \alpha\left[R_{t+1} + \gamma Q(S_{t+1}, A_{t+1}) - Q(S_t, A_t)\right]$

{% asset_img sarsa.png %}

#### Q-Learning

更新公式：$Q(S_t, A_t) \gets Q(S_t, A_t) + \alpha\left[R_{t+1} + \gamma \max_{a'}Q(S_{t+1}, a') - Q(S_t, A_t)\right]$

{% asset_img q-learning.png %}

### 函数近似

对价值函数进行参数化建模，用参数为$\theta$的函数$\hat{v}(s, \pmb{w})$去近似$v_\pi(s)$。指定一个状态的分布$\mu(s) \geq 0, \sum_{s} \mu(s) = 1$来表示我们对于每个状态s的误差的重视程度。一个自然的目标函数为均方价值误差，记为$VE$
$$
VE(\pmb{w}) = \sum_{s\in S}\mu(s)\left[v_\pi(s) - \hat{v}(s, \pmb{w})\right]^2
$$
假设在样本中的分布$\mu$和VE中的分布相同。在这种情况下，一个好的策略就是尽量减少观测到的样本的误差。使用SGD方法进行优化
$$
\begin{align*}
\pmb{w}_{t+1} &= \pmb{w}_t - \frac{1}{2}\alpha\nabla\left[v_\pi(S_t) - \hat{v}(S_t, \pmb{w}_t)\right]^2 \\
&= \pmb{w}_t - \alpha\left[v_\pi(S_t) - \hat{v}(S_t, \pmb{w}_t)\right]\nabla\hat{v}(S_t, \pmb{w}_t)
\end{align*}
$$

由于我们无法得到$v_\pi(S_t)$，所以使用它的一个随机近似$U_t$替代，如果$U_t$是一个无偏估计，那么收敛性是有保证的。可以取$U_t$为$G_t$，这种方法被称做梯度方法。如果采用自举估计值，取$U_t$为$R_{t+1}+\hat{v}(S_{t+1}, \pmb{w})$，则没有足够的收敛性保证，但在大多数情况下，都会收敛，这种方法称为半梯度方法。
### Policy Gradient

基于动作价值函数的方法，都是先学习动作价值函数，然后根据估计的动作价值函数选择动作，策略的获得依赖于价值函数。策略梯度方法对策略进行参数化建模，通过梯度上升的方式直接对策略进行学习。用$\pmb{\theta}$表示策略的参数向量。

策略参数的学习方法都基于某种性能度量$J(\pmb{\theta})$的梯度，性能度量可以是从初始状态s到终止状态s'的return的期望。在能够分出episode和连续型的条件下，性能度量会有所不同。在得到梯度后，通过梯度上升，不断对策略的参数进行优化
$$
\pmb{\theta_{t+1}} = \pmb{\theta_{t}} + \alpha \widehat{\nabla J(\pmb{\theta_{t}})}
$$
其中，$\widehat{\nabla J(\pmb{\theta_{t}})}$是对$\nabla J(\pmb{\theta_{t}})$的随机估计。

#### 策略梯度定理

在分幕式情况下策略梯度定理表达式如下
$$
\nabla J(\pmb{\theta}) \propto \sum_s \mu(s) \sum_{a} q_\pi(s, a)\nabla \pi (a|s, \pmb{\theta})
$$
其中，$\mu(s)$表示在使用策略$\pi$的条件下，s出现在轨迹中的概率。

#### REINFORCE

由于$\mu(s)$是将目标策略$\pi$下每个状态出现的频率作为加权系数的求和项，如果按照策略$\pi$执行，则状态将按这个比例出现。假设$\gamma$为1即没有折扣，根据策略梯度定理
$$
\begin{align*}
\nabla J(\pmb{\theta}) &\propto \sum_s \mu(s) \sum_{a} q_\pi(s, a)\nabla \pi (a|s, \pmb{\theta}) \\
&= \mathbb{E}_\pi\left[\sum_{a} q_\pi(S_t, a)\nabla \pi (a|S_t, \pmb{\theta})\right] \\
&= \mathbb{E}_\pi\left[\sum_{a} \pi(a|S_t, \pmb{\theta})q_\pi(S_t, a)\frac{\nabla \pi (a|S_t, \pmb{\theta})}{\pi(a|S_t, \pmb{\theta})}\right] \\
&= \mathbb{E}_\pi\left[q_\pi(S_t, A_t)\frac{\nabla \pi (A_t|S_t, \pmb{\theta})}{\pi(A_t|S_t, \pmb{\theta})}\right] \tag{用采样$A_t \sim \pi$替换$a$} \\
&= \mathbb{E}_\pi\left[G_t\frac{\nabla \pi (A_t|S_t, \pmb{\theta})}{\pi(A_t|S_t, \pmb{\theta})}\right] \tag{$\mathbb{E}_\pi\left[G_t|S_t, A_t\right] = q_\pi(S_t, A_t)$} \\
&= \mathbb{E}_\pi\left[G_t\nabla \ln \pi (A_t|S_t, \pmb{\theta})\right] \tag{$\nabla\ln x = \frac{\nabla x}{x}$}
\end{align*}
$$

则可以得到REINFORCE算法
{% asset_img Policy-Gradient.png %}

#### REINFORCE with baseline

根据策略梯度定理
$$
\begin{align*}
\nabla J(\pmb{\theta}) &\propto \sum_s \mu(s) \sum_{a} q_\pi(s, a)\nabla \pi (a|s, \pmb{\theta}) \\
&= \sum_s \mu(s) \sum_{a} (q_\pi(s, a)-b(s))\nabla \pi (a|s, \pmb{\theta})
\end{align*}
$$
由此导出更新公式
$$
\pmb{\theta}_{t+1} = \pmb{\theta}_{t} + \alpha\left(G_t - b(S_t)\right)\frac{\nabla \pi (A_t|S_t, \pmb{\theta}_t)}{\pi(A_t|S_t, \pmb{\theta}_t)}
$$
其中的$b(s)$可以是不随动作$a$变化的任意函数。因为这个baseline也可以是常量0，所以上式是REINFORCE算法更一般的推广。通常来说，加入baseline不会使更新的期望值发生变化，但是对方差会有很大影响。

{% asset_img Policy-Gradient-w-baseline.png %}

#### Actor-Critic

尽管带基线的强化学习方法既学习了一个策略函数也学习了一个状态价值函数，我们也不认为它是一种AC方法，因为它的状态价值函数仅被用作baseline，而不是作为一个Critic，也即没有被用于自举操作。只有当采用自举法时，才会出现依赖于函数逼近质量的偏差和渐进性收敛。引入自举，往往能减小方差和加速收敛。

考虑单步AC方法，使用单步回报代替$G_t$，则得到如下更新公式
$$
\begin{align*}
\pmb{\theta}_{t+1} &= \pmb{\theta}_{t} + \alpha\left(G_{t:t+1} - \hat{v}_(S_t, \pmb{w})\right)\frac{\nabla \pi (A_t|S_t, \pmb{\theta}_t)}{\pi(A_t|S_t, \pmb{\theta}_t)} \\
&= \pmb{\theta}_{t} + \alpha\left(R_{t+1} + \hat{v}(S_{t+1}, \pmb{w}) - \hat{v}_(S_t, \pmb{w})\right)\frac{\nabla \pi (A_t|S_t, \pmb{\theta}_t)}{\pi(A_t|S_t, \pmb{\theta}_t)} \\
&= \pmb{\theta}_{t} + \alpha\delta_t\frac{\nabla \pi (A_t|S_t, \pmb{\theta}_t)}{\pi(A_t|S_t, \pmb{\theta}_t)}
\end{align*}
$$
进而可以得到如下算法

{% asset_img Actor-Critic.png %}

### Deep Reinforcement Learning (DRL)

#### Deep Q-Learning (DQN)

#### Deep Deterministic Policy Gradient (DDPG)

#### Trust Region Policy Gradient (TRPO)

#### Proximal Policy Gradient (PPO)

#### Soft Actor-Critic (SAC)

参考文献：

1. [《Reinforcement Learning: An Introduction》](https://web.stanford.edu/class/psych209/Readings/SuttonBartoIPRLBook2ndEd.pdf)

