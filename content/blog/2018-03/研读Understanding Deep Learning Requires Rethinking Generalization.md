---
date: "2018-03-14T10:40:55+08:00"
draft: false
title: "每周Paper精读(1) Understanding Deep Learning Requires Rethinking Generalization"
tags: ["deep learning", "generalization"]
series: ["每周Paper精读"]
categories: ["论文精读"]
toc: true
---

## 前言

上周五小仓鼠说有篇论文很有意思，问我看过没，是ICLR 2017 的 Best Paper [Zhang et al. 2017] Understanding Deep Learning Requires Rethinking Generalization。汗颜的确很久没有关心会议论文，现在更多都是关心项目技术点方向的论文。这篇论文一看的确很有意思，而且在学术界引起了非常激烈的讨论。有些人认为论文深度不够，提出的观点在泛化的理论学界已经研究很久，更偏向于实验报告不足以Best Paper，有人说中间有些逻辑问题，也有不少人觉得是对传统理论发起进攻的flag。这引起了我的兴趣，于是替换了我最近打算实现的NER的论文作为精读系列第一篇。

[Zhang C, Bengio S, Hardt M, et al. Deep Learning Requires Rethinking Generalization[C]// International Conference on Learning Representation. 2017:1-14.](https://arxiv.org/abs/1611.03530)

## 论文概要

作者通过一系列实验，展现出神经网络强大的拟合能力及现有的正则化方法的局限性，并提出传统机器学习中的泛化理论并不适用于深度学习，并说明即使在线性模型中，泛化理论实际上也没这么简单。

### Contributions

1. 展现神经网络的强大拟合能力

	通过不同程度随机或根据一定规则修改数据集标签（部分错误的标签数据集、随机标签的数据集）及修改数据集图片（图片的像素点打乱的数据集(shuffled、random，前者根据打乱一部分像素，后者完全打乱)、根据高斯分布生成的新数据集）两种方式来进行实验，使用随机梯度下降SGD并使用相同的超参，(Figure 1)**发现Inception模型在CIFAR10的各种数据集上都能100%拟合，只是不同噪声的数据集收敛速度有所不同（但也相差并没有太多），而一旦开始收敛，都能很快拟合。随机标签被认为是对数据的一次transformation，也就是说学习算法的uniform stability和训练数据的标签是独立的**。

	作者还提出不同于之前在"population level"对于特定function family在整个领域的表达能力，认为需要关注给定样本大小\\(n\\)情况下，也就是有限样本的网络的表达能力，并在文中证明**2层使用ReLU作为激活函数并有\\(2n+d\\)参数的神经网络就已经有足够的capacity去表示\\(d\\)维的样本大小为\\(n\\)数据集**。

2. 现有显式正则化方法的局限性

	通过在真实数据集和随机标签数据集(CIFAR10、ImageNet2012)上，分别对不同的显式正则化方法(explicit regularization)进行试验，发现(Table 1&2)：

	* random crop的数据增强方法在CIFAR10中使用不同网结构络(Inception、Alexnet)的试验中基本都能提高3%~4%的测试准确率，在ImageNet中能提高10%甚至以上。
	* Weight decay(\\(l2\\)正则化)在CIFAR10中能提高1%左右测试准确率，但有时会降低0.1% ~ 0.3%（Inception使用数据增强、MLP1层），在ImageNet中提高了6% ~ 8%（没有CIFAR10降低的对比情况）。
	* dropout并没有控制变量的对比试验。
	* 即使加入了显式正则化方法(explicit regularization)，随机标签的数据集一样能有很高的拟合程度，CIFAR10都能达到99%以上，ImageNet中最低的也有87%，其他也都在90%以上。

	基于以上发现，作者认为在CIFAR10上explicit regularization只提高了4% ~ 5%，在ImageNet上也只提高了18%，不算数据增强部分，在CIFAR10上提高1%，在ImageNet也有9% ~12%的提高。**相比于改进网络结构，explicit regularization提高并不明显、不是必须步骤，而有些网络模型本身就具有了一定的泛化能力**。此外，即使加入了正则化方法，训练集上的error还是能保持在很低的水平，作者认为**显式正则化可能提高了模型的泛化能力，但对控制泛化误差不是必要的也并不足够**。

3. 隐式正则化

	**Early stopping、Batch normalization**尽管不是为了解决泛化问题，但都对模型的泛化有一定帮助(Figure 2)，**被作者认为是implicit regularizer**。通过对于线性模型中的随机梯度下降算法的研究分析及在MNIST和CIFAR10上的实验，**作者提出SGD本身也是一种implicit regularizer。**

	**但不管是显式正则化还是隐式正则化在合适地调参后能帮助提高泛化能力，但都移去后模型依旧有不错的泛化能力，所以并不是泛化的基本原因**。

### 争议点

1. 泛化理论研究角度来说并无亮点贡献

	主要引起研究泛化理论学者不满的主要是作者提出的对于传统机器学习算法中的泛化理论的研究实际上在泛化理论界已经有广泛的研究了（尽管作者在论文中有提到这点），而作者对于这个并没有提出更为进一步研究和思考。这也是很多人反对这篇论文作为Best Paper，甚至觉得只是一篇实验报告的原因。

2. 对于深度学习模型来说，显式正则化并非效果不如模型结构改进这一观点有些武断。

	就如作者的实验来说，在ImageNet中所有显式正则化效果提升了18%，在CIFAR10上也有5%，这两者上的提高程度还是很巨大的，并不能得出正则化效果微弱，更无证据支撑模型结构的改进更为重要这一个结论。


### 疑惑之处

> 到底怎样的机制可以被称为正则化？

[Wiki](https://en.wikipedia.org/wiki/Regularization_%28mathematics%29)中对于正则化介绍说是应用在ill-posed不适定问题最优化的目标函数中的技巧，是提高模型泛化能力的技巧。

据我之前的理解，正则化是为了防止过拟合，提高模型的泛化能力。就形式化而言，指目标函数中单独的后缀项，用来惩罚系数太大的问题，或者说制定系数的规则。一般泛化能力都是通过计算训练集和测试集的错误率差来进行衡量。也就是说，我理解中符合形式化表述的是正则化项，目标是提高泛化能力，但正则化并不是提高模型泛化能力的唯一方法。所以作者把所有提高泛化能力的手段都称为正则化，是与我理解的体系架构有很大不同的地方。

发现ICML2010就已经有一篇论文[Implicit Regularization in Variational Bayesian Matrix Factorization](http://www.ms.k.u-tokyo.ac.jp/2010/ICML2010a.pdf)提到implicit regularization，但文中并没有直接定义implicit regularization，更多提到有regularizaiton effect但没有显式使用正则化。作者的思路也沿袭这个思路。直觉上这样说，对于方法A，如果使用A会比不使用A会使得模型泛化能力提高，那么A就是正则化，如果A是为了正则化设计的，就是显式正则化，反之，就是隐式正则化。如果从“只要提高泛化能力的手段都是正则化方法”这个角度思考，迫使模型泛化能力提高，也相当于隐形地给参数加上了镣铐不往更复杂的方向走，也的确有理由称之为regularization。

所以作者将数据增强也认为是显式正则化的手段之一。而在我理解的系统框架中，数据增强是与正则化并列的防止过拟合的方法。训练数据越多，模型效果越好是我们已有的共识。数据增强其中一个非常重要的作用是为了增加噪声来扩充数据集，与此同时使模型更关注鲁棒的通用特征（比如说尺度不变等），与之前提到的regularization的描述也相符。一句题外话，作者使用数据增强说明能够使模型泛化能力提高的对比实验中，使用数据增强的数据集比不使用数据增强的数据集要多许多，所以具体是数据增强使泛化能力提高还是更多的数据的效果很难判断。当然现实当中，我们总是会使用最多的数据来进行建模。

而其他手段，比如说作者发现early stopping等也能提高泛化能力，于是被分为implicit regularizer。[Wiki](https://en.wikipedia.org/wiki/Regularization_%28mathematics%29)中也将early stopping收入regularization中，理由是在训练过程中参数总是趋向于随着迭代次数增加而变得越来越发复杂，在validation set中没有提高时提早结束训练能够控制算法的复杂性。同样的还有batch normalization和stochastic gradient descent (SGD)，由于提高了模型泛化能力而被认为是隐式正则化，作者也尝试在线性模型上证明SGD的原因。

## 进一步阅读与了解

[线性模型的SGD中implicit regularization符合最小\\(l2\\)正则证明](https://stats.stackexchange.com/questions/316240/implicit-regularization-in-sgd-on-linear-model)

[Generalization Theory and Deep Nets, An introduction](http://www.offconvex.org/2017/12/08/generalization1/) 几个教授的blog，都和最优化有关，有时间都要学习下。这篇主要进行了泛化理论的入门介绍。

[ A breif history of Regularization](https://changkun.us/archives/2018/02/245/#more) 给出了非常清晰的正则化历史脉络

## 版本控制

| Version | Action | Time       |
| ------- | ------ | ---------- |
| 1.0     | Init   | 2018-03-14 |