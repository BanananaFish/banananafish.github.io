---
date: 2024-08-30
draft: false
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
做梯度上升：
$$
 \theta \leftarrow \theta+\eta \nabla \bar{R}_{\theta}
$$
而对于概率分布的梯度如何计算，就是进行一系列基础变换，最后把梯度计算转换成一个采样问题。其中策略对数概率分布的梯度可以使用反向传播来计算：
$$
\begin{aligned}
\nabla \bar{R}_{\theta}&=\sum_{\tau} R(\tau) \nabla p_{\theta}(\tau)\\ &=\sum_{\tau} R(\tau) p_{\theta}(\tau) \frac{\nabla p_{\theta}(\tau)}{p_{\theta}(\tau)} \\ &= \sum_{\tau} R(\tau) p_{\theta}(\tau) \nabla \log p_{\theta}(\tau) \\ &=\mathbb{E}_{\tau \sim p_{\theta}(\tau)}\left[R(\tau) \nabla \log p_{\theta}(\tau)\right] \\ & \approx \frac{1}{N} \sum_{n=1}^{N} R\left(\tau^{n}\right) \nabla \log p_{\theta}\left(\tau^{n}\right) \\ &=\frac{1}{N} \sum_{n=1}^{N} \sum_{t=1}^{T_{n}} R\left(\tau^{n}\right) \nabla \log p_{\theta}\left(a_{t}^{n} \mid s_{t}^{n}\right)
\end{aligned}
$$
在实现上来说，我们把策略问题建模成分类问题，我们最大化**采样到的策略**和**实际选择的策略**之间的似然（使用交叉熵计算，也就是最小化交叉熵），并且把似然乘上这次采样到轨迹的**奖励**。再进行梯度上升，在加权了奖励后，使得网络的策略分布向**获取最大的奖励**靠拢。这就实现了**策略梯度**。


## 经典算法：**REINFORCE**
![image.png](https://raw.githubusercontent.com/BanananaFish/image_repo/master/20240830123219.png)
区别在于，$G_t$ 计算该动作之后获取的奖励量，而非直接用整条轨迹的奖励量来加权。说白了，对比监督学习，强化学习不是靠标签和预测之间的差，而是靠**未来的奖励加权**来保证梯度方向正确。

## 参考
[EasyRL](https://datawhalechina.github.io/easy-rl)