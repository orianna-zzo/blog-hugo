---
date: "2018-08-11T19:00:35+08:00"
draft: false
title: "Blog养成记(9) 项目结构&前端环境配置"
tags: ["环境配置", "前端", "项目结构"]
series: ["Blog养成记"]
categories: ["杂技浅尝"]
toc: true
---

其实如果只是对静态页面进行修改，不需要配什么环境，只需要对html进行修改就好。

哦，不对，因为还是会使用hugo来生成静态页面，hugo还是要配置的，配置的方法详见这个系列的第2篇[Blog养成记2 Hugo+Docker在Github上建立Blog]({{< ref "/sci-tech/2018-01/blog养成记2-hugo-docker在github上建立blog" >}})。所以重点就在如何自己生成自己喜欢的hugo模板了。

由于仅使用hugo静态页面，所以也不计划使用gulp之类的工具，可能出了hugo外唯一考虑要使用的是sass。这是因为css的结构化有些糟，而sass作为css的预编译器，有更好的结构性，更像一门编程语言，修改起来会更得心应手，(我猜:no_mouth:)不会有改了A不知道会不会影响B的囧状。sass具体的配置详见`前端试水`系列第2篇[前端试水2 使用docker镜像的sass配置]({{< ref "/sci-tech/2018-08/前端试水2-使用docker镜像的sass配置" >}})。

## 项目结构

参考Hugo的其他theme和[官网](https://gohugo.io/getting-started/directory-structure/)对各个文件夹的介绍，对新建主题的文件结构如下：

```
MyNewTheme
├── archetypes/
├── exampleSite/
├── i18n/
├── layouts/
⎮	├── _default/
⎮	├── partials/
⎮	├── page
⎮	├── post
⎮	├── shortcodes/
⎮	├── 404.html
⎮	├── index.html
⎮	└── robots.txt
├── static/
⎮	├── css/
⎮	├── js/
⎮	├── scss/
⎮	├── font/
⎮	└── img/
└── theme.toml
```

其中`archetypes`定义了如果使用`hugo new`，默认生成的`md`的文件头；`exampleSite`提供一个网站实例；`i18n`提供多语言的翻译；`layouts`提供主题的布局；`static`存放静态文件；`theme.toml`提供主题的信息。

[Hugo官网](https://gohugo.io/templates/)对于每个模板都会有简单的介绍，可以借鉴一下。

其中`scss`主要参考[Sass目录层次](https://www.sasscss.com/sass-guidelines/architecture/)，建立以下scss的项目结构：

```
scss
├── abstracts/
├── base/
├── components/
├── layout/
├── pages/ 
├── vendors/
⎮	└── bootstrap4/
└── main.scss
```

`abstracts`存放整个项目的sass辅助工具；`base`存放项目中的模板文件；`components`存放小型组件；`layout`存放布局部分；`pages`存放有特定样式的页面；`vendors`存放外部库和框架；`main.scss`为主文件，其中引用其他文件夹中的代码，引用次序为：abstracts - vendors - base - layout - components - pages。

## Resource资源链接汇总

我建立的docker for Hugo开发镜像: [Docker Hub上的repo](https://hub.docker.com/r/orianna/hugo/)、[Github上的repo](https://github.com/orianna-zzo/hugo-docker-dev)。
我建立的docker for Sass开发镜像:  [Libsass docker镜像](https://hub.docker.com/r/orianna/libsass/)、[Dart Sass docker镜像](https://hub.docker.com/r/orianna/dart-sass/)、[Docker for Sass的Dockerfile](https://github.com/orianna-zzo/dockerfile-repo/tree/master/front-end-docker/sass-docker)。  

[Hugo官网](https://gohugo.io/getting-started/directory-structure/)、[Hugo官网介绍各个文件夹](https://gohugo.io/getting-started/directory-structure/)、[Hugo官网介绍Template](https://gohugo.io/templates/)
[Sass官网](http://sass-lang.com)、[Sass目录层次](https://www.sasscss.com/sass-guidelines/architecture/)

## 版本控制

| Version | Action | Time       |
| ------- | ------ | ---------- |
| 1.0     | Init   | 2018-08-11 |