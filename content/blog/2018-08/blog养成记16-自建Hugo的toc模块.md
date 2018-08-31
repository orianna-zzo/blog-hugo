---
date: "2018-08-25T22:59:59+08:00"
publishdate: "2018-08-31+08:00"
lastmod: "2018-08-31+08:00"
draft: false
title: "Blog养成记(16) 自建Hugo的TOC模板"
tags: ["hugo", "blog", "toc"]
series: ["Blog养成记"]
categories: ["杂技浅尝"]
toc: true
---

Table Of Content是一个十分常用的功能，这个系列的第13篇[增加一个TOC侧边栏]({{< ref "/blog/2018-08/blog养成记13-增加一个toc侧边栏" >}})就是为了这个做准备，只不过是在静态页面上尝试想要的样式。

## Hugo的Table of Content

Hugo对于Table Of Content也有内建变量，可以参考[这里](https://gohugo.io/content-management/toc/)。

在正文部分，Markdown正文中所有的标题都会自动建一个`id`，`id`为标题内容，也可以用`{#your-id}`来重新定义。TOC目录部分，Hugo对于这一部分渲染为`<nav id="TableOfContents"><ul></ul></nav>`，目录会连接到对应的`id`上。

我建立的partial模板如下：

```html
{{ if .Params.toc }}
<!--Grid column-->
<div class="col-md-2">
	<!--Scrollspy-->
	<div id="page-scrollspy" class="toc-nav">
		{{ .TableOfContents }}
	</div>
	<!--Scrollspy-->
</div>
<!--Grid column-->
{{- end -}}
```

##### 存在问题

其中，正如第13篇[增加一个TOC侧边栏]({{< ref "/blog/2018-08/blog养成记13-增加一个toc侧边栏" >}})说的，我在baseof.html模板中规定了\<body\>的样式，想使用滚动监听，同样也配置了第14篇[让同页滚动更平滑]({{< ref "/blog/2018-08/blog养成记14-让同页滚动更平滑" >}})。

```
<body class="bg-light" data-spy="scroll" data-target="#page-scrollspy" data-offset="90">
```

但可惜的是，由于Hugo自动渲染的html代码中缺少滚动监听所必须的类，因此无法实现滚动监听的功能。此外，Hugo的TOC对1~6级标题都包括了进来，这样光1级和6级之间的缩进空间就占了不少。因此不得不自己重新写TOC的模板。

## 自建TOC模板

在[Github的Issue](https://github.com/gohugoio/hugo/issues/1778)中找到了对于这些问题的一个方法，因此决定参考着也定制化地写一个我自己的TOC模板。模板中我并没有选择监听所有的标题，而只选择了\<h1\>~\<h4\>。

```
{{ if .Params.toc }}
	<!-- ignore empty links with + -->
	{{ $headers := findRE "<h[1-4].*?>(.|\n])+?</h[1-4]>" .Content }}
	<!-- at least one header to link to -->
	{{ if ge (len $headers) 1 }}

		<!--Grid column-->
		<div class="col-md-2 pl-0">

			<!--Scrollspy-->
			<div id="page-scrollspy" class="toc-nav">
				
				<ul class="nav nav-pills ml-0">
					<!-- TOC header -->
					<li class="nav-item pb-3 text-center">
						<span class="font-weight-bold mb-2">- CATALOG -</span>
					</li>
					{{ range $headers }}
						{{ $header := . }}
						{{ range first 1 (findRE "<h[1-4]" $header 1) }}
							{{ range findRE "[1-4]" . 1 }}
								{{ $next_heading := (int . ) }}
								{{ range seq $next_heading }}
									<ul class="nav">
								{{end}}
								{{ $anchorId :=  (replaceRE ".* id=\"(.*?)\".*" "$1" $header ) }}
										<li class="nav-item">
						 					<a class="nav-link" href="#{{ $anchorId }}">
												 {{ $header | plainify | htmlEscape }}
											</a>
										</li>
						 
								<!-- close list -->
								{{ range seq $next_heading }}
									</ul>
								{{end}}
							{{ end }}
						{{ end }}
				 {{end}}

				</ul>
			</div>
			<!--Scrollspy-->

		</div>
		<!--Grid column-->
	{{ end }}
{{- end -}}
```

### 解决`href`中文乱码问题

需要注意的是，在上述的toc模板中`$anchorId`在\<a href\>中由于出现中文，会变成乱码，影响滚动监听。但很神奇的是，我直接在html中的`href`写中文，正常显示，把`$anchorId`显示在文中，也是正常显示。

多次尝试无果，无奈之下，决定修改`bootstrap.js`来解决这个问题。

对于`bootstrap.js`，需要修改两个地方。

一个是`Public Util Api`中的`getSelectorFromElement。`需要对`href`的内容进行解码，获得正确的中文，因为正文中的`id`是正确的中文，只有这样才能获得正确的selector。

```javascript
getSelectorFromElement: function getSelectorFromElement(element) {
        var selector = element.getAttribute('data-target');

        if (!selector || selector === '#') {
          selector = {{% bgstyle purple %}}decodeURI(element.getAttribute('href')){{% /bgstyle %}} || '';
        }

        try {
          return document.querySelector(selector) ? selector : null;
        } catch (err) {
          return null;
        }
      },
```

另一个是`scrollspy`中`_activate`函数。由于是需要根据`href`的内容获取selector，因此需要将`id`进行编码获得真实的`href`值。

```javascript
_proto._activate = function _activate(target) {
    this._activeTarget = target;

    this._clear();

    var queries = this._selector.split(','); // eslint-disable-next-line arrow-body-style


    queries = queries.map(function (selector) {
        return selector + "[data-target=\"" + target + "\"]," + (selector + "[href=\"" + {{% bgstyle purple %}}encodeURI(target).toLowerCase(){{% /bgstyle %}} + "\"]");
    });
    var $link = $$$1([].slice.call(document.querySelectorAll(queries.join(','))));

    if ($link.hasClass(ClassName.DROPDOWN_ITEM)) {
        $link.closest(Selector.DROPDOWN).find(Selector.DROPDOWN_TOGGLE).addClass(ClassName.ACTIVE);
        $link.addClass(ClassName.ACTIVE);
    } else {
        // Set triggered link as active
        $link.addClass(ClassName.ACTIVE); // Set triggered links parents as active
        // With both <ul> and <nav> markup a parent is the previous sibling of any nav ancestor

        $link.parents(Selector.NAV_LIST_GROUP).prev(Selector.NAV_LINKS + ", " + Selector.LIST_ITEMS).addClass(ClassName.ACTIVE); // Handle special case when .nav-link is inside .nav-item

        $link.parents(Selector.NAV_LIST_GROUP).prev(Selector.NAV_ITEMS).children(Selector.NAV_LINKS).addClass(ClassName.ACTIVE);
    }

    $$$1(this._scrollElement).trigger(Event.ACTIVATE, {
        relatedTarget: target
    });
};
```



## Resource资源链接汇总

[Hugo官网对TableOfContent介绍](https://gohugo.io/content-management/toc/)、[自建TOC参考](https://github.com/gohugoio/hugo/issues/1778)  

## 版本控制

| Version | Action | Time       |
| ------- | ------ | ---------- |
| 1.0     | Init   | 2018-08-25 |
