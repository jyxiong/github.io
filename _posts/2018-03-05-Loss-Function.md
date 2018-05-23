---
layout: post
title: Loss Function
key: 20180305
tags: 
  - MXNet & Gluon
  - CNN
---

## 损失函数
在机器学习算法中都会有一个目标函数，算法的求解过程就是对目标函数的优化过程。在分类或回归任务中，输出的预测值与真实值之间的差距可以用损失函数来度量，此时损失函数即为优化的目标函数。不同算法的损失函数也不同。

### 0-1损失函数
$$
L(y, f(x)) = \begin{cases} 0, & \text {if $ y = f(x) $;} \\ 1, & \text{if $ y \neq f(x) $.} \end{cases}
$$

### Hinge损失函数
$$
L(z) = \text {max} (0, 1 - z)
$$

### 平方损失函数
$$
L(z) = z^2
$$

### 指数损失函数
$$
L(z) = \begin{cases} 1, & \text {if $z$ < 0;} \\ 0, & \text{otherwise.} \end{cases}
$$

### 对数损失函数
$$
L(z) = \begin{cases} 1, & \text {if $z$ < 0;} \\ 0, & \text{otherwise.} \end{cases}
$$


