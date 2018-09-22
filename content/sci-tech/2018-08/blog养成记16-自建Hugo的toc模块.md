---
date: "2018-08-25T22:59:59+08:00"
publishdate: "2018-08-31+08:00"
lastmod: "2018-08-31+08:00"
draft: false
title: "Blog养成记(16) 自建Hugo的TOC模板"
tags: ["hugo", "blog", "toc", "js", "bootstrap"]
series: ["Blog养成记"]
categories: ["杂技浅尝"]
summary: "Table Of Content是一个十分常用的功能，这个系列的第13篇Blog养成记13 增加一个TOC侧边栏就是为了这个做准备，只不过是在静态页面上尝试想要的样式。本文用到的样式都基于第13篇中增加的css样式。"
toc: true
---

Table Of Content是一个十分常用的功能，这个系列的第13篇[Blog养成记13 增加一个TOC侧边栏]({{< ref "/sci-tech/2018-08/blog养成记13-增加一个toc侧边栏" >}})就是为了这个做准备，只不过是在静态页面上尝试想要的样式。本文用到的样式都基于第13篇中增加的css样式。

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

**存在问题**

其中，正如第13篇[Blog养成记13 增加一个TOC侧边栏]({{< ref "/sci-tech/2018-08/blog养成记13-增加一个toc侧边栏" >}})说的，我在baseof.html模板中规定了\<body\>的样式，想使用滚动监听，同样也配置了第14篇[Blog养成记14 让同页滚动更平滑]({{< ref "/sci-tech/2018-08/blog养成记14-让同页滚动更平滑" >}})。

```
<body class="bg-light" data-spy="scroll" data-target="#page-scrollspy" data-offset="90">
```

但可惜的是，由于Hugo自动渲染的html代码中缺少滚动监听所必须的类，因此无法实现滚动监听的功能。此外，Hugo的TOC对1~6级标题都包括了进来，这样光1级和6级之间的缩进空间就占了不少。因此不得不自己重新写TOC的模板。

## 自建TOC模板

