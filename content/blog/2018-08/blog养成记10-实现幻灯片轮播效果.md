---
date: "2018-08-12T01:41:43+08:00"
publishdate: "2018-08-12"
lastmod: "2018-08-12"
draft: false
title: "Blog养成记(10) 实现幻灯片(轮播)效果"
tags: ["前端", "css", "bootstrap", "blog", "carousel"]
series: ["Blog养成记"]
categories: ["杂技浅尝"]
toc: true
---

## 前言

设计中首页有个大图，希望它能实现幻灯片的轮播效果，同时如果能够满足自适应的需求就更好了。对于幻灯片的轮播效果，一年前我试过一个在Github上的js插件，这次使用Bootstrap4发现这已经集成进Bootstrap的框架了，如果不是非要在网页上实现一个真的幻灯片，没必要再去整一个幻灯片的插件。

## Carousel

### 基础版

在Bootstrap4中轮播组件称为Carousel，在[官网](https://getbootstrap.com/docs/4.1/components/carousel/)就有对其使用的简单介绍。

简单说来，该组件分为四块：indicator(显示第几页)、control(控制上一页和下一页)、caption(每一页上显示的字)和slide(显示的图片)。这几部分中，除了slide是必须要有的（不然用这个控件干嘛），其他都可以不用。基本的格式如下，基本包含了所有的基础用法，还有一些参数比如间隔时间等可以调整：

```html
<!-- Carousel -->
<div id="example" class="carousel slide carousel-fade" data-ride="carousel" data-interval="3000">
  <!-- Indicator -->
  <!-- data-target=外面div的id -->
  <ol class="carousel-indicators">
    <li data-target="#example" data-slide-to="0" class="active"></li>
    <li data-target="#example" data-slide-to="1"></li>
    <li data-target="#example" data-slide-to="2"></li>
  </ol>
  <!-- /.Indicator --> 
    
  <!-- Slides -->  
  <div class="carousel-inner">
    <!-- First Slide -->  
    <div class="carousel-item active">
      <img class="d-block w-100" src="path/to/img1.jpg" alt="First slide">
      <!-- First Caption -->  
      <div class="carousel-caption d-none d-md-block">
    	<h5>Caption 1</h5>
    	<p>Content of caption 1</p>
 	  </div>
      <!-- /.First Caption -->  
    </div>
    <!-- /.First Slide -->  
      
    <!-- Second Slide -->  
    <div class="carousel-item">
      <img class="d-block w-100" src="path/to/img2.jpg" alt="Second slide">
    </div>
    <!-- /.Second Slide -->  
      
    <!-- Third Slide -->  
    <div class="carousel-item">
      <img class="d-block w-100" src="path/to/img3.jpg" alt="Third slide">
    </div>
    <!-- /.Third Slide -->  
  </div>
  <!-- /.Slides --> 
    
  <!-- Controls -->
  <!-- href=外面div的id -->
  <a class="carousel-control-prev" href="#example" role="button" data-slide="prev">
    <span class="carousel-control-prev-icon" aria-hidden="true"></span>
    <span class="sr-only">Previous</span>
  </a>
  <a class="carousel-control-next" href="#example" role="button" data-slide="next">
    <span class="carousel-control-next-icon" aria-hidden="true"></span>
    <span class="sr-only">Next</span>
  </a
  <!-- /.Controls -->   
</div>
<!-- /.Carousel -->
```

### mdb进阶版

基础版一般都够用了，但是如果希望能有响应式的进站大图，或者能在图片上增加一层灰度蒙版的话（可以凸显上面的文字内容），基础版就不够用了。而我本身对于这些样式不够熟悉，幸亏网上找了一个更详细的[教程](https://mdbootstrap.com/javascript/carousel/#introduction)，不仅有进站大图、进站Video、各种灰度蒙版，还有各种其他样式的轮播，比如说一组卡片等。网站还提供了[免费的静态网页](https://mdbootstrap.com/freebies/)，可以进行参考与学习。不过这些需要依靠网站自己的css文件和js文件，小白表示还没有搞懂，直接搬过来不知道为什么总是无法显示，于是最后在该网站模板的基础上进行修改了。

等到再了解一点再补充这块。

注意的是，如果使用mdb的样式，需要在\<head\>中增加以下内容：

```html
<link href="/css/vendors/mdb/mdb.min.css" rel="stylesheet">
<link href="/css/vendors/mdb/style.min.css" rel="stylesheet">
```

还需要在\<body\>结束前增加对应以下内容：

```html
<script type="text/javascript" src="/js/vendors/mdb/mdb.js"></script>
<script type="text/javascript">
  // Animations initialization
  new WOW().init();
</script>
```

值得一提的是，mdb实现响应式轮播的方式是使用css样式中的`background-image`。

## Resource资源链接汇总

[Bootstrap官网对carousel介绍](https://getbootstrap.com/docs/4.1/components/carousel/)、 [mdb关于carousel的教程](https://mdbootstrap.com/javascript/carousel/#introduction)、[mdb的免费静态网站](https://mdbootstrap.com/freebies/)

## 版本控制

| Version | Action | Time       |
| ------- | ------ | ---------- |
| 1.0     | Init   | 2018-08-12 |
| 1.1     | mdb js | 2018-08-18 |
