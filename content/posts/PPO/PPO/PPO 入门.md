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

## 重要性采样
**问题**：如何用一个可采样的分布 $q$ 去近似一个不可采样的分布 $q$ 的随机变量期望