---
date: "2018-01-27T15:04:18+08:00"
draft: false
title: "慢学Docker(2) Docker初探"
tags: ["docker"]
series: ["慢学Docker"]
categories: ["杂技浅尝"]
toc: true
---

## Introduction

### 什么是docker？

什么是Docker？Docker是一个开源的容器应用引擎。开发者可以将应用及依赖打包上传到一个可移植的容器中执行，由于容器是可移植的，于是，这个应用就可以在任何其他可以运行这个容器的地方运行了。换句话说，只要其他可以运行docker的地方就可以运行这个容器（因为是可移植的），也就可以运行你的应用，而docker在很多平台都可以安装运行，这样完全不用担心由于平台环境的不同对应用部署带来的困难，更不用提多次部署这种重复劳动的麻烦事了。此外，容器使用了sandbox机制，容器与容器之间有较好的隔离性，不用担心。这是docker最通俗也是最广泛的应用。

是不是觉得这个优点和应用很熟悉？当我们想要跨平台无视底层环境要求跑程序执行应用我们都会想到什么？对，就是虚拟机。在本机上装个虚拟机应用比如说virtual box或者vmware，然后再用操作系统镜像（比如说win7）建个虚拟机，就可以在虚拟机中安装各种应用了。在这里docker就相当于virtual box或者vmware，容器就相当于你建的虚拟机实例，docker中也有一个镜像的概念，就相当于你建虚拟机所使用的操作系统镜像（win7）。这样说是否更通俗易理解一些？

### docker容器 vs vm虚拟机 

