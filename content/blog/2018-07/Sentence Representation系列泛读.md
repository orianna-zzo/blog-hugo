---
date: "2018-07-30T17:41:23+08:00"
publishdate: "2018-07-31"
lastmod: "2018-08-01"
draft: false
title: "每周Paper精读(2) Sentence Representation系列泛读"
tags: ["nlp", "repre"]
series: ["每周Paper精读"]
categories: ["论文精读"]
toc: true
---

## 前言

原本计划是每周一篇论文精读，然而实际上由于项目上安排较紧，距离上一次论文精读已经过了几个月。现在项目算是告一段落，在项目中也算是找到一些问题所在，正在看感兴趣的论文（这期是赶不上了）。正好媛源的实习生肖风顺正在做关于句向量的调研，找了些相关论文，我也旁听了两次，顺便就把这两次旁听的结果记录一下，顺便也对其中内容也简要的过一遍大概思路，虽然这样算不上什么“论文精读”，但本来精读和泛读就要结合么:smile:。PS，写着写着其实和精读也差不多了:sweat_smile:。

媛源这里主要是想将法院文书中的诉请语句和判决语句能够做一个分类，比较偏向于句子分类问题，因此会比较关注于Sentence Repre的生成，这一系列论文基本都是与此相关。

## 论文列表与Contribution简述

