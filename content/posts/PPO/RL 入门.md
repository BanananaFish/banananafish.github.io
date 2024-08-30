---
date: 2024-08-30
draft: true
params:
  author: Bananafish
title: RL 入门
tags:
  - RL
series:
  - 初试 PPO
series_order: "1"
---
## 定义 RL 问题
![image.png](https://raw.githubusercontent.com/BanananaFish/image_repo/master/20240830113629.png)
最大化图片中这条轨迹 $\tau$ 获取的奖励，也就是说对于随机变量 $R(\tau)$，最大化它的期望 $\bar{R_\theta}=\sum_\tau R(\tau)p_\theta (\tau)$，其中概率分布是由一个深度模型 $\theta$ 实现的，对于每一个状态，该模型输出一个策略分布，然后 actor 从中进行采样作为当前的策略。

## 奖励最大化
要最大化奖励，我们可以使用梯度上升。具体来说就是对于奖励梯度：
$$
\nabla \bar{R}_{\theta}=\sum_{\tau} R(\tau) \nabla p_{\theta}(\tau)
$$
做：
$$ \theta \leftarrow \theta+\eta \nabla \bar{R}_{\theta} $$
而对于概率分布的梯度如何计算，就是进行一系列基础变换，最后把梯度计算转换成一个采样问题：
$$ \begin{aligned} \nabla \bar{R}_{\theta}&=\sum_{\tau} R(\tau) \nabla p_{\theta}(\tau)\\ &=\sum_{\tau} R(\tau) p_{\theta}(\tau) \frac{\nabla p_{\theta}(\tau)}{p_{\theta}(\tau)} \\ &= \sum_{\tau} R(\tau) p_{\theta}(\tau) \nabla \log p_{\theta}(\tau) \\ &=\mathbb{E}_{\tau \sim p_{\theta}(\tau)}\left[R(\tau) \nabla \log p_{\theta}(\tau)\right] \\ & \approx \frac{1}{N} \sum_{n=1}^{N} R\left(\tau^{n}\right) \nabla \log p_{\theta}\left(\tau^{n}\right) \\ &=\frac{1}{N} \sum_{n=1}^{N} \sum_{t=1}^{T_{n}} R\left(\tau^{n}\right) \nabla \log p_{\theta}\left(a_{t}^{n} \mid s_{t}^{n}\right) \end{aligned} $$