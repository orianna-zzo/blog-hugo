---
date: "2018-02-06T10:01:15+08:00"
draft: false
title: "Docker for datascience: 从纠结到放弃"
tags: ["docker", "环境配置", "dockerfile"]
series: []
categories: ["杂技浅尝"]
toc: true
---

表示我是不纠结不死星人。

先是由[jupyter stacks](https://github.com/jupyter/docker-stacks)突发奇想，于是纠结于想建立属于自己的docker族系，特别是用来做数据科学&机器学习。想要不用Anaconda、甚至不用conda，自己按照使用习惯安装我自己需要的包、想使用最小的alpine作为基镜像，因此开始走上不归路。

想要使用bash而不是ash，想要默认调用bash，想要调用bash时能默认出现现在使用用户及现在所在文件夹。折腾了2天终于成功，dockerfile在[orianna-zzo/dockerfile-repo/alpine-bash-docker](https://github.com/orianna-zzo/dockerfile-repo/tree/master/alpine-bash-docker)，算是让我尝到一次甜头。

成功第一次，于是开始尝试在python3.6上安装我想要的包。pull了python3.6-alpine3，废了老大功夫总算在多次碰壁下把依赖都安装成功可以成功pip install想要的包。不过在查验我装了每个python包是什么作用时发现我安装了nomkl，这个包说明使用numpy时使用非mkl版本。网上一查，mkl对于intel的cpu加速还是很厉害的，于是把nomkl删了开始重新继续折腾。折腾了一圈，通过安装openblas-dev，替代mkl，但发现openBlas性能不如mkl。

然后狠下心，在alpine上直接安装。为了numpy等包装g++、gfortran，为了matplotlib装libfreetype-dev和libpng。结果发现连h5py都装不上，在pandas中无法用hdf5格式读写文件。alpine上mkl也难以安装成功，pip install也说平台不对。无奈之下舍弃alpine。

使用ubuntu:16.04，为了h5py等包装build-essential，为了numpy等包装g++、gfortran，为了matplotlib装libfreetype6-dev和libpng12-dev，为了mkl装cpio。mkl终于安心装上，然而pip install numpy后使用show_config()查看时无法连接到mkl。spacy下了源码直接build结果总是报编码错误。心累。

于是纠结了一周的环境问题，在我最后决定选择ubuntu:16.04+anaconda3落下帷幕。

嗯，我正在尝试这个，希望能够成功。

## 后文

果然还是无脑anaconda比较省心。只是ubuntu:16.04+anaconda3 后再安装tensorflow直接就超过3g大关，比官方镜像直接多了2g，捂脸。决定不再纠结这个，反正已经配置成功，可以正常使用。毕竟不是重点，已经浪费了好多时间，还是好好钻研算法吧！

相关的dockerfile都可以在[orianna-zzo/dockerfile-repo/python-docker](https://github.com/orianna-zzo/dockerfile-repo/tree/master/python-docker)找到。

## 版本控制

| Version | Action   | Time       |
| ------- | -------- | ---------- |
| 1.0     | Init     | 2018-02-06 |
| 1.1     | 增加后文 | 2018-02-13 |