在[Github的Issue](https://github.com/gohugoio/hugo/issues/1778)中找到了对于这些问题的一个方法，因此决定参考着也定制化地写一个我自己的TOC模板。模板中我并没有选择监听所有的标题，而只选择了\<h1\>~\<h4\>。此外，由于一些post中并不存在\<h1\>，对这种情况作了额外处理，避免TOC目录右移太多。

```
{{ if .Params.toc }}
	<!-- ignore empty links with + -->
	{{ $headers := findRE "<h[1-4].*?>(.|\n])+?</h[1-4]>" .Content }}
	<!-- at least one header to link to -->
	{{ if ge (len $headers) 1 }}
		{{% bgstyle purple %}}{{ $h1_n := len (findRE "<h1.*?>(.|\n])+?</h1>" .Content) }}{{% /bgstyle %}}
		{{% bgstyle purple %}}{{ $re := (cond (eq $h1_n 0) "<h[2-4]" "<h[1-4]") }}{{% /bgstyle %}}
		{{% bgstyle purple %}}{{ $renum := (cond (eq $h1_n 0) "[2-4]" "[1-4]") }}{{% /bgstyle %}}
	

		<!--Grid column-->
		<div class="col-md-2 pl-0">

			<!--Scrollspy-->
			<div id="page-scrollspy" class="toc-nav">
				
				<ul class="nav nav-pills ml-0">
					<!-- TOC header -->
					<li class="nav-item pb-3 text-center">
						<span class="font-weight-bold mb-2">- CATALOG - </span>
					</li>

					{{ range $headers }}
						{{ $header := . }}
						{{ range first 1 (findRE $re $header 1) }}
							{{ range findRE $renum . 1 }}
								{{% bgstyle purple %}}{{ $next_heading := (cond (eq $h1_n 0) (sub (int .) 1 ) (int . ) ) }}{{% /bgstyle %}}
								{{ range seq $next_heading }}
									<ul class="nav">
								{{end}}
								{{ $anchorId :=  (replaceRE ".* id=\"(.*?)\".*" "$1" $header ) }}

										<li class="nav-item">
						 					<a class="nav-link" href="#{{ $anchorId }}">
												 {{ $header | plainify | htmlUnescape }}
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
          selector = {{% bgstyle purple %}}decodeURI{{% /bgstyle %}}(element.getAttribute('href')) || '';
        }

        try {
          return document.querySelector(selector) ? selector : null;
        } catch (err) {
          return null;
        }
      }
```



另一个是`scrollspy`中`_activate`函数。由于是需要根据`href`的内容获取selector，因此需要将`id`进行编码获得真实的`href`值。另外，为了能够与`href`正常中文的进行兼容，增加了判断。

```

_proto._activate = function _activate(target) {

        this._activeTarget = target;
        this._clear();
        var queries = this._selector.split(','); // eslint-disable-next-line arrow-body-style

        queries = queries.map(function (selector) {
            return selector + "[data-target=\"" + target + "\"]," + (selector + "[href=\"" + target + "\"]");
        });
        var $link = $$$1([].slice.call(document.querySelectorAll(queries.join(','))));
    
        {{% bgstyle purple %}}if ($link.length==0){{% /bgstyle %}} {
    
            queries = this._selector.split(',');
            queries = queries.map(function (selector) {
              return selector + "[data-target=\"" + target + "\"]," + (selector + "[href=\"" + {{% bgstyle purple %}} encodeURI(target).toLowerCase(){{% /bgstyle %}} + "\"]");
            });
            var $link = $$$1([].slice.call(document.querySelectorAll(queries.join(','))));   
        }
                                
        if ($link.hasClass(ClassName.DROPDOWN_ITEM)) {
            $link.closest( Selector.DROPDOWN ).find( Selector.DROPDOWN_TOGGLE ).addClass( ClassName.ACTIVE );
            $link.addClass(ClassName.ACTIVE);
        } else {
            // Set triggered link as active
            $link.addClass(ClassName.ACTIVE); // Set triggered links parents as active
            // With both <ul> and <nav> markup a parent is the previous sibling of any nav ancestor
            $link.parents(Selector.NAV_LIST_GROUP).prev(Selector.NAV_LINKS + ", " + Selector.LIST_ITEMS).addClass(ClassName.ACTIVE); // Handle special case when .nav-link is inside .nav-item
            $link.parents( Selector.NAV_LIST_GROUP ).prev( Selector.NAV_ITEMS ).children(  Selector.NAV_LINKS ).addClass( ClassName.ACTIVE );
        }
    
        $$$1(this._scrollElement).trigger(Event.ACTIVATE, {
            relatedTarget: target
        });
      };

```

### 解决监控位置问题

在某些情况下，明明被监控内容中显示的是A，但是TOC中激活的是A的上一个目录。这样就会出现，我点击了TOC中的目录A，内容滚动到了A，但是后来TOC中激活的是A的上一个目录。

仔细跟踪了一下bootstrap.js，发现一个问题：`offset`是使用函数`getBoundingClientRect()`获得的，是小数型，但是`scrollTop`是整型的，因此在边界点会出现问题。在Github/Issues上发现，还有其他小伙伴遇到了同样的问题，记录下我第一次[回答Issue](https://github.com/twbs/bootstrap/issues/23809#issuecomment-422284646)，希望能帮上~

解决方法就是在`_proto._process = function _process()`中修改`scrollTop`的数值：

```javascript
_proto._process = function _process() {
        var scrollTop = this._getScrollTop() + this._config.offset {{% bgstyle purple %}}  + 1{{% /bgstyle %}};

        var scrollHeight = this._getScrollHeight();

        var maxScroll = this._config.offset + scrollHeight - this._getOffsetHeight();

        if (this._scrollHeight !== scrollHeight) {
          this.refresh();
        }

        if (scrollTop >= maxScroll) {
          var target = this._targets[this._targets.length - 1];

          if (this._activeTarget !== target) {
            this._activate(target);
          }

          return;
        }

        if (this._activeTarget && scrollTop < this._offsets[0] && this._offsets[0] > 0) {
          this._activeTarget = null;

          this._clear();

          return;
        }

        var offsetLength = this._offsets.length;

        for (var i = offsetLength; i--;) {
          var isActiveTarget = this._activeTarget !== this._targets[i] && scrollTop >= this._offsets[i] && (typeof this._offsets[i + 1] === 'undefined' || scrollTop < this._offsets[i + 1]);

          if (isActiveTarget) {
            this._activate(this._targets[i]);
          }
        }
      };
```



### 标题太长怎么办

经常会出现这种情况，一行的标题太长，于是就换行显示。问题到不大，只是不太美观，还是希望能够只显示一行。有两种方案，一个是允许TOC有水平滚动条，另一个是将太长的标题省略显示。

#### 使用水平滚动条

这个只需要在css样式中标记对应的元素不能换行即可。

```css
.toc-nav .nav-link {
  white-space:nowrap;
} 
```

这样如果标题太长，在TOC会出现水平滚动条。

#### 太长标题省略显示

TOC模块出现水平滚动条总嫌有些冗余，于是决定将太长的标题省略显示，我选择使用这种方案。在css中增加以下内容：

```css
.toc-nav ul {
  overflow:hidden;
  white-space:nowrap;
}

.toc-nav .nav-link {
  text-overflow:ellipsis;
  overflow:hidden;
} 

```



## Resource资源链接汇总

[Hugo官网对TableOfContent介绍](https://gohugo.io/content-management/toc/)、[自建TOC参考](https://github.com/gohugoio/hugo/issues/1778)  

## 版本控制

| Version | Action           | Time       |
| ------- | ---------------- | ---------- |
| 1.0     | Init             | 2018-08-25 |
| 1.1     | 处理没有\<h1\>   | 2018-09-02 |
| 1.2     | 解决监控位置问题 | 2018-09-18 |
