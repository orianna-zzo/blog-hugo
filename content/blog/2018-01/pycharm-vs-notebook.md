---
date: "2018-01-30T21:07:19+08:00"
draft: false
title: "Pycharm vs Notebook"
tags: ["python", "notebook", "pycharm", "工具选择", "效率流程"]
series: []
categories: ["杂技浅尝"]
toc: true
---


严格说来题目起的有些问题，pycharm是一个IDE (Integrated Development Environment)，而且在pycharm里同样也可以使用Notebook。这里主要是指传统的编程方式和Notebook交互式方式的比较和选择。这篇博客希望对那些和我一样有选择恐惧症并且非常迷茫的人有所帮助。

## 缘起

说起来接触python也已经一年半的时间。去年7月，哦不，应该是前年7月，刚入职才开始因为工作学习的python，首先遇到的就是IDE的选择问题。比较流行的开发工具有JetBrain的Pycharm和Jupyter Notebook。

## 初识

组长和组里新学习代码的小伙伴们偏爱notebook，不过我却更青睐相对于更像一个传统IDE 的Pycharm。

Pycharm就像是我们经常使用的其他语言的IDE一样 (比如说visual studio, eclipse)，集成各种调试工具，可以非常方便地在里面进行单步调试、变量监控，文档结构也非常明晰，而且也可以在pycharm里使用notebook（尽管我不在这里使用）。

我对于notebook的印象是介于传统IDE和console中间，反馈速度很快，很灵活，可以将每次中间结果进行保留。但是由于太灵活了，经常看到同事给我的notebook顺序非常紊乱，根本无从下手，难以复现，这也是导致我对notebook印象并不好的原因。此外，不确定是使用的版本配置或是其他原因，小伙伴的notebook在数据量增大时很容易奔溃，但是pycharm跑普通的python程序却很正常。基于以上原因，我并不是很理解为什么notebook会这么火。**在我看来，直接一个pycharm乖乖编写python程序就很ok，想即时得到结果就在pycharm里开个console或者设个断点单步调试，在监控窗口也可以尝试各种代码是否可行，甚至可以监控运行时变量的状态和结构，非常方便、功能强大**。有人给我的理由是notebook即使可见结果，对于初学者更为友好，但我觉得对我来说并不会如此。

初识，notebook很轻而易举地完败。

## 再顾

再次考虑了解notebook是源于新来的实习生都喜欢使用notebook，不仅仅是转专业的实习生喜欢，cs的也喜欢，网上很多教程类也都是在notebook之上，因此让我很想了解notebook到底有什么魔力让这么多人喜欢并且选择。

我问了cs专业的实习生，他给我的理由是**可以很方便的记录各个小实验的方法过程和结果，notebook中可以添加markdown块，实验结果的数据和图片可以很方便进行显示，代码块和markdown块可以交叉存储，是一种更为灵活和方便的记录方式**。

我在查找资料时也发现了大家对于notebook的使用方法。Stackoverflow上也有人提问[如何更好地整理notebook中的代码](https://stackoverflow.com/questions/36427747/scientific-computing-ipython-notebook-how-to-organize-code)，非常有启发性，建议参考下。

感觉上，notebook，顾名思义是记录本，也就是说它最大的功能是记录你的想法与尝试、零碎的小实验，并且notebook实际上并不简单局限于python。而使用notebook也有一些比较好的实践，我这里列举一些我觉得十分不错的，以便我后期查看：

* Notebook的代码应该整洁，也就是说随时可以做到按顺序重跑全部代码并能复现结果，实验结束后notebook可能需要重构。
* 建立一个很多project能够通用的utils库。可以写一份setup.py，让其他组员可以方便安装。
* 将大的notebook分成多个小的notebook，每个notebook最好能够符合“假设-数据-结论”的模式。一个例子是分为data preparation、data validation、exploratory plotting、simple linear model、hierachical model、 playground等，将数据准备的记过都存成本地文件，便于其他notebook引入。
* 每个cell最好能符合“idea-execution-output”的模式。

## 思考

并没有完整意义上的完美的工具，每个工具都有其最优使用场景。在这个场景不尽如意，不等于这不是一个好的工具。Pycharm是各个意义上强大的工具，但并不等于notebook没有优秀的地方。尽管常规编程、调试、上线不是notebook的强项，但对于数据科学这个领域需要多种实验查看数据结构，notebook就十分适合记录实验结果，可以验证多种想法假设。想法初步验证后，再正式重构正规编程。


## Resource资源链接汇总

[stackoverflow: 如何更好地整理notebook中的代码](https://stackoverflow.com/questions/36427747/scientific-computing-ipython-notebook-how-to-organize-code)


## 版本控制

| Version | Action | Time       |
| ------- | ------ | ---------- |
| 1.0     | Init   | 2018-01-30 |