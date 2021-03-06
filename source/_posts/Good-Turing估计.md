---
title: Good-Turing估计
date: 2016-05-13 14:04:57
tags: 数据平滑
categories: 学习
---

## 使用HMM的中文词性标注程序

这两天做了一个使用隐马尔可夫模型（HMM）的中文词性标注的简单程序，虽说是一个试水性质的探索，但这其中的某些小细节仍值得研究。

有关HMM的模型介绍不再赘述，对于中文词性标注，我们做出的假设是：文本中的句子是观测状态序列，句子中的每一个词是一个观测状态，其词性标注就是隐含状态。那么要得到这个模型的具体信息，我们需要得到$(\pi, A, B)$这三个模型参数。

### 参数计算

设隐含状态空间为$S$，$\pi$是HMM的初始概率矩阵。$\pi_i$为隐含状态$i$在0时刻（即初始时刻）时出现的概率，可以使用如下公式进行计算：

$$ \pi\_i = \frac{\sharp ( s\_t=i|t=0 ) } {\sum\_{j\in S} \sharp (s\_t=j|t=0)} $$

其中$\sharp(s_t=i|t=0)$表示t=0时刻，状态$i$出现的次数。

$A$是HMM的转移概率矩阵，$A_{ij}$表示从隐含状态$i$到$j$的转移概率，使用如下公式计算：

$$A\_{ij} = P(s\_t=j | s\_{t-1} = i) = \frac{\sharp (s\_t=j , s\_{t-1} = i ) }{\sharp (s\_{t-1} = i ) }$$

$B$是HMM的发射概率矩阵，设词空间为$W$，第i个词为$c_i$，则有：

$$ B\_{ij} = P (c\_i | s=j ) = \frac{\sharp ( c\_i, s=j )}{\sharp (s=j)}$$

表示词$c_i$在状态$s=j$的情况下的发射概率。

### 数据稀疏问题

按照1.1节中的计算方法，从语料中统计各个标注与词的共现情况，可以很容易的得到大部分概率。因为语料库大小、语法规则等原因，会出现很明显的数据稀疏问题。例如，中文词语的词性大多只有一个，那么某个词的发射概率在不是该标注的情况下就会为零，但也不能说它就不可能被标注为其他词性，因为语言是存在有限的变化的；此外，因为语料库大小的原因，不可能包含所有可能的语法现象，这也使得某些特定的情况没有记录，从而导致其相关概率为零。

**没有见过不代表不存在**，因此这些数据稀疏问题需要使用特殊方法去解决，而不是简单粗暴地认为其不可能发生。这时，就需要使用数据平滑方法来解决稀疏问题。常见的平滑方法有：拉普拉斯平滑（加1平滑）和古德图灵平滑。

因为实验中发现，拉普拉斯平滑的效果不如古德图灵平滑，本文主要讲述古德图灵平滑。

## 古德图灵估计

古德-图灵（Good-Turing）估计法是很多平滑技术的核心，于1953年有古德（I.J.Good）引用图灵（Turing）的方法而提出来的。其基本思想是：对于没有看见的事件，我们不能认为它发生的概率就是零，因此我们从概率的总量（Probability Mass）中，分配一个很小的比例给这些没有看见的事件（如下图）。这样一来看的见概率总和就要小于1了，因此，需要将所有看见的事件概率调小一点。至于小多少，要根据“越是不可信的统计折扣越多”的方法进行。

![分配](http://images.cnitblog.com/blog/408927/201412/202122413296010.png)

以统计词典中的每个词的概率为例，假设语料库中出现r次的词有$N_r$个，特别地，未出现的词数量为$N_0$。语料库的大小为$N$。那么很显然：

$$N = \sum_{r=1}^{\infty}{rN_r}$$

出现r次的词在整个语料库上的相对频度（Relative Frequency）则是r/N，如果不做任何处理，就以这个相对频度作为这些词的概率估计。

现在假设当r比较小时，它的估计可能不可靠，因此在计算出现r次的词的概率时，要使用一个更小的系数$d_r$（而不是直接使用r），古德图灵估计按照下面的公式计算$d_r$:

$$d\_r = (r+1) \frac{N\_{r+1}}{N\_r}$$

显然有，

$$\sum\_r d\_r N\_r = N$$

一般来说，出现一次的词的数量比出现两次的多，出现两次的比三次的多。这种规律称为Zipf定律（Zipf's Law）。下图是一个小语料上出现r次的词的数量$N_r$和r的关系。

![Zipf's Law](http://images.cnitblog.com/blog/408927/201412/202211207981424.png)

可以看出r越大，词的数量$N\_r$越小，即$N\_{r+1} < N\_r$。因此，一般情况下$d_r < r$，而$d_0 > 0$。这样就给为出现的词赋予了一个很小的非零值，从而解决了零概率的问题。同时下调了出现概率很低的词的概率。

当然，在实际的自然语言处理中，一般会设置一个阈值T，仅对出现次数小于T的词做上述调整。并且，因为实际语料的统计情况使得$N_{r+1} < N_r$不一定成立，$N_r = 0$情况也可能出现，所以需要使用曲线拟合的方式替换掉原有的$N_r$，并使用如下Kartz退避公式计算$d_r$:

$$d\_r = \frac{(r+1)\frac{N\_{r+1}}{N\_r}-r\frac{(k+1)N\_{k+1}}{N\_1}}{1-\frac{(k+1)N\_{k+1}}{N\_1}}, 1 \le r \le k$$

