---
title: 最大熵模型与逻辑回归模型的等价性证明
date: 2017-06-24 16:41:05
tags: 最大熵模型
categories: 学习
---

# 最大熵模型与逻辑回归模型等价性证明

假设现在有一个二分类问题，即$y\in \{0, 1\}$，而$x \in \mathcal{R}^N$

（1）在LR模型中，假设参数为$\theta \in \mathcal{R}^N$，则有模型定义为：

$$P(y=1|x) = \frac{\exp(\theta^Tx)}{1 + \exp(\theta^Tx)}$$

$$ P(y=0|x) = 1 - P(y=1|x) =\frac{1}{1 + \exp(\theta^Tx)}$$

（2）对于ME模型，其定义为：

$$P(y|x,w)=\frac{1}{Z\_w(x)}\cdot \exp(\sum\_{i=1}^{n} w\_i f\_i(x,y))$$

其中$Z\_w(x) = \sum\_y \exp(\sum\_{i=1}^{n} w\_i f\_i(x,y))$。

## 证明：

令ME模型的特征函数的个数$n=N$，即x的维度，定义任意一个特征函数为：

$$f\_i(x,y)=\left \\{ \begin{matrix}g(x,i) & y=1 \newline 0 & y=0 \end{matrix} \right.$$

其中函数$g(x,i)=x\_i$，也即返回$x$第$i$维上的数值。

则有：
$$P(y=1|x,w) = \frac{\exp(\sum\_{i=1}^{n} w\_i g\_i(x,i))}{\exp(\sum\_{i=1}^{n} w\_i g\_i(x,i))+\exp(\sum\_{i=1}^{n} w\_i\cdot 0))}=\frac{\exp(w^Tx)}{1+\exp(w^Tx)}$$

$$P(y=0|x,w) = \frac{\exp(\sum\_{i=1}^{n} w\_i\cdot 0))}{\exp(\sum\_{i=1}^{n} w\_i g\_i(x,i))+\exp(\sum\_{i=1}^{n} w\_i\cdot 0))}=\frac{1}{1+\exp(w^Tx)}$$

此时ME模型的形式与LR模型等价。