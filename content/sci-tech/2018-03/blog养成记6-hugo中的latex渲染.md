---
date: "2018-03-19T22:33:45+08:00"
draft: false
title: "Blog养成记(6) Hugo中的LaTeX渲染"
tags: ["hugo", "LaTeX","blog"]
series: ["Blog养成记"]
categories: ["杂技浅尝"]
summary: "Hugo本身并不支持\\(\\LaTeX\\)，但可以通过javascript进行渲染。Hugo官网提供了多种方法，由于这篇博客我决定选择\\(\\KaTeX\\)，而不是MathJax。"
toc: true
---

## 前言

Hugo本身并不支持\\(\\LaTeX\\)，但可以通过javascript进行渲染。Hugo官网提供了多种方法，由于[这篇博客](http://nosubstance.me/post/a-great-toolset-for-static-blogging/)我决定选择\\(\\KaTeX\\)，而不是MathJax。



## 安装

在head.html的</head>标签前加上以下语句：

```Html
<!-- KaTeX -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.9.0/katex.min.css" integrity="sha384-TEMocfGvRuD1rIAacqrknm5BQZ7W7uWitoih+jMNFXQIbNl16bO8OZmylH/Vi/Ei" crossorigin="anonymous">
<script src="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.9.0/katex.min.js" integrity="sha384-jmxIlussZWB7qCuB+PgKG1uLjjxbVVIayPJwi6cG6Zb4YKq0JIw+OMnkkEC7kYCq" crossorigin="anonymous"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.9.0/contrib/auto-render.min.js" integrity="sha384-IiI65aU9ZYub2MY9zhtKd1H2ps7xxf+eb2YFG9lX6uRqpXCvBTOidPRCXCrQ++Uc" crossorigin="anonymous"></script>

<script>
    document.addEventListener("DOMContentLoaded", function() {
      renderMathInElement(document.body);
    });
</script>
```

不过0.9.0版本似乎很难链接，还是从[这里](https://github.com/Khan/KaTeX/releases)下载js和css，并放在`static/js`文件夹中，并将上面代码改为：

```html
<!-- KaTeX -->
<link rel="stylesheet" href="/js/katex/katex.min.css" >
<script src="/js/katex/katex.min.js" > </script>
<script src="/js/katex/contrib/auto-render.min.js" ></script>
 
<script>
    document.addEventListener("DOMContentLoaded", function() {
      renderMathInElement(document.body);
    });
</script>
```



## 使用

尽管[KaTeX的github](https://github.com/Khan/KaTeX/blob/master/contrib/auto-render/README.md)上是如下给出auto-render的默认值的：

```yaml
[
  {left: "$$", right: "$$", display: true},
  {left: "\\[", right: "\\]", display: true},
  {left: "\\(", right: "\\)", display: false}
]
```

但实际上，三种标识符都成立，其中前两种将数学公式以block形式展现，第三种以inline形式展现。使用中只需要将left和right放在数学公式两侧即可。

## Resource资源链接汇总

我建立的docker for Hugo开发镜像:  [Docker Hub上的repo](https://hub.docker.com/r/orianna/hugo-docker-dev/)、[Github上的repo](https://github.com/orianna-zzo/hugo-docker-dev)。  
我的个人主页Hugo代码:  [blog-hugo](https://github.com/orianna-zzo/blog-hugo)  
[KaTeX Github](https://github.com/Khan/KaTeX)、[KaTeX官网](https://khan.github.io/KaTeX/)、[KaTeX auto-render](https://github.com/Khan/KaTeX/blob/master/contrib/auto-render/README.md)、[KaTeX contents](https://khan.github.io/KaTeX/function-support.html#top)

## 版本控制

| Version | Action | Time       |
| ------- | ------ | ---------- |
| 1.0     | Init   | 2018-03-19 |