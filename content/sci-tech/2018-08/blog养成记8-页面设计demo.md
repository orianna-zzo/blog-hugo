---
date: "2018-08-11T17:27:55+08:00"
draft: false
title: "Blog养成记(8) 页面设计demo"
tags: ["设计", "blog"]
series: ["Blog养成记"]
categories: ["杂技浅尝"]
toc: true
---

实际上前几个月就已经将页面原型设计完成，中间经过需求梳理和页面demo两个部分，不过设计完后就停滞了不少时间。最近才又开始折腾静态页面，在公司也尝试搭一个简单网站（尝试了flask，最后还是回归hugo生成静态页面），所以为了能将之后的部分继续，所以赶紧补上。

## 需求梳理

首先需要将应用场景进行梳理，细化需求，还特地做了对应的思维导图明确所需要的功能。

需求场景方面主要是以下几个点：

* 生活类：能满足基本的博客需求，写一些生活感悟、观演感想、事件评论、心情日记之类的内容，能将之前的一些不错的游记（大多半途而废😂）、照片也能安置就更好了，这一部分越现代越好，更需要设计感一些；
* 技术类：能满足技术文档、学习记录的需求，记录一些小技巧和学习过程，主要是分为科研类、学习类和尝试类，这一部分越简约越好，能帮助聚焦，也会显得更专业；
* 专题类：主要是一些内容并不是孤立的，是围绕一个核心的；
* 个人简介：当然还是需要有个人介绍的，不过专业性cv类还是偏设计感可能需要看最后的需求。

## 页面设计

针对以上的需求场景，将分成了以下几类：

- 首页 
- Sci & Tech 赛先生
  - Papers 论文精读：论文精读
  - Learning 学海无涯：网课、知识点梳理、读书笔记
  - Tech 杂技浅尝：eg.前端、docker之类不会太深入研究的部分，主要针对快速掌握并应用
- Project 课题组：项目情况
- Blog 随想录
  - Thoughts 对酒当歌：人生感悟
  - Critique 品鉴评论
  - Travel 御风而行：游记
  - Moment 时光碎片：短内容的心情日记
  - Gallery 光影长廊：照片集
- ArXiv 荟萃集
- Profile 档案处：个人介绍

网上找了不少静态页面的模板参考，还有各个博客的设计，参考他们的设计和功能，列了不少设想，所有的内容汇集在下面的思维导图中。

![blog-xmind](/images/series/Blog养成记/8/blog-xmind.jpg)

## Demo出炉

向认识的产品的同事咨询了下，开始尝试[墨刀](https://free.modao.cc/)，可以在上面做原型设计和简单交互。这个在线工具很方便，我也只用了最简单的设计部分，几乎是傻瓜式的。

Duang~设计稿暂时定版如下，后续很有可能会修改，毕竟现在十分的粗糙。

### 主页

![home-page](/images/series/Blog养成记/8/1.jpg)

### 赛先生

list:

![sci-index](/images/series/Blog养成记/8/1-1_sci.jpg)

single:

![sci-single](/images/series/Blog养成记/8/1-1-1_papers.jpg)

### 课题组

list:

![project-index](/images/series/Blog养成记/8/1-2_project.jpg)

single:

![sci-index](/images/series/Blog养成记/8/1-2-1_projectdetail.jpg)

### 随想录

list:

![blog-index](/images/series/Blog养成记/8/1-3_blog.jpg)

single - default:

![blog-single](/images/series/Blog养成记/8/1-3-3_travel.jpg)

single - moment:

![blog-single2](/images/series/Blog养成记/8/1-3-4_moment.jpg)

single - gallery:

![blog-single3](/images/series/Blog养成记/8/1-3-5_gallery.jpg)

### 荟萃集

list:

![arxiv-index](/images/series/Blog养成记/8/1-4_arxiv.jpg)

其他:

![cat1](/images/series/Blog养成记/8/1-4-1_catpapers.jpg)

![cat2](/images/series/Blog养成记/8/1-4-2_catmoment.jpg)

![cat3](/images/series/Blog养成记/8/1-4-3_catgallery.jpg)

![cat4](/images/series/Blog养成记/8/1-4-4_seriesblog.jpg)



## 版本控制

| Version | Action | Time       |
| ------- | ------ | ---------- |
| 1.0     | Init   | 2018-08-11 |