既然虚拟机也可以完成相似的功能，为什么要用docker呢？换句话来问，同样都是虚拟化技术，docker的容器技术和vm虚拟机有什么区别？docker官方网站也简单回答了这个疑问，可以在[这里](https://docs.docker.com/get-started/#containers-vs-virtual-machines)查看。我对于这些底层的架构知识并不是很了解，只能根据网上的信息以及最近这一个月的入门级使用谈及一下感受。

相比vm虚拟机，docker的容器技术更为轻量级。直观上，这个轻量级体现在启动速度更快、占用资源更少。我并没有具体测试比较过，但在实际使用中感受是，我原本开一个虚拟机跑程序就已经很不错了，虚拟机开机还有个等待开机时间，而现在使用docker，容器载入速度很快，同时在机子上会运行几个不同的容器，从镜像重新生成容器也很快很方便。2014年开始，docker一直对外宣传的一个重点也是这个，“虚拟机需要数分钟启动，而Docker容器只需要50毫秒”。

不过docker容器和虚拟机尽管都是虚拟化技术，但是里面的技术细节有很多不同。下面两张docker官网上的图很清晰简单地表示了两者的区别：

{{% img-no-border %}}{{% center %}}<img name="architecture comparison" src="https://www.docker.com/sites/default/files/d8/2018-11/docker-containerized-and-vm-transparent-bg.png" />{{% /center %}}{{% /img-no-border %}}


前者是docker容器的架构图，后者是虚拟机的架构图。最显然的区别是，每个虚拟机中都运行着各自的guest OS，而在docker容器架构中每个容器只包含了各自的应用和依赖包，并不包含独立的操作系统。也就是说容器采用kernel共享，同时使用cgroups和namespace等方法对容器所使用的命名空间和依赖包等进行区分以达到隔离效果。所以尽管各个容器都是独立在宿主机的操作系统上运行着的，但不可避免地，各个容器之间会共享一些通用运行库。这个区别会使得各个容器之间相互独立，但并没有虚拟机的隔离性好，但这也是使得docker容器能够快速启动并且资源占用少的一个重要原因。太复杂的我也不清楚细节，只是大致有个概念，这对我们简单应用了解也应该就足够了。

嗯，再啰嗦一句。在查资料是看到了这篇知乎上的[博文](https://zhuanlan.zhihu.com/p/30594040?utm_source=weibo&utm_medium=social)，里面提到在会议SOSP 2017上发表了一篇很有意思的paper，他们通过精简内核和其他虚拟技术把虚拟机做的更轻量级，使之启动速度比docker更快，内存开销比docker更小。现在的热门是容器技术，说不定再过个四五年的又是虚拟机的天下了呢？嗯，说不准是两个结合的究极进化体。门外汉表示看看热闹就可以了。

### docker并不万能

Docker真心很好用，才上手了1个多月的我特别喜欢。但是作为新兴技术，还有很多不完善的地方。有些问题可能作为个人使用者来说并不是很大的问题，但是对于生产来说都是非常重要需要考虑的问题，这里只是简单记录下到目前为止遇到的一些局限性，至于其他的，遇到再加吧：

* docker容器主要针对Unix系的，也就是说如果应用是基于windows平台的，现在无法使用docker容器。不负责任地加一嘴，就是没有windows系的基镜像，就像不让你用windows虚拟机一样。
* docker本身基于Linux 64bit，也就是说如果你的机子是32位的，暂时无法使用，不过好在现在基本是64位机的天下了，这个问题基本很少遇到。
* windows机如果要使用docker，需要支持硬件虚拟化。现在windows版的docker实际上相当于跑了个linux内核的虚拟机。也就是windows不是所有64位机子都能装得上docker的，我新的实习生电脑就装不上，公司的windows笔记本也装不上。
* 截止目前为止，docker的安装还需要root权限，docker的使用至少需要有sudo权限的用户组。据说容器的使用已经可以不需要root权限了，但我还没有实际使用过。公司服务器一般不会给你root权限，这个需要找运营，折腾起来麻烦。
* 由于docker的隔离性并不够强，所以docker的安全方面还有待加强。这部分我还没有强烈感受到，反正不影响在自己电脑上折腾。

### GPU的选择: Nvidia Docker

Docker很好地解决了cpu程序环境依赖问题，但没有解决gpu程序的依赖问题。程序无法可移植地配置gpu环境。为docker配置gpu (nvidia) 环境必须在虚拟环境中安装与宿主环境相同版本的显卡驱动，并手动将显卡设备挂在指定位置。这为gpu程序移植造成困扰。Nvidia在docker基础上进行了封装，解决了gpu依赖问题。如果需要使用gpu，可以安装nvidia-docker。Nvidia-docker安装的前置条件包括docker已经安装、NVIDIA GPU、NVIDIA driver已经安装等，具体条件和安装过程需要查看nvidia-docker的[github](https://github.com/NVIDIA/nvidia-docker)。

这里只是提一嘴，关注点还是在docker本身。毕竟现在用的mac可用的不是n卡，等有机会整个n卡玩并需要安装docker的时候再试试nvidia-docker。

## Installation

Docker分为Community (CE) 和Enterprise (EE) 版本，个人使用用CE就够了，一些EE的高级功能用不上也不会用。Docker CE有分为stable版和edge版，前者每一个季度更新一个更reliable的更新，后者每个月都会提供一些新的特征。不爱折腾的我就直接选择stable版。更多关于docker安装的环境需求可以参考官网[该页面](https://docs.docker.com/install/#server)，不同的环境还是有不同的需求的。

网上有很多零散的安装教程，建议找最新的来看，很多实际上都过时了。最安全的当然直接上官网。

### Mac安装

Mac可以选择用Homebrew进行安装:

```shell
$ # stable channel
$ brew cask install docker
$ # edge channel
$ brew cask install docker-edge
```

也可以在[这里](https://docs.docker.com/docker-for-mac/install/#download-docker-for-mac)选择stable或者edge进行下载，安装对应的dmg包即可。

PS，其实还有docker toolbox，暂时还没搞清楚之间的关系，感觉可能是早期版本，官网[这里](https://docs.docker.com/docker-for-mac/docker-toolbox/)给出了解释，没有仔细看，不过我认为无脑安装docker for mac就够用了。

### Windows安装

在官网[这里](https://docs.docker.com/docker-for-windows/install/#download-docker-for-windows)选择版本进行下载安装。不过Windows最低版本要求时64位win10 pro。

### Linux安装

Docker CE支持Ubuntu、Debian、CentOS、Fedora这几个Linux环境安装。
以下主要作为介绍及资料汇总，来源为官网([Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)、[Debian](https://docs.docker.com/install/linux/docker-ce/debian/)、[CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)、[Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/))。

Linux的版本要求如下：

* Ubuntu
    * Artful 17.10 (仅Docker CE 17.11 Edge 或更高版本)
    * Xenial 16.04 (LTS)
    * Trusty 14.04 (LTS)    
* Debian
    * Buster 10 (Docker CE 17.11 Edge only)
    * Stretch 9 (stable) / Raspbian Stretch
    * Jessie 8 (LTS) / Raspbian Jessie
    * Wheezy 7.7 (LTS) (需要升级内核，版本至少为3.10)
* CentOS
    * CentOS7 maintained version  
* Fedora
    * 64位 version 26
    * 64位 version 27

安装前需要先卸载以前的旧版本的docker或者docker-engine。

Linux安装分为2种：

* 一种是在[这里](https://download.docker.com/linux/)选择对应的linux发行版本下载安装包。
  
    * Ubuntu  
      在[https://download.docker.com/linux/ubuntu/dists/](https://download.docker.com/linux/ubuntu/dists/)选择Ubuntu版本，然后进入pool/stable文件夹，选择计算机架构并下载.deb包。  
    * Debian  
      在[https://download.docker.com/linux/debian/dists/](https://download.docker.com/linux/debian/dists/)选择Debian版本，然后进入pool/stable文件夹，选择计算机架构并下载.deb包。  
    * CentOS  
      在[https://download.docker.com/linux/centos/7/x86_64/stable/Packages/](https://download.docker.com/linux/centos/7/x86_64/stable/Packages/)下载.rpm包。
    * Fedora  
      在[https://download.docker.com/linux/fedora/](https://download.docker.com/linux/fedora/)选择Fedora版本，然后进入x86_64/stable/Packages文件夹，选择计算机架构并下载.rpm包。
  
  然后再通过安装包进行安装，并通过下面命令启动docker。

  ```shell
  $ # Ubuntu & Debian
  $ sudo dpkg -i /path/to/package.deb    # start docker automatically
  $ # CentOS
  $ sudo yum install /path/to/package.rpm
  $ sudo systemctl start docker    # start docker
  $ # Fedora
  $ sudo dnf -y install /path/to/package.rpm
  $ sudo systemctl start docker    # start docker
  ```

* 另一种需要先配置包管理器（apt-get/yum/dnf）的repository，再通过对应包管理器下载。不同的发行版本，具体的操作也不同，详情这里就不列举了，到官网对应的安装说明页（[Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)、[Debian](https://docs.docker.com/install/linux/docker-ce/debian/)、[CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)、[Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)）去查看。

### 查看安装版本

安装完毕后可以检查Docker Engine, Docker Compose和Docker Machine版本

```shell
$ docker --version
$ docker-compose --version
$ docker-machine --version
```

这样docker就安装成功了！

## 一个简单的实例

安装完docker后，可以通过一个简单的实例了解docker的一些基础操作，这样就可以快速开始上手玩了。更为复杂的操作以及基本概念会在后续内容中介绍。

首先，我们需要获得一个镜像，例如alpine：

```shell
$ docker pull alpine
```

这样我们就从docker的仓库中获得了latest版本的alpine镜像（默认latest版本），通过下面命令查看电脑中所有的docker镜像及镜像信息：

```shell
$ docker images
```

alpine镜像已经下载成功，接下去我们就运行一个alpine的容器：

```shell
$ docker run --name my-docker -it alpine
```

`—name` 给这个容器赋一个名字， `-it` 表示使用一个可交互的终端。

这时就已经在一个alpine容器中了。可以进行你想做的操作，比如说 `ls`，`cd`等。操作完成后可以键入 `exit` 离开容器。

我们看一下现在运行的容器：

```shell
$ docker ps
```

嗯，看不到my-docker了，这个容器已经关掉了。

那现在一共有哪些容器呢？

```shell
$ docker ps -a
```

这会显示所有的容器，包括并未在运行的容器。

我们可以继续打开这个容器继续上次未完的操作：

```shell
$ docker start -i my-docker
```

`-i` 表示使用可交互式的方式。

这样可以继续上次的操作，并以 `exit`退出。

这个容器后面不再会使用的话，可以删除：

```shell
$ docker rm my-docker
```

alpine这个镜像如果不使用的话也可以删除了：

```shell
$ docker rmi alpine
```

## Resource资源链接汇总：

[docker官方文档](https://docs.docker.com/)、[docker for mac下载页](https://docs.docker.com/docker-for-mac/install/#download-docker-for-mac)、[docker for windows下载页](https://docs.docker.com/docker-for-windows/install/#download-docker-for-windows)、[docer for linux下载页](https://download.docker.com/linux/)

[docker for Ubuntu安装说明](https://docs.docker.com/install/linux/docker-ce/ubuntu/)、[docker for Debian安装说明](https://docs.docker.com/install/linux/docker-ce/debian/)、[docker for CentOS安装说明](https://docs.docker.com/install/linux/docker-ce/centos/)、[docker for Fedora安装说明](https://docs.docker.com/install/linux/docker-ce/fedora/)

[nvidia-docker github](https://github.com/NVIDIA/nvidia-docker)

## 版本控制

| Version | Action | Time       |
| ------- | ------ | ---------- |
| 1.0     | Init   | 2018-01-27 |
| 1.1     | Update Linux Installation | 2018-03-21 |
| 1.2     | Update Images | 2019-03-12 |