---
date: 2024-08-30
draft: true
params:
  author: Bananafish
title: PPO 入门
tags:
  - RL
  - LLM
series:
  - 初试 PPO
series_order: "2"
---
## 策略梯度的问题
1. 多次采样，一次参数更新，并且更新后原采样轨迹就废弃了（因为策略改变了）
2. 收敛很慢，效率低下
**于是**，我们使用异策略来进行采样和训练。

## 重要性采样
**问题**：如何用一个可采样的分布 $q$ 去近似一个不可采样的分布 $p$ 的随机变量期望

使用重要性采样：
$$
 \int f(x) p(x) \mathrm{d}x=\int f(x) \frac{p(x)}{q(x)} q(x) \mathrm{d}x=\mathbb{E}_{x \sim q}[f(x){\frac{p(x)}{q(x)}}]
$$
这样只需要在采样上乘重要性系数就能近似目标分布。注意，这个采样对于方差是有偏的，也就是：
$$ \begin{aligned} 
\operatorname{Var}_{x \sim q}\left[f(x) \frac{p(x)}{q(x)}\right] &=\mathbb{E}_{x \sim q}\left[\left(f(x) \frac{p(x)}{q(x)}\right)^{2}\right]-\left(\mathbb{E}_{x \sim q}\left[f(x) \frac{p(x)}{q(x)}\right]\right)^{2} \\ &=\mathbb{E}_{x \sim p}\left[f(x)^{2} \frac{p(x)}{q(x)}\right]-\left(\mathbb{E}_{x \sim p}[f(x)]\right)^{2} 
\end{aligned} $$
$p$ 和 $q$ **差距不大**时，方差第一项的系数可以忽略。


## 使用重要性采样来实现异策略
把策略梯度的采样换成重要性采样：
$$
 \nabla \bar{R}_{\theta}=\mathbb{E}_{\tau \sim p_{\theta^{\prime}(\tau)}}\left[\frac{p_{\theta}(\tau)}{p_{\theta^{\prime}}(\tau)} R(\tau) \nabla \log p_{\theta}(\tau)\right]
$$
$\theta'$ 作为“示范”，只负责采样，然后训练 $\theta$ ，从而实现多次采样，一次更新。训练过程为：
$$ \mathbb{E}_{\left(s_{t}, a_{t}\right) \sim \pi_{\theta^{\prime}}}\left[\frac{p_{\theta}\left(s_{t}, a_{t}\right)}{p_{\theta^{\prime}}\left(s_{t}, a_{t}\right)} A^{\theta}\left(s_{t}, a_{t}\right) \nabla \log p_{\theta}\left(a_{t}^{n} | s_{t}^{n}\right)\right] $$
就是从采样到的数据集中，训练 $\theta$ ，其中 $A ()$ 为减去基线后的奖励，称作优势。而可以假设对于每个模型，状态分布是相同的，即 $p_{\theta}(s_t) \approx p_{\theta'}(s_t)$，使用条件概率，就可以化简为：
$$ \mathbb{E}_{\left(s_{t}, a_{t}\right) \sim \pi_{\theta^{\prime}}}\left[\frac{p_{\theta}\left(a_{t} | s_{t}\right)}{p_{\theta^{\prime}}\left(a_{t} | s_{t}\right)} A^{\theta^{\prime}}\left(s_{t}, a_{t}\right) \nabla \log p_{\theta}\left(a_{t}^{n} | s_{t}^{n}\right)\right]
$$
这样就可以用策略网络（类似分类任务）的方式收集数据与优化了。

## PPO
可以看到，异策略方式的结果好坏和两个分布的相似程度息息相关。PPO 依靠添加约束，类似于正则化，让两个分布相似（KL 散度减小）。目标函数就是上面的增加一项：
$$ \begin{aligned} &J_{\mathrm{PPO}}^{\theta^{\prime}}(\theta)=J^{\theta^{\prime}}(\theta)-\beta \mathrm{KL}\left(\theta, \theta^{\prime}\right) \\ &J^{\theta^{\prime}}(\theta)=\mathbb{E}_{\left(s_{t}, a_{t}\right) \sim \pi_{\theta^{\prime}}}\left[\frac{p_{\theta}\left(a_{t} \mid s_{t}\right)}{p_{\theta^{\prime}}\left(a_{t} \mid s_{t}\right)} A^{\theta^{\prime}}\left(s_{t}, a_{t}\right)\right] \end{aligned} $$

## 变种
### PPO-clip
不使用正则项，而是使用一个 clip 操作，自适应的调节分布差异。