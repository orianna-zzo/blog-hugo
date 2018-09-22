---
date: "2018-08-17T21:01:32+08:00"
publishdate: "2018-08-17+08:00"
lastmod: "2018-08-17+08:00"
draft: false
title: "Blog养成记(13) 增加一个TOC侧边栏"
tags: ["前端", "css", "blog", "scrollspy", "bootstrap"]
series: ["Blog养成记"]
categories: ["杂技浅尝"]
toc: true
---

想在页面右侧保留一个固定在屏幕上的TOC导航栏(Table Of Content)，可以直接选择跳转的网页段落位置，此外也可以提醒文章结构。

## Scrollspy

这个插件bootstrap中有，起名叫[scrollspy滚动监听](https://getbootstrap.com/docs/4.1/components/scrollspy)，但是原版的实在不是我想要的样式。之前在[blog养成记10-实现幻灯片轮播效果]({{< ref "/sci-tech/2018-08/blog养成记10-实现幻灯片轮播效果" >}})中使用的[mdb的这个插件](https://mdbootstrap.com/javascript/scrollspy/#mdb-scrollspy)有更好的样式，不过可惜这个是pro的样式，所以只能自己动手丰衣足食啦。

### Bootstrap基础版

scrollspy使用时分为两个部分，一个是正文被监控的部分(spied element)，另一个是监听菜单部分(Scrollspy)。前者需要将`data-target`设为后者的id，并设置`data-spy="scroll"`，后者需要连接的`href`设为在前者内连接的id。


在bootstrap提供的样例中，使用`.nav`或者`.list-group`来实现，动画效果也是基于这两者。由于`.list-group`只是一级目录，而在nested nav中，如果一个内部的`.nav`是`.active`的，那么它上级的`.nav`也是`.active`的，比较符合我的期望，因此放弃了`.list-group`的方法，具体可详见官网的教程。

此外，需要在Spied Element中增加`position: relative;`。当监听对象Spied Element不是\<body\>时，还需要增加`overflow-y: scroll;`。如果希望监听菜单Scrollspy能够浮动在该元素中，而不是随着页面滚动消失的话，可以在监听菜单部分增加以下样式`position: sticky;`

Bootstrap的Scrollspy我尝试了好几次，都无法达到页面展示的效果，甚至我担心是js冲突仅适用Bootstrap的框架，但还是无法达到监听的效果。我还怀疑过是否是Hugo的原因，但事实上即使不适用hugo，直接写一个index.html也无法达到监听的效果。后来直接将这些网站的源码都贴过来尝试，最后发现是因为如果监控对象不是\<body\>的话，监控对象的height必须有个数值，不能是auto或者100%

基于nested nav的scrollspy的样例如下，重点部分进行高亮：

```html

<div class="row">
      <div class="col-4">
        <!-- Scrollspy -->
        <nav {{% bgstyle purple %}}id="navbar-example3{{% /bgstyle %}}" class="navbar navbar-light bg-light flex-column"  style="{{% bgstyle purple %}}position: sticky;{{% /bgstyle %}} top:6rem;">
          <a class="navbar-brand" href="#">Navbar</a>
          <nav class="{{% bgstyle purple %}}nav nav-pills{{% /bgstyle %}} flex-column">
            <{{% bgstyle purple %}}a class="nav-link"{{% /bgstyle %}} href="#item-1">Item 1</a>
            <nav class="nav nav-pills flex-column">
              <a class="nav-link ml-3 my-1" href="#item-1-1">Item 1-1</a>
              <a class="nav-link ml-3 my-1" href="#item-1-2">Item 1-2</a>
            </nav>
            <a class="nav-link" href="#item-2">Item 2</a>
            <a class="nav-link" href="#item-3">Item 3</a>
            <nav class="nav nav-pills flex-column">
              <a class="nav-link ml-3 my-1" href="#item-3-1">Item 3-1</a>
              <a class="nav-link ml-3 my-1 active" href="#item-3-2">Item 3-2</a>
            </nav>
          </nav>
        </nav>
      </div>
      
      <div class="col-8">
        <!-- Spied Element -->
        <!-- data-target=Scrollspy的id --> 
        <div {{% bgstyle purple %}}data-spy="scroll" data-target="#navbar-example3" data-offset="0"{{% /bgstyle %}} style="{{% bgstyle purple %}}position: relative; height: 500px; overflow: auto;{{% /bgstyle %}}">
          <h4 id="item-1">Item 1</h4>
          <p>Ex consequat commodo adipisicing exercitation aute excepteur occaecat ullamco duis aliqua id magna ullamco eu. Do aute ipsum ipsum ullamco cillum consectetur ut et aute consectetur labore. Fugiat laborum incididunt tempor eu consequat enim dolore proident. Qui laborum do non excepteur nulla magna eiusmod consectetur in. Aliqua et aliqua officia quis et incididunt voluptate non anim reprehenderit adipisicing dolore ut consequat deserunt mollit dolore. Aliquip nulla enim veniam non fugiat id cupidatat nulla elit cupidatat commodo velit ut eiusmod cupidatat elit dolore.</p>
          <h5 id="item-1-1">Item 1-1</h5>
          <p>Amet tempor mollit aliquip pariatur excepteur commodo do ea cillum commodo Lorem et occaecat elit qui et. Aliquip labore ex ex esse voluptate occaecat Lorem ullamco deserunt. Aliqua cillum excepteur irure consequat id quis ea. Sit proident ullamco aute magna pariatur nostrud labore. Reprehenderit aliqua commodo eiusmod aliquip est do duis amet proident magna consectetur consequat eu commodo fugiat non quis. Enim aliquip exercitation ullamco adipisicing voluptate excepteur minim exercitation minim minim commodo adipisicing exercitation officia nisi adipisicing. Anim id duis qui consequat labore adipisicing sint dolor elit cillum anim et fugiat.</p>
          <h5 id="item-1-2">Item 1-2</h5>
          <p>Cillum nisi deserunt magna eiusmod qui eiusmod velit voluptate pariatur laborum sunt enim. Irure laboris mollit consequat incididunt sint et culpa culpa incididunt adipisicing magna magna occaecat. Nulla ipsum cillum eiusmod sint elit excepteur ea labore enim consectetur in labore anim. Proident ullamco ipsum esse elit ut Lorem eiusmod dolor et eiusmod. Anim occaecat nulla in non consequat eiusmod velit incididunt.</p>
          <h4 id="item-2">Item 2</h4>
          <p>Quis magna Lorem anim amet ipsum do mollit sit cillum voluptate ex nulla tempor. Laborum consequat non elit enim exercitation cillum aliqua consequat id aliqua. Esse ex consectetur mollit voluptate est in duis laboris ad sit ipsum anim Lorem. Incididunt veniam velit elit elit veniam Lorem aliqua quis ullamco deserunt sit enim elit aliqua esse irure. Laborum nisi sit est tempor laborum mollit labore officia laborum excepteur commodo non commodo dolor excepteur commodo. Ipsum fugiat ex est consectetur ipsum commodo tempor sunt in proident.</p>
          <h4 id="item-3">Item 3</h4>
          <p>Quis anim sit do amet fugiat dolor velit sit ea ea do reprehenderit culpa duis. Nostrud aliqua ipsum fugiat minim proident occaecat excepteur aliquip culpa aute tempor reprehenderit. Deserunt tempor mollit elit ex pariatur dolore velit fugiat mollit culpa irure ullamco est ex ullamco excepteur.</p>
          <h5 id="item-3-1">Item 3-1</h5>
          <p>Deserunt quis elit Lorem eiusmod amet enim enim amet minim Lorem proident nostrud. Ea id dolore anim exercitation aute fugiat labore voluptate cillum do laboris labore. Ex velit exercitation nisi enim labore reprehenderit labore nostrud ut ut. Esse officia sunt duis aliquip ullamco tempor eiusmod deserunt irure nostrud irure. Ullamco proident veniam laboris ea consectetur magna sunt ex exercitation aliquip minim enim culpa occaecat exercitation. Est tempor excepteur aliquip laborum consequat do deserunt laborum esse eiusmod irure proident ipsum esse qui.</p>
          <h5 id="item-3-2">Item 3-2</h5>
          <p>Labore sit culpa commodo elit adipisicing sit aliquip elit proident voluptate minim mollit nostrud aute reprehenderit do. Mollit excepteur eu Lorem ipsum anim commodo sint labore Lorem in exercitation velit incididunt. Occaecat consectetur nisi in occaecat proident minim enim sunt reprehenderit exercitation cupidatat et do officia. Aliquip consequat ad labore labore mollit ut amet. Sit pariatur tempor proident in veniam culpa aliqua excepteur elit magna fugiat eiusmod amet officia.</p>
        </div>
      </div>
    </div>

```

### 参考mdb最后采用版

就如上一节所说的，如果监控的不是\<body\>就一定需要定义height，而定义了height就会出现一个内部的滚动窗口，这与我设想的不一致。于是决定到`baseof.html`中修改\<body\>，并规定id为`page-scrollspy`的是全页的滚动监听菜单。

```html
<body class="bg-light" data-spy="scroll" data-target="#page-scrollspy" data-offset="90">
```

所使用的滚动监听菜单部分如下。基本外层确定id为`page-scrollspy`并使用`.toc-nav`，里面使用无序列表，最外层\<ul\>使用`.nav`和`.nav-pills`类，每个子项都是\<li class="nav-item"\>里面再套\<a class="nav-link"\>，若还存在子列表，使用\<ul class="nav"\>并加上缩进`.ml-3`。

```html
<!--Scrollspy-->
<div {{% bgstyle purple %}}id="page-scrollspy" class="toc-nav"{{% /bgstyle %}}>

  <!-- Links -->
  <{{% bgstyle purple %}}ul class="nav nav-pills"{{% /bgstyle %}}>
    <{{% bgstyle purple %}}li class="nav-item"{{% /bgstyle %}}>
      <{{% bgstyle purple %}}a class="nav-link"{{% /bgstyle %}} href="#section1">Section 1</a>
      <ul class="nav ml-3">
        <li class="nav-item">
          <a class="nav-link" href="#subsection1A">Subsection 1A</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="#subsection1B">Subsection 1B</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="#subsection1C">Subsection 1C</a>
        </li>
      </ul>
    </li>

    <li class="nav-item">
      <a class="nav-link" href="#section2">Section 2</a>

      <ul class="nav ml-3">
        <li class="nav-item">
          <a class="nav-link" href="#subsection2A">Subsection 2A</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="#subsection2B">Subsection 2B</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="#subsection2C">Subsection 2C</a>
        </li>
      </ul>
    </li>
  </ul>
  <!-- Links -->

</div>
<!--Scrollspy-->
```

对应的，需要在css文件中增加以下内容来确定滚动监听菜单的样式，包括菜单的位置、显示的字号颜色等：

```css
.toc-nav {
  position: -webkit-sticky!important;
  position: sticky!important;
  top: 6rem;
  overflow-y: auto;

  -ms-flex-order: 2;
  order: 2;
  padding-top: 1.5rem;
  padding-bottom: 1.5rem;
  font-size: .875rem; 
}

.toc-nav ul {
  list-style: none;
  margin-left: 1rem;
  padding-left: 0;
}

.toc-nav li, .toc-nav ul {
  width: 100%;
}

.toc-nav .nav-item, .toc-nav .nav-link {
  font-size: .8rem;
  font-weight: 400;
  line-height: 1.1rem;
  padding: 0 5px;
  margin-top: 3px;
  margin-bottom: 3px;
  color: #666;
  -webkit-border-radius: 0;
  border-radius: 0;
}
```

另外，我希望`.activate`及`a:activate`时不仅仅是加粗，还能在左边有条边线，希望鼠标滑过时能也能有条细的边线。因此css增加：

```css
.toc-nav a.nav-link:hover {
  background-color: transparent;
  border-left: .025rem solid #38849e;
  -webkit-box-shadow: none;
  box-shadow: none;
  color: #38849e; 
}

.toc-nav a.nav-link.active, .toc-nav a.nav-link:active {
  background-color: transparent;
  color: black; 
  border-left: .125rem solid #38849e;
  -webkit-box-shadow: none;
  box-shadow: none;
  font-weight: 500;
} 

```



## Resource资源链接汇总

[mdb关于scrollspyl的教程](https://mdbootstrap.com/javascript/scrollspy/#mdb-scrollspy)

## 版本控制

| Version | Action   | Time       |
| ------- | -------- | ---------- |
| 1.0     | Init     | 2018-08-17 |
| 1.1     | fix bugs | 2018-09-02 |
