---
date: "2018-01-22T11:20:32+08:00"
draft: false
title: "/bin/sh vs /bin/bash"
tags: ['linux', 'shell', 'bash', "alpine"]
series: []
categories: ["杂技浅尝"]
toc: true
---

## 问题背景

这里是一些关于为什么会发现这个问题的碎碎念……

之前也提到我们组的环境有些乱，一些开发机布置在生产，一台有GPU的计算机也布置在生产，如果需要root权限需要找运营，而据我上次和同事沟通结果是木有运营（我觉得应该是他们不知道找谁）。由于没有root权限，不能使用docker，而公司的机子连开发环境都是无法执行pip等操作的，所以只能使用conda创建虚拟环境。

然而，conda创建虚拟环境也需要注意版本问题，比如我手上是没有linux机子的，试验过mac就算conda创建成功也无法搬到服务器上。服务器的linux有以下3个版本，oracle linux6.7, mint 18, centos 6.7，保险起见还是为每个环境都建一个对应的conda环境。

而这个问题就出在mint 18上……

## 问题内容

我之前建立的docker文件中默认情况是启动 `/bin/sh`，基本都没有问题，这次也是这样操作。然而在mint 18中，如果在 `/bin/sh` 中无法使用 `source` 命令，提示 `/bin/sh: 25: source: not found`。

## 问题原因

一开始我还以为是没有安装source命令，但经过查找发现，只要把 `/bin/sh` 改为 `/bin/bash` 即可。

原因是，尽管很多linux的 `/bin/sh` 是建立一个指向 `/bin/bash` 的软连接（还是会有一些细节不同），但是仍有一些操作系统比如Debian系是用的 `/bin/dash` ，这两者的区别还是挺大的，但具体是什么我并没有仔细研究。总之以后shell编程如果确认是bash脚本的话还是直接用 `bash` 比较安全，用 `sh` 容易发生混淆。

## 后续

~~所以，赶紧把dockerfile里的都改为 `/bin/bash` 了……~~(2018-03-20 Update)

发现不是所有系统都有bash，因此盲目将所有sh都改成`/bin/bash`是不行的，最好使用前先了解所用的操作系统。

比如说alpine中用的不是dash也不是bash，而是ash，如果要用bash，需要先下载bash相关包再重建sh->bash的软连接。可以参考我关于[alpine中使用bash的github](https://github.com/orianna-zzo/dockerfile-repo/tree/master/alpine-bash-docker)



## 版本控制

| Version | Action           | Time       |
| ------- | ---------------- | ---------- |
| 1.0     | Init             | 2018-01-22 |
| 1.1     | Bash is not everywhere | 2018-03-20|
