---
date: "2018-01-09T17:52:25+08:00"
Lastmod: "2018-08-13+08:00"
draft: false
title: "Blog养成记(3) Hugo的语法高亮配置"
tags: ["hugo", "语法高亮", "blog"]
series: ["Blog养成记"]
categories: ["杂技浅尝"]
toc: true

---

## 前言

Hugo官网在[这里](http://gohugo.io/content-management/syntax-highlighting/)给出了详细的语法高亮配置说明。

一般有以下几种常见方法：  
1. 使用Hugo默认Chroma (弃，一些代码无法高亮)   

2. [使用Pygments]({{< relref "#使用Pygments进行高亮" >}}) (弃，一些代码无法高亮)   

3. [使用Highlight Shortcode]({{< relref "#使用highlight-shortcode进行高亮" >}})  (弃，高亮颜色渲染问题)   

5. [使用Highlight.js]({{< relref "#使用highlight.js进行高亮" >}})  :heavy_check_mark:

## 使用Pygments进行高亮

Hugo从0.28版本开始默认使用Chroma来作语法高亮。Chroma使用go编写的，渲染速度很快。
如果需要使用Pygments，需要先安装Pygments，并在网站配置文件中设置一些相关参数。

### Pygments安装

我在建立Hugo镜像时已经安装了Pygments，不然需要先安装Pygments。如果在Debian和Ubuntu系统中可以用下面语句安装，其他系统也可参考：
```bash
$ sudo apt-get install python3-pygments
```

### Pygments配置

下面是我按官网在 `config.toml` 中配置的参数：
```toml
[highlighting] 
  pygmentsUseClassic       = true
  pygmentsCodeFences       = true
  pygmentsStyle            = "autumn"
```

其中，
`pygmentsUseClassic=true` 说明使用Pygments来进行语法高亮；  
`pygmentsCodeFences=true` 使在code fence中的根据设置的语言标签进行语法高亮；  
`pygmentsStyle="autumn"` 设置高亮的风格,可以在[这里](https://help.farbox.com/pygments.html)查看各高亮风格，选择最心仪的。

我选择了 `autumn`，下面是在code fence中的高亮示例：
```python
#!/usr/bin/python3

from engine import RunForrestRun

"""Test code for syntax highlighting!"""

class Foo:
	def __init__(self, var):
		self.var = var
		self.run()

	def run(self):
		RunForrestRun()  # run along!
```



由于语法高亮渲染需要时间，因此语法高亮默认是放在缓存的，如果需要查看则需要清空缓存。

### Pygments存在问题

与Chroma一样，Pygments对于一些格式无法进行高亮。现在发现的就有无法对shell脚本高亮，也无法识别toml文件。

## 使用Highlight Shortcode进行高亮

除了可以使用code fence高亮代码，还可Hugo内建的highlight shortcode进行高亮。highlight shortcode的优点是不仅可以根据语法进行高亮，还能在code fence中指定行进行mark，还可以设置代码的行号，比较灵活。

使用下面的语法：

```
{{</* highlight python "linenos=inline, hl_lines=3 8 12, linenostart=199" */>}}
# 代码放在这里
{{</* /highlight */>}}
```

其中，`linenos=inline`或者`linenos=table`提示显示代码前的行数，`hl_lines`设置高亮代码，`linenostart`设置代码行数的起始数字。

{{<highlight python "linenos=inline,hl_lines=3 8 12,linenostart=199">}}
#!/usr/bin/python3

from engine import RunForrestRun

"""Test code for syntax highlighting!"""

class Foo:
​	def __init__(self, var):
​		self.var = var
​		self.run()

	def run(self):
		RunForrestRun()  # run along!
{{</highlight >}}


#### 2018.8.13 `Highlight Shortcode`颜色渲染问题修正

如题，突然发现颜色渲染有问题，所以只能逐个看到底用了那个css。发现我的主页用了`highlight shortcode`的css样式和官网的不一样。鉴于不想花太多时间，只要能看就要，因此在`override.css`中加了强制的css样式，于是问题暂时打个不怎么好看的补丁，但至少`highlight shortcode`还是勉强可看了。

```css
.highlight pre {
    background-color: #f7f7f7 !important;
    color: rgb(194, 192, 192) !important;
}
```

#### 2018.8.21 自建shortcode替代Hightlight Shortcode

由于highlight shortcode的渲染样式实在是太诡异，难以调整，因此自己建立专门用于高亮的shortcode，这些shortcode也可以在代码块中使用，可以不仅只渲染一行，更为灵活方便。于是就可以弃用highlight shortcode了。具体内容详见[Blog养成记15 创建shortcode模板便捷高亮内容]({{< ref "/sci-tech/2018-08/blog养成记15-创建shortcode模板便捷高亮内容" >}})。

## 使用highlight.js进行高亮

highlight.js这个插件使用起来特别方便，还可以定制化选择需要高亮的语言，只需要在[highlight.js官网](https://highlightjs.org/download/)选择所需要的语言，下载即可。然后解压文件中，<code>highlight.pack.js</code>决定那些语言的代码会根据语法进行高亮，在style文件夹中选择喜欢的css样式文件，css文件决定代码高亮的颜色，最后我选择了<code>github-gist.css</code>

然后需要在\<head\>中增加：

```html
<link rel="stylesheet" href="/path/to/styles/default.css">
```

在\<body\>结束前增加：
```html
<script src="/path/to/highlight.pack.js"></script>
<script>hljs.initHighlightingOnLoad();</script>
```

## Resource资源链接汇总

我建立的docker for Hugo开发镜像:  [Docker Hub上的repo](https://hub.docker.com/r/orianna/hugo-docker-dev/)、[Github上的repo](https://github.com/orianna-zzo/hugo-docker-dev)。  
我的个人主页Hugo代码:  [blog-hugo](https://github.com/orianna-zzo/blog-hugo)  

[Hugo官网](https://gohugo.io)、[关于语法高亮说明](http://gohugo.io/content-management/syntax-highlighting/)、[pygments主题配色](https://help.farbox.com/pygments.html)、[highlight.js官网](https://highlightjs.org/download/)

## 版本控制

| Version | Action                    | Time       |
| ------- | ------------------------- | ---------- |
| 1.0     | Init                      | 2018-01-09 |
| 1.1     | 增加tag、版本控制及资源链接  | 2018-01-17 |
| 1.2 | highlight颜色修正 | 2018-08-13 |
| 2.0 | 修改结构，增加highlight.js | 2018-08-21 |


