---
date: "2018-08-21T21:59:02+08:00"
publishdate: "2018-08-21+08:00"
lastmod: "2018-08-21+08:00"
draft: false
title: "Blog养成记(15) 创建shortcode模板便捷高亮内容"
tags: ["hugo", "blog"]
series: ["Blog养成记"]
categories: ["杂技浅尝"]
toc: true
---

## 为什么使用shortcode

由于Hugo支持在文本内容中使用html标签，因此在文本中的文字如果需要高亮可以直接使用\<mark\>标签即可。但是code fence中的代码如何进行高亮呢？直接在code fence中使用html标签会被默认为文本（理应有办法转义，但暂时还不了解）。

之前我的方案是使用highlight shortcode(参见[Blog养成记3 Hugo的语法高亮配置]({{< ref "/sci-tech/2018-01/blog养成记3-hugo的语法高亮配置" >}}))，但是由于highlight渲染的风格难以调整的关系，对于这个使用起来非常不灵活。而且使用hightlight shortcode只能一行一行地高亮，不够灵活，有时只想高亮代码段中部分代码怎么办？

## 什么是shortcode模板

[Hugo官网](https://gohugo.io/content-management/shortcodes/)对Shortcode介绍为出现在内容文件中可以自定义的代码片段(snippets)，可以减少作者在Markdown的内容中增加大段html的标签内容。Hugo本身提供了一些内建的shortcode，比如我之前使用的highlight就是其中一个，还有一些其他常用的片段内容。

Shortcode不会在Hugo模板(template)中起作用，对应在Hugo模板中的片段应放在`partial`文件夹中作为partial模板。

## 新建shortcode改变背景色

在`/layouts/shortcodes/`文件夹中新建一个html文件，文件名是shortcode的代码名。

在这个场景中，我想要改变选中文字的背景色，因此我新建了`bgstyle.html`文件。

在该文件中写shortcode的内容：

```html
{{- if eq (.Get 0)  "yellow" -}}
    <mark style="background: #ffeb92fd;">
        {{- .Inner -}}
    </mark>
{{- else if eq (.Get 0) "red" -}}
    <mark style="background: #fa9494;">
        {{- .Inner -}}
    </mark>
{{- else if eq (.Get 0) "blue" -}}
    <mark style="background: #9ccaff;">
        {{- .Inner -}}
    </mark>
{{- else if eq (.Get 0) "green" -}}
    <mark style="background: #acffea;">
        {{- .Inner -}}
    </mark>
{{- else if eq (.Get 0)  "purple" -}}
    <mark style="background: #eeb4ff;">
        {{- .Inner -}}
    </mark>
{{- else -}}
    <mark style="background: .Get 0 ;">
        {{- .Inner -}}
    </mark>    
{{- end -}}
```

在这个样例中，`.Get 0`是获得shortcode的第一个参数，在这里是背景颜色参数，`.Inner`是shortcode开始和结束标记之间的内容，`{{- code -}}`与`{{ code }}`的区别是前者会将html标签间的空格换行都去了，而后者会保留。由于我不希望改变高亮的格式，因此选择前者。

实际上shortcode模板中有更多的内容，包括传入多个参数等，这些就需要参看[官方文档](https://gohugo.io/content-management/shortcodes/)了。

## 如何使用shortcode

有两组使用方法，前者(%)在开始/结束标记之间会出现Markdown语法，后者不会：

```html
<!-- 会出现Markdown语法 -->
{{%/* myshortcode */%}}Hello **World!**{{%/* /myshortcode */%}}

<!-- 不会出现Markdown语法 -->
{{</* myshortcode */>}}<p>Hello <strong>World!</strong></p>{{</* /myshortcode */>}}
```

在我上面的例子中，使用方式和效果如下：

```html
我就想{{%/* bgstyle blue */%}}尝试一下{{%/* /bgstyle */%}}如何使用shortcode.
我就想{{% bgstyle blue %}}尝试一下{{% /bgstyle %}}如何使用shortcode.
```

## Resource资源链接汇总

[Hugo官网对Shortcode介绍](https://gohugo.io/content-management/shortcodes/)  

## 版本控制

| Version | Action | Time       |
| ------- | ------ | ---------- |
| 1.0     | Init   | 2018-08-21 |