[1]. [Kim, Yoon. “Convolutional Neural Networks for Sentence Classification.” *Proceedings of the 2014 Conference on Empirical Methods in Natural Language Processing (EMNLP)*, 2014, pp. 1746–1751.]({{< relref "#1-convolutional-neural-networks-for-sentence-classification" >}}) [下载](https://arxiv.org/abs/1408.5882)

据说是第一篇在NLP领域使用CNN的论文，算是将CNN从图像识别领域延伸到了NLP来，并取得了不错的效果。

[2]. [Zhang, Ye, and Byron C. Wallace. “A Sensitivity Analysis of (and Practitioners’ Guide to) Convolutional Neural Networks for Sentence Classification.” *International Joint Conference on Natural Language Processing(IJCNLP)*, vol. 1, 2017, pp. 253–263.]({{< relref "#2-a-sensitivity-analysis-of-and-practitioners-guide-to-convolutional-neural-networks-for-sentence-classification" >}})  [下载](https://arxiv.org/abs/1510.03820v1)

对在句子分类层面使用CNN进行了详细的实验，对初学者的CNN调参有一定指导意义，可以帮助理解各参数对CNN的影响，但实质上的帮助并不会很大，算是给些经验性的参考吧。

[3]. [Zhang, Xiang, et al. “Character-Level Convolutional Networks for Text Classification.” *Neural Information Processing Systems(NIPS)*, 2015, pp. 649–657.]({{< relref "#3-character-level-convolutional-networks-for-text-classification" >}}) [下载](https://arxiv.org/abs/1509.01626)

使用字符集Embedding来代替词语级的Embedding。

[4]. [Shen, Dinghan, et al. “Baseline Needs More Love: On Simple Word-Embedding-Based Models and Associated Pooling Mechanisms.” *Proceedings of the 56th Annual Meeting of the Association for      Computational Linguistics (ACL)*, 2018.]({{< relref "#4-baseline-needs-more-love-on-simple-word-embedding-based-models-and-associated-pooling-mechanisms" >}}) [下载](https://arxiv.org/abs/1805.09843)

2015直接就跳到了2018，其实中间有很多基于attention做的repre的建模，不管是对于句子中词语的attention，句子结构的attention，还是段落的，整体结构算是越来越复杂。但是这篇2018ACL的文章指出，根本不需要这么复杂的模型，只需要最简单的词向量就可以有很好的效果。不过，据实验，调参的好坏很大程度上决定了这个基线的结果。

[5]. [Wang, Guoyin, et al. “Joint Embedding of Words and Labels for Text Classification.” *Meeting of the Association for Computational Linguistics(ACL)*, 2018.]({{< relref "#5-joint-embedding-of-words-and-labels-for-text-classification" >}}) [下载](https://arxiv.org/abs/1805.04174)

[6]. [Li, Shen, et al. “Analogical Reasoning on Chinese Morphological and Semantic Relations.” *Meeting of the Association for Computational Linguistics*, 2018.]({{< relref "#6-analogical-reasoning-on-chinese-morphological-and-semantic-relations" >}}) [下载](https://arxiv.org/abs/1805.06504)

## 数据集

便于以后进行查询实验，将几篇论文的数据集列一下：

* **MR**: [Movie reviews](https://www.cs.cornell.edu/people/pabo/movie-review-data/) with one sentence per review. Classiﬁcation involves detecting positive/negative reviews (Pang and Lee, 2005). (ref: 论文[1])
* **SST-1**: [Stanford Sentiment Treebank](http://nlp.stanford.edu/sentiment/)—an extension of MR but with train/dev/test splits provided and ﬁne-grained labels (very positive, positive, neutral, negative, very negative), re-labeled by Socher et al. (2013). Data is actually provided at the phrase-level and hence we train the model on both phrases and sentences but only score on sentences at test time, as in Socher et al. (2013), Kalchbrenner et al. (2014), and Le and Mikolov (2014). Thus the training set is an order of magnitude larger than listed in table 1. (ref: 论文[1])
* **SST-2**: Same as SST-1 but with neutral reviews removed and binary labels. (ref: 论文[1])
* **Subj**: Subjectivity dataset where the task is to classify a sentence as being subjective or objective (Pang and Lee, 2004). (ref: 论文[1])
* **TREC**: [TREC question dataset](http://cogcomp.cs.illinois.edu/Data/QA/QC/)—task involves classifying a question into 6 question types (whether the question is about person, location, numeric information, etc.) (Li and Roth, 2002). (ref: 论文[1]) 
* **CR**: [Customer reviews](http://www.cs.uic.edu/ ∼ liub/FBS/sentiment-analysis.html) of various products (cameras, MP3s etc.). Task is to predict positive/negative reviews (Hu and Liu, 2004). (ref: 论文[1])
* **MPQA**: [Opinion polarity detection subtask](http://www.cs.pitt.edu/mpqa/) of the MPQA dataset (Wiebe et al., 2005). (ref: 论文[1])

| Data      | \\(c\\) | \\(l\\)  | \\(N\\)   | \\(\|V\|\\) | \\(\|V_{pre}\|\\) | \\(Test\\) |
| --------- | ---- | ---- | ----- | ----- | ----------- | ------ |
| **MR**  | 2    | 20   | 10662 | 18765 | 16448       | \\(CV\\)  |
| **SST-1** | 5    | 18   | 11855 | 17836 | 16262       | 2210   |
| **SST-2** | 2    | 19   | 9613  | 16185 | 14838       | 1821   |
| **Subj**  | 2    | 23   | 10000 | 21323 | 17913       | \\(CV\\)   |
| **TREC**  | 6    | 10   | 5952  | 9592  | 9125        | 500    |
| **CR**   | 2    | 19   | 3775  | 5340  | 5046        | \\(CV\\)   |
| **MPQA**  | 2    | 3    | 10606 | 6246  | 6083        | \\(CV\\)   |

Table 1: Summary statistics for the datasets after tokenization. \\(c\\): Number of target classes. \\(l\\): Average sentence length. \\(N\\): Dataset size. \\(\|V\|\\): Vocabulary size. \\(\|V_{pre}\|\\): Number of words present in the set of pre-trained word vectors. \\(Test\\): Test set size (CV means there was no standard train/test split and thus 10-fold CV was used). (ref: 论文[1])

## 论文详情

### [1]. Convolutional Neural Networks for Sentence Classification 

据说是第一篇在NLP领域使用CNN的论文，但看了之后的实验观察，其实2014ACL就已经有一篇在NLP的句子建模领域使用CNN的论文了，但在那篇中在SST-1数据集上使用随机初始化词向量的模型效果只有37.4%，大大比这篇的45.0%低，作者Yoon Kim归功于采用了多个不同窗口宽度的filter。所以，这篇如果有较高地位，应该在“第一篇在NLP领域使用CNN的论文”前再加一句，效果出色。

#### 架构细节

其实乍一看，Yoon Kim就只是直接采用了CNN的框架套用在了NLP领域，与图像领域并没有什么很大区别。但一些细节上还是会有一些略微的不同。图1为原论文中的配图，图2为网上找到的对于一句话实例的介绍图片，相对更为清楚和详细。

![图1 原论文配图](https://github.com/llhthinker/NLP-Papers/raw/master/text%20classification/2017-10/Convolutional%20Neural%20Networks%20for%20Sentence%20Classification/model.png)

![图2 一句话实例](https://github.com/llhthinker/NLP-Papers/raw/master/text%20classification/2017-10/A%20Sensitivity%20Analysis%20of%20(and%20Practitioners%E2%80%99%20Guide%20to)%20Convolutional/model.png)

基本上图就能够说明论文的主要步骤和思想了，有几点比较有趣的思考：

* 不同于图像领域的卷积核为\\(n \times n\\)的正方形，Yoon Kim在这里使用的是\\(n \times k\\)，其中\\(k\\)是词向量的维度，在此情况下的卷积结果的确比粗暴直接套CNN的正方形filter更为合理（除非对于词向量来说，也存在局部视野的意义）。在此情况下，feature map不再是类似地正方形，而是一个一维向量，max pooling原本为对feature map的2维度pooling改为单维度取最大值也是显而易见的。
* 图片天然有RGB的三通道，Yoon Kim尝试使用static和non-static的词向量策略作为两个不同的通道，并希望能通过static的部分减少过拟合（使feature map不会偏离太过），但不是在所有数据集上都有更好的效果。{{% bgstyle purple %}}多通道部分的确是一个很有趣的思路，可以考虑融合更多的信息进来，就像NIN一样。 {{% /bgstyle %}}
* 之前大多直接使用word2vec，由于word2vec只考虑了上下文的文字信息，更偏向于句法概率而不是语义，因此两个语义相反但用法相似的词语是很有可能在词向量的距离上是相近的。而在新的数据集（特别是情感分类数据集）上继续fine-tune，由于加入了语义部分不断进行调整，可以使两个用法相似但是语义更远的词分得更开，对于原本的词向量，能对现实中的词语有更好的建模。

除了以上几点之外，还有以下几个论文的内容比较有意思：

* 其实之前的思路一直是只将词向量作为初始化的方法来看待，目的是为了更快速地训练网络，提供更多的额外信息，并且能帮助降低一些迭代到不好的参数的可能。第一次看到说考虑static（词向量参数固定不再发生变化）和mixed（将词向量参数固定和词向量参数继续fine-tune作为两个通道）的情况。虽然最后的结论还是static的效果更为稳定地优秀，但还是耳目一新。
* 论文提到另一篇2014ACL同样尝试了CNN做句子建模，同样随机初始化词向量，但在SST-1上只获得了37.4%的结果，相比Yoon Kim的45.0%相差挺多，Yoon Kim把这个结果归因于使用了多个不同窗口大小的filter。
* 对于wordvec词典中未出现的词使用与word2vec一致variance的均匀分布来初始化。

### [2]. A Sensitivity Analysis of (and Practitioners’ Guide to) Convolutional Neural Networks for Sentence Classification



### [3]. Character-Level Convolutional Networks for Text Classification



### [4]. Baseline Needs More Love: On Simple Word-Embedding-Based Models and Associated Pooling Mechanisms



### [5]. Joint Embedding of Words and Labels for Text Classification



### [6]. Analogical Reasoning on Chinese Morphological and Semantic Relations



## 进一步阅读与了解



## 版本控制

| Version | Action | Time       |
| ------- | ------ | ---------- |
| 1.0     | Init + paper[1]  | 2018-07-30 |
| 1.1| Add shortcode for text bg | 2018-08-01|