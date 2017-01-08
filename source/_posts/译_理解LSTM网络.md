---
title: 翻译理解LSTM网络
date: 2017-01-08 21:29:03
tags: LSTM
categories: 学习
---

# 理解LSTM网络

> 本文译自：[colah's blog: Understanding LSTM Networks](http://7xnh8y.com1.z0.glb.clouddn.com/http://colah.github.io/posts/2015-08-Understanding-LSTMs/) Posted on August 27, 2015.

## 循环神经网络(Recurrent Neural Networks)

人类并不是每时每刻从空白处开始思考的。在阅读这篇文章时，你如何理解每个词都基于你对其之前的词的理解。你并不会将所有的思想丢弃而开始重新思考。人类的思想是具有持续性的。

传统的神经网络模型不能以这样的方式运作，这似乎是一个巨大的弊端。例如，想象你试图区分电影中每个时间点发生的事件的类别。传统的神经网络应该很难用于处理这个问题——使用电影中先前的事件推断后续的事件。

循环神经网络（Recurrent Neural Networks，以下简称为RNN）解决了这个问题。RNN自身包含循环，允许信息持久存在。

![RNN 包含循环](http://7xnh8y.com1.z0.glb.clouddn.com/RNN have loops.png)

**图1 RNN 包含循环**

如上图所示是一个神经网络的模块，A读入某个输入$x_t$，并输出一个值$h_t$。A上的循环可以使得信息从当前时刻传递到下一时刻。

这些循环使得循环神经网络看起来有些神秘。但只要你进一步思考就会发现它与普通的神经网络没有那么大的却别。一个循环神经网络可以看作是多个相同网络结点的复制，其中每一个网络结点会将信息传递给其后继网络结点。考虑展开一个循环神经网络：

![展开循环神经网络](http://7xnh8y.com1.z0.glb.clouddn.com/RNN-unrolled.png)

**图2 展开循环神经网络**

链式的结构特征揭示了 RNN 本质上是与序列和列表相关的。对于这类数据，它们是最自然的神经网络架构。

并且RNN也确实被这样使用了！在过去的几年中，RNN在应用于各类问题中取得了不可思议的成功：语音识别、语言建模、机器翻译和图像描述等等。这份列表还在不断增长中。我建议大家参考Andrej Karpathy的博文——[The Unreasonable Effectiveness of Recurrent Neural Networks](http://7xnh8y.com1.z0.glb.clouddn.com/http://karpathy.github.io/2015/05/21/rnn-effectiveness/)，领略RNN丰富有趣的特征。

这些成功的关键之处就是LSTM的应用，这是一种特殊的RNN，比标准的RNN模型在很多任务上都有更好的表现。几乎所有基于RNN取得的令人激动的成果都是通过LSTM达到的。这篇文章将会介绍LSTM。

## 长期依赖问题(The Problem of Long-Term Dependencies)

RNN似乎有能力可以将先前的信息连接到当前的任务上，例如使用过去的视频画面来推测对当前画面的理解。如果RNN可以做到，那么它们将变得非常有用。但它们真的可以吗？这还有许多依赖因素。

有时候，我们只需要知道先前的信息来完成当前的任务。例如，考虑我们有一个基于当前词预测下一个词的语言模型。如果我们试着预测句子“the cloud are in the *sky*,”中的最后一个词，我们并不需要更多的上下文——显然下一个词应该是*sky*。在类似的例子中，相关信息和预测词的位置之间的间隔非常小，RNN可以学会使用过去的信息。

![RNN短期依赖](http://7xnh8y.com1.z0.glb.clouddn.com/RNN-shorttermdepdencies.png)

但在一些更加复杂的情况下，我们可能需要更多的上下文信息。假设我们试图预测一段文本“I grew up in France… I speak fluent *French*.”中的最后一个词*French*。最近的信息建议下一个词可能是一种语言的名字，但是要弄清楚具体是那种语言时，我们就需要离当前位置很远的上下文信息“France”。这说明相关信息和预测位置之间的距离可能会非常大。

不幸的是，当这个距离不断增大到一定程度时，RNN将不能够学习如何连接使用这些信息。

![RNN Long-Term依赖](http://7xnh8y.com1.z0.glb.clouddn.com/RNN-longtermdependencies.png)

从理论上说，RNN绝对有能力处理类似的“长期依赖（Long-Term Dependencies）”问题。人们可以小心仔细地挑选参数来解决这类问题的最初级形式。但在实践中，RNN不太可能有能力学习到这些知识。[Hochreiter (1991)](http://7xnh8y.com1.z0.glb.clouddn.com/http://people.idsia.ch/~juergen/SeppHochreiter1991ThesisAdvisorSchmidhuber.pdf)和[Bengio, *et al*. (1994)](http://7xnh8y.com1.z0.glb.clouddn.com/http://www-dsi.ing.unifi.it/~paolo/ps/tnn-94-gradient.pdf)等人对这个问题进行了深入的研究，他们发现一些导致RNN很难学习到长期依赖的相当基本的原因。

谢天谢地，LSTM并没有这个问题！

## LSTM网络(LSTM Networks)

长短期记忆网络（Long Short Term Memory Networks），通常被简称为LSTM，是一种特殊的循环神经网络，可以学习长期依赖。LSTM 由[Hochreiter & Schmidhuber (1997)](http://7xnh8y.com1.z0.glb.clouddn.com/http://deeplearning.cs.cmu.edu/pdfs/Hochreiter97_lstm.pdf)提出，并在近期被[Alex Graves](http://7xnh8y.com1.z0.glb.clouddn.com/https://scholar.google.com/citations?user=DaFHynwAAAAJ&hl=en)进行了改良和推广。在很多问题，LSTM 都取得相当巨大的成功，并得到了广泛的使用。

LSTM是被刻意设计成可以避免长期依赖问题的。记忆长期的信息是LSTM的默认行为，而非需要努力学习才能获得的能力。

 所有的RNN都有一个重复网络模块的链状结构的形式。在标准RNN中，这个重复的模块只有一个非常简单的结构，比如仅一个tanh layer：

![标准RNN中包含单个层结构的重复模块](http://7xnh8y.com1.z0.glb.clouddn.com/LSTM3-SimpleRNN.png)

LSTM也有类似的链结构，只是重复模块有不同的内部构造。不是只有单个神经网络层，而是四个，它们以一种非常特殊的方式进行交互。

![LSTM中包含四个交互层的重复模块](http://7xnh8y.com1.z0.glb.clouddn.com/LSTM3-chain.png)

不必担心之后会提到的各种细节，接下来我们将会一步一步地解释剖析LSTM的结构原理。现在我们先来熟悉一下图中使用标记的含义：

![LSTM图标记含义](http://7xnh8y.com1.z0.glb.clouddn.com/LSTM2-notation.png)

在上图中，每一条黑线携带了一个向量，从一个结点的输出到其他结点的输入。粉色的圆圈代表元素级的操作，如向量加和，而黄色的矩阵表示网络层。合并的黑线表示向量的拼接，分开的黑线表示向量的复制，并被送入不同的位置。

## LSTM的核心思想(The Core Idea Behind LSTMs)

LSTM的核心就是cell的状态，如下图，水平线在图上方穿行而过。

cell的状态类似于一条传送带，从左至右在整条模块链上运行，只有一些少量的线性交互。这很容易使得信息在其上流传并保持不变。

![Cell line](http://7xnh8y.com1.z0.glb.clouddn.com/LSTM3-C-line.png)

通过一种被称为门（gates）的结构，LSTM有能力向cell的状态中移除或添加信息。

门是一种让信息选择式通过的方法，包含一个sigmoid层和元素级乘法操作。

![gates](http://7xnh8y.com1.z0.glb.clouddn.com/LSTM3-gate.png)

sigmoid层输出0到1之间的数值，描述了每个部分可以通过的量的多少。0表示不允许通过，1表示允许全部通过。

**LSTM拥有三个门，用来保护和控制cell的状态。**

## 逐步理解LSTM(Step-by-Step LSTM Walk Through)

LSTM中的第一步就是我们将会决定把什么信息从cell的状态中丢弃。这个决定是由一个被称为**遗忘门（forget gate layer）**的sigmoid层做出的。遗忘门会读取$h_{t-1}$和$x_t$，并输出一个0到1之间的数值给cell状态$C_{t-1}$中的每一个数字。1表示“完全保留”，0表示“完全舍弃”。

让我们回到语言模型的例子中，基于前面的词来预测下一个词。在这个问题中，cell的状态可能包含了当前**主语**的性别，因此正确的**代词**可以被预测出来。当我们看到一个新的主语，我们想要忘记**旧的主语**。

![遗忘门](http://7xnh8y.com1.z0.glb.clouddn.com/LSTM3-focus-f.png)

经过遗忘门后，下一步就是确定我们要保留什么信息在cell状态中。这一步包含两个部分：第一，一个称作“**输入门（input gate layer）**”的sigmoid层决定了什么值将会被更新；第二，一个tanh层产生一个新的候选向量$\widetilde{C}_t$，这个向量将会被加到（be added to）cell状态中。之后，我们将会结合这两个向量来更新cell状态。

![输入门](http://7xnh8y.com1.z0.glb.clouddn.com/LSTM3-focus-i.png)

现在是时候对旧的cell状态进行更新了，即$C_{t-1}$到$C_t$的过程。之前两步已经决定了要做什么，接下来我们就去完成它。

我们将旧状态$C_{t-1}$与遗忘门$f_t$相乘，忘记我们确定要丢掉的信息。然后，加上$i_t * \widetilde{C}_t$，即新的候选值，根据之前的输入门决定了我们如何更新每个值的程度。

在语言模型的例子中，这就是我们如何根据往时信息决定丢弃旧主语性别并添加新信息的地方。

![更新cell状态](http://7xnh8y.com1.z0.glb.clouddn.com/LSTM3-focus-C.png)

最终，我们需要决定输出什么。这个输出将会基于我们的cell状态，但确实一个过滤后的值。首先，我们使用一个sigmoid层来决定cell状态的什么部分可以输出。然后，我们使用一个tanh层（得到-1到1之间的值）处理cell状态，并将其与之前sigmoid门的输出相乘，这样我们可以只输出我们确定的部分。

回到语言模型的例子，因为刚刚看到了一个**主语**，它接下来可能想要输出的信息是与**动词**有关的。例如，它可能想要输出主语是单数还是负数，这样如果是动词的话，我们也知道动词需要进行的词形变化。

![输出门](http://7xnh8y.com1.z0.glb.clouddn.com/LSTM3-focus-o.png)

## LSTM的变体(Variants on Long Short Term Memory)

在此之前，我们已经讨论了LSTM的标准形式，但并不是所有的LSTM都和上述的结构一样。事实上，似乎所有使用了LSTM的论文中所使用的LSTM都有结构上的细小区别。尽管这些区别很小，但还是值得我们注意。

[Gers & Schmidhuber (2000)](http://7xnh8y.com1.z0.glb.clouddn.com/ftp://ftp.idsia.ch/pub/juergen/TimeCount-IJCNN2000.pdf)提出的一种流行的LSTM变种中，引入了一种称为“窥视孔链接”的结构。即令控制门层也受cell状态的影响。

![LSTM3-var-peepholes](http://7xnh8y.com1.z0.glb.clouddn.com/LSTM3-var-peepholes.png)

上图中，在所有门中都加入了窥视孔，但是许多论文中仅会加入部分窥视孔而不是全部。

另一种变体，使用了耦合的遗忘门和输入门。不再是单独地决定遗忘什么或应该添加什么新的信息，而是同时作出决定。只有当我们要输入某些信息时才会忘记掉一些信息，也只有当我们忘记某些旧的信息时才会输入新的信息。

![coupled gates](http://7xnh8y.com1.z0.glb.clouddn.com/LSTM3-var-tied.png)

还有一种改变较大的变种，门控循环单元（Gated Recurrent Unit，简称GRU），由[Cho, *et al*. (2014)](http://7xnh8y.com1.z0.glb.clouddn.com/http://arxiv.org/pdf/1406.1078v3.pdf)提出。GRU将遗忘门和输入门合二为一，引入了称为“更新门”（Update Gate）的结构。GRU也将cell状态和隐含层状态合并了，同时做了一些其他改变。这使得GRU比标准的LSTM模型更加简单，并且越来越受到欢迎。

![LSTM3-var-GRU](http://7xnh8y.com1.z0.glb.clouddn.com/LSTM3-var-GRU.png)

这里只介绍了几种流行的LSTM变体。还有许多其他变体，例如[Yao, *et al*. (2015)](http://7xnh8y.com1.z0.glb.clouddn.com/http://arxiv.org/pdf/1508.03790v2.pdf)提出的Depth Gated RNN。还有用一些采用完全不同方法来解决长期依赖的问题的例子，如[Koutnik, *et al*. (2014)](http://7xnh8y.com1.z0.glb.clouddn.com/http://arxiv.org/pdf/1402.3511v1.pdf) 提出的 Clockwork RNN。

要问哪个变体是最好的？其中的差异性真的重要吗？[Greff, *et al*. (2015)](http://7xnh8y.com1.z0.glb.clouddn.com/http://arxiv.org/pdf/1503.04069.pdf) 给出了流行变体的比较，结论是他们基本上是一样的。[Jozefowicz, *et al*. (2015)](http://7xnh8y.com1.z0.glb.clouddn.com/http://jmlr.org/proceedings/papers/v37/jozefowicz15.pdf) 则在超过 1 万种 RNN 架构上进行了测试，发现一些架构在某些任务上也取得了比 LSTM 更好的结果。

## 结论(Conclusion)

刚开始，我提到了人们通过 RNN 取得的重要成果。本质上这些都可以使用LSTM及其变种完成。对于大多数任务而言，LSTM确实有更好的性能。

写下了这么些公式，LSTM看起来令人望而怯步。但希望经过我一步一步的讲解之后，它可以变得易于理解一些。

LSTM 是我们在 RNN 中获得的重要成功。很自然地，我们也会考虑：接下来哪里会有更加重大的突破呢？在研究人员间普遍的观点是：“对！下一步已经有了——那就是注意力机制！” 这个想法是让 RNN 的每一步都从更加大的信息集中挑选信息。例如，如果你使用 RNN 来产生一个图片的描述，可能会选择图片的一个部分，根据这部分信息来产生输出的词。实际上，[Xu, **et al*.* (2015)](http://7xnh8y.com1.z0.glb.clouddn.com/http://arxiv.org/pdf/1502.03044v2.pdf)已经这么做了——如果你希望深入探索注意力可能这就是一个有趣的起点！还有一些使用注意力的相当振奋人心的研究成果，看起来有更多的东西亟待探索……

注意力也不是 RNN 研究领域中唯一的发展方向。例如，[Kalchbrenner, *et al.* (2015)](http://7xnh8y.com1.z0.glb.clouddn.com/http://arxiv.org/pdf/1507.01526v1.pdf)提出的 Grid LSTM 看起来也是很有前途。使用生成模型的 RNN，诸如[Gregor, *et al*.(2015)](http://7xnh8y.com1.z0.glb.clouddn.com/http://arxiv.org/pdf/1502.04623.pdf)， [Chung, *et al.* (2015)](http://7xnh8y.com1.z0.glb.clouddn.com/http://arxiv.org/pdf/1506.02216v3.pdf)和[Bayer & Osendorfer (2015)](http://7xnh8y.com1.z0.glb.clouddn.com/http://arxiv.org/pdf/1411.7610v3.pdf)提出的模型同样很有趣。在过去几年中，RNN的研究已经相当火热，而研究成果当然也会更加丰富！