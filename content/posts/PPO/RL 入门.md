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
要最大化奖励，我们可以使用梯度上升。具体来说就是对于：
$$

$$