---
date: "2018-03-20T23:47:17+08:00"
draft: false
title: "慢学Docker(3) Docker Hub速度蜗牛爬怎么办?"
tags: ["docker", "docker hub", "镜像设置"]
series: ["慢学Docker"]
categories: ["杂技浅尝"]
toc: true
---

## 前言

其实两三个月前使用docker hub不管push还是pull虽然算不上光速但也不错。但今天修铭哥问我关于docker的使用，直接用Dockerfile建镜像不知道为什么成功不了，于是想直接pull我已经上传到docker hub中的镜像，结果就这1.3G左右的东西，下了整整一天。其中某layer就52M也能下半天。

晚上回家想下vue的docker镜像，几个四五十M的layer也慢悠悠下着，终于确定不仅仅是公司网不好，重点是docker hub的速度是变得更慢了。于是网上开始找解决方案。

## 解决方案

网上主要有以下几个国内镜像：

* Docker官方国内镜像
* 阿里云镜像
* 网易蜂巢
* Daocloud

注意：docker版本低于1.10需要通过配置启动参数来配置镜像源，可参考[这篇](https://www.zhihu.com/question/55135855)。

我用的是mac，只需要在docker的Preference中配置即可，可配置多个Registry mirrors：

{{% img-no-border %}}{{% center %}}<img name="docker-mirror" src="/images/series/慢学Docker/3/Docker-mirror.jpg" width='500px'/>{{% /center %}}{{% /img-no-border %}}

最后点击⌈Apply& Restart⌋重启docker，或者在命令行执行重启。

```shell
$ service docker restart  
```

若是其他操作系统，需要根据各镜像给出的流程操作。

### Docker官方国内镜像

国内镜像地址是[https://registry.docker-cn.com](https://registry.docker-cn.com)。详情查看[官网](https://www.docker-cn.com/registry-mirror)。

不过据说最近国内官方镜像挂了，好在可以输入多个镜像地址。

### 阿里云镜像

登陆阿里云，访问[https://cr.console.aliyun.com/#/accelerator](https://cr.console.aliyun.com/#/accelerator)，根据操作系统进行操作，添加镜像地址。

{{% img-no-border %}}![Aliyun-mirror](/images/series/慢学Docker/3/Aliyun-mirror.jpg){{% /img-no-border %}}

### 网易蜂巢

在镜像列表中添加[http://hub-mirror.c.163.com](http://hub-mirror.c.163.com)。

### Daocloud

进入[注册入口注册](https://account.daocloud.io/signin)。进入用户服务界面后，点击右上方的⌈加速器⌋：

{{% img-no-border %}}![aocloud_open_ac](/images/series/慢学Docker/3/Daocloud-open-acc.jpg){{% /img-no-border %}}

就会打开如下网页，可以得到一个加速器地址。根据docker所在系统的操作系统跟着指令完成配置。

{{% img-no-border %}}![aocloud-get-mirro](/images/series/慢学Docker/3/Daocloud-get-mirror.jpg){{% /img-no-border %}}

Daocloud在声明中提出docker加速器服务永久免费，希望不会食言。

![aocloud-clai](/images/series/慢学Docker/3/Daocloud-claim.jpg)

## Resource资源链接汇总：

[docker国内官方镜像](https://www.docker-cn.com/registry-mirror)、[阿里云镜像](https://cr.console.aliyun.com/#/accelerator)、[知乎上对docker版本小于1.10镜像配置方法](https://www.zhihu.com/question/55135855)、[Daocloud](https://account.daocloud.io/signin)

## 版本控制

| Version | Action | Time       |
| ------- | ------ | ---------- |
| 1.0     | Init   | 2018-03-20 |