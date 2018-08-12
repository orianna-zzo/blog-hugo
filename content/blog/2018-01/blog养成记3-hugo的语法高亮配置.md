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

一般有以下3种常见方法：  
1. 使用Hugo默认Chroma    
2. 使用Pygments    
3. 使用CSS  

## 使用Pygments进行高亮

Hugo从0.28版本开始默认使用Chroma来作语法高亮。Chroma使用go编写的，渲染速度很快。
如果需要使用Pygments，需要先安装Pygments，并在网站配置文件中设置一些相关参数。

### Pygments安装

我在建立Hugo镜像时已经安装了Pygments，不然需要先安装Pygments。如果在Debian和Ubuntu系统中可以用下面语句安装，其他系统也可参考：
```shell
$ sudo apt-get install python3-pygments
```

### Pygments配置

下面是我按官网在 `config.toml` 中配置的参数：
```
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

除了可以使用code fence高亮代码，还可使用下面的语法：

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
	def __init__(self, var):
		self.var = var
		self.run()

	def run(self):
		RunForrestRun()  # run along!
{{</highlight >}}

由于语法高亮渲染需要时间，因此语法高亮默认是放在缓存的，如果需要查看则需要清空缓存。

### Pygments存在问题

现在还有一些遗留问题，等待后续解决：

1. shell脚本无法高亮  
2. 对toml文件无法识别

#### 2018.8.13 `Highlight shortcode`颜色渲染问题修正

如题，突然发现颜色渲染有问题，所以只能逐个看到底用了那个css。发现我的主页用了`highlight shortcode`的css样式和官网的不一样。鉴于不想花太多时间，只要能看就要，因此在`override.css`中加了强制的css样式，于是问题暂时打个不怎么好看的补丁，但至少`highlight shortcode`还是勉强可看了。

```css
.highlight pre {
    background-color: #f7f7f7 !important;
    color: rgb(194, 192, 192) !important;
}
```

## 使用CSS进行高亮

感觉使用CSS进行语法高亮会相对更灵活、可定制。但是暂时还未尝试，若后续尝试则在此处补充。


## Resource资源链接汇总

我建立的docker for Hugo开发镜像:  [Docker Hub上的repo](https://hub.docker.com/r/orianna/hugo-docker-dev/)、[Github上的repo](https://github.com/orianna-zzo/hugo-docker-dev)。  
我的个人主页Hugo代码:  [blog-hugo](https://github.com/orianna-zzo/blog-hugo)  

[Hugo官网](https://gohugo.io)、[关于语法高亮说明](http://gohugo.io/content-management/syntax-highlighting/)、[pygments主题配色](https://help.farbox.com/pygments.html)

## 版本控制

| Version | Action                    | Time       |
| ------- | ------------------------- | ---------- |
| 1.0     | Init                      | 2018-01-09 |
| 1.1     | 增加tag、版本控制及资源链接  | 2018-01-17 |
| 1.2 | highlight颜色修正 | 2018-08-13 |


