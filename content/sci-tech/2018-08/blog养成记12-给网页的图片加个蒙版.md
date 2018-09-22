---
date: "2018-08-13T00:40:13+08:00"
publishdate: "2018-08-13+08:00"
lastmod: "2018-08-13+08:00"
draft: false
title: "Blog养成记(12) 给网页的图片加个蒙版"
tags: ["前端", "css", "blog"]
series: ["Blog养成记"]
categories: ["杂技浅尝"]
toc: true
---

上一期说到想给网站图标的图片加个蒙版，然而没有成功。那么网页中的图片么？经常看到个人图片都是圆形的，然而实际上上传的原图是方的。如果使用了蒙版功能，就可以对同一张图想怎么变形状就怎么变了。<!--more-->

首先，定义一个类，比如`avatar`，在css文件中定义该类使用了某长图片蒙版：

```html

.avatar {
  border-radius: 50%;
  box-shadow: 0px 2px 3px rgba(0, 0, 0, 0.2);
  max-width: 50px;
  {{% bgstyle purple %}}-webkit-mask-image: url(/img/mask/circle.svg);{{% /bgstyle %}}
  {{% bgstyle purple %}}mask-image: url(/img/mask/circle.svg);{{% /bgstyle %}}
}
```

其中高亮的两句定义了蒙版的图片。在这里，蒙版我使用的是font awesome里的圆形图，就长下面这个样子：

<img name="circle mask" src="/img/mask/circle.svg"  width='30px'/>

在实际使用时，只需要在`<img>`标签中使用该类即可，如：

```html
<img class="avatar" src="img/avatar.png" width="40" height="40" class="d-inline-block align-top" alt="" />
```

## 版本控制

| Version | Action | Time       |
| ------- | ------ | ---------- |
| 1.0     | Init   | 2018-08-13 |

