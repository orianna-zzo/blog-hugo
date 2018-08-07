---
date: "2018-01-07T11:05:25+08:00"
draft: false
title: "Blog养成记(2) Hugo+Docker在Github上建立Blog"
tags: ["hugo", "docker", "环境配置", "blog"]
series: ["Blog养成记"]
categories: ["杂技浅尝"]
toc: true

---


## Introduction

正如上一篇说的，我选择了[Hugo](https://gohugo.io)作为静态网页生成器。Hugo是一个用go写的静态网页生成器，它被提及最多的优点就是它生成网站的速度快。此外，Hugo的安装配置看上去也并不麻烦，直接在[这里](https://github.com/gohugoio/hugo/releases)选择合适的版本和环境下载对应release版并配置环境变量即可。整体来说非常方便。

在决定使用Hugo之外，我还决定用Docker来做环境配置。因为新买了mac，不愿意弄乱环境，也希望以后能够跨平台使用，方便配置，更重要的是，最近对docker感兴趣，想实践一下。[Docker](https://www.docker.com/)是一个开源的应用容器引擎，可以方便地将不同容器间的环境进行隔离，但又比虚拟机更轻量化，能更快速启动。与Docker相关的内容我会在另一个Docker系列进行详细说明，这里主要还是与建立Blog相关的使用为主。

网上使用Hugo写blog的内容很多，使用docker的也很多，但使用docker来搭建hugo编写环境并不多，我也是在一步步摸索中。那么，就跟着我一起开始尝试吧！

## Hugo的docker环境配置

### Docker安装

在Mac中可以使用Homebrew进行安装：

```shell
$ brew cask install docker
```

若是Windows或者其他操作系统，可以在[这里](https://www.docker.com/get-docker)选择你的操作系统下载相应版本进行安装并配置环境变量。

### 获得Hugo开发镜像

我在docker hub上查找了下，截止目前并没有官方镜像，都是用户自己建立并上传的镜像。[Hugo的Github](https://github.com/gohugoio/hugo)中的确有建立docker镜像的Dockerfile，但是我试了几次都未成功，最后决定建立自己的Hugo docker镜像，顺便学习下Dockerfile。

#### 直接获得镜像

如果只想获得开发镜像，可以选择从docker hub上下载个镜像，选择还挺多，欢迎下载我建立的docker镜像，在docker hub中只有33MB，只需要在终端中输入下面的命令即可：

```shell
$ docker pull orianna/hugo
```

该镜像可以在docker hub中找到，点[这里](https://hub.docker.com/r/orianna/hugo/)是在docker hub上的repo。

接下去在终端输入下面这行命令可以查看你现在有的镜像信息：

```shell
$ docker images
```

你可以发现`orianna/hugo`只有85.7MB大小.

#### 自建镜像

或者，你可以选择自己建立镜像。如果已经获得了Hugo镜像，可以略过这一部分。

我建立Hugo docker的Dockerfile放在[Github](https://github.com/orianna-zzo/hugo-docker-dev)上，大家可以去参考试试。现在是v0.46版，只有85.7MB大小的镜像，后续随着开发可能会有新的变化。

将所有内容clone到当前目录：

```shell
$ git clone https://github.com/orianna-zzo/hugo-docker-dev.git
```

打开Dockerfile，其中HUGO_VERSION是[Hugo](https://github.com/gohugoio/hugo)官方的发布版本，可以选择你需要的Hugo版本进行修改。在v0.3的Dockerfile中，定义了两个挂载文件夹，一个是`/hugo-site`用来挂载你的Hugo源码，另一个是`static-site`用来定义Hugo生成静态网页的输出文件夹。

{{% color light-grey %}}~~除了基础的下载Hugo执行文件和pygments高亮外，Dockerfile还定义了每次打开容器都会执行`start.sh`。该shell脚本只有一个作用，如果在当前文件夹中包含`run.sh`文件则执行该文件，若不存在则打开一个终端，在该终端内你可以自由尝试hugo命令。`run.sh`的作用主要是便于不用反复输入常用命令，可将常用命令直接写入其中保持注释状态，使用时只需要将要使用的命令保持正常状态即可。如果你clone了这个repository，在site-sample中包含了一个样例`run.sh`。~~(v2.0){{% /color %}} 

在这个repo的文件夹中打开终端，输入下面命令以建立镜像：

```shell
$ docker build -t orianna/hugo:0.46 -t orianna/hugo:latest .
```

其中`-t`给这个镜像打上tag。根据docker hub的要求，非官方镜像的镜像名称需要为`你的docker-name/镜像名称`，`:`后为该镜像的tag。`-t`可以有多个。注意不要忘记最后的`.`，它表示使用该文件夹中除了`.dockerignore` 中的其他内容作为建立镜像的上下文内容。

接下去你就可以在终端输入下面这行命令来查看镜像信息：

```shell
$ docker images
```

至此，你已经获得了Hugo的docker镜像了。

### 尝试使用Hugo

假设您clone了上文提到的内容，可以在该项目中使用下面命令启动容器来试用Hugo docker：

```shell
$ docker run --name my-hugo -v $(pwd)/site-sample:/hugo-site -v $(pwd)/public:/static-site -p 1313:1313 --rm -it orianna/hugo:latest
```

其中，`—name` 给该容器起了名字（可省略）；

`-v $(pwd)/site-sample:/hugo-site` 是将当前文件夹中的 `site-sample` 文件夹挂载到镜像中的 `/hugo-site` 文件夹（不可省略）；

`-v $(pwd)/public:static-site` 将当前文件夹中 `public` 文件夹挂载到镜像中的 `/static-site` 文件夹（可视情况省略）；

`-p` 是给出了镜像和宿主机之间的端口映射；

`—rm` 说明该容器结束后就自动销毁；

`-it` 表明打开一个可交互的终端。

此时，就可以用浏览器打[http://localhost:1313](http://localhost:1313)查看网页了。至此，基于Docker的Hugo开发环境就已经就绪了。

## 正式初建Blog

### Blog文件夹结构

在本地建立文件夹 `blog-hugo` 以存放Hugo源码，进入该文件夹，打开docker容器并将之挂载到Hugo的docker容器中。

```shell
$ docker run --name my-hugo -v $(pwd):/hugo-site -p 1313:1313 --rm -it orianna/hugo:latest COMMAND [-flag]
```

{{% color light-grey %}}~~此时，因为 `blog-hugo` 文件夹中并未有 `run.sh` ，所以会打开一个交互终端，并位于 `/hugo-site` 文件夹下。~~(v2.0){{% /color %}}

如执行`hugo new site.`，Hugo就会自动生成一个博客的目录结构，则需要输入：

```shell
$ docker run --name my-hugo -v $(pwd):/hugo-site -p 1313:1313 --rm -it orianna/hugo:latest new site .
```

Hugo的文件夹结构一般如下所示：

```
.
└── site
    ├── config.toml / config.yaml / config.json
    ├── content
    │   └── ...
    ├── layouts
    │   └── ...
    ├── themes
    │   └── ...
    ├── static
    │   └── ...
    ├── archetypes
    │   └── ...
    ├── data
    │   └── ...
    └── ...
```

其中，`config.toml` 是网站的配置文件，Hugo还可使用 `config.yaml` 或者 `config.json` 进行配置。

`content` 文件夹中存放所有的网站内容，可在此文件夹中建立其他子文件夹，即为子模块。

`layouts` 文件夹存放 `.html` 格式的模板。模板确定了静态网站渲染的样式。

`themes` 文件夹存放网站使用的theme主题模板。

`static` 文件夹存放未来网站使用的静态内容，比如图片、css、JavaScript等。当Hugo生成静态网站时，该文件夹中的所有内容会原封不动的被复制。

`archetypes` 文件夹存放网站预设置的文件模板头部，当使用 `hugo new` 时即可生成一个带有该头部的实例。

`data` 文件夹用来存储Hugo生成网站时应用的配置文件。配置文件可以是YAML，JSON或者TOML格式。

### 配置theme

可在[这里](https://themes.gohugo.io/)找自己喜欢的主题。我暂时选择简洁风的[cocoa](https://github.com/nishanths/cocoa-hugo-theme)，将主题clone到 `themes` 文件夹下：

```shell
$ git clone https://github.com/nishanths/cocoa-hugo-theme.git themes/cocoa
```

具体使用方式，可参照主题中 `exampleSite` 文件夹中 `config.toml` 配置网站的配置文件 `config.toml` ，需要在该文件中指出 `theme="cocoa"`。此外还可参考该文件夹 `content` 中的内容建立自己的博客，具体我就不在这里阐述了，可参考我的[Github](https://github.com/orianna-zzo/blog-hugo)。

在 `content` 中建立 `blog` 文件夹，包含一个 `_index.md` 文件，该文件仅包含一个文件头部。文件头部样例如下：

```
+++
date = "2018-01-06T02:15:26+08:00"
draft = false
title = "Blog"
+++
```

或者使用YAML风格头部：

```
---
date: "2018-01-06T02:15:26+08:00"
draft: false
title: "Blog"
---
```

其中，`date` 说明该博客建立时间，`draft` 说明这篇是否是草稿，若是草稿，在无特别指明情况下并不会生成静态网页，`title` 表明该文件显示的标题。

在同样文件夹下，建立其他 `.md` 文件，同样也是有相似的文件头部。该博客的文件名应和 `title` 一致，但要注意 `title` 中的空格或者 `+` 作为文件名时应该替换成`-`， 不然会报找不到404网页。文件内容在这块区域下面，使用markdown语法。

### 查看网页

{{% color light-grey %}}~~若文件夹中不存在 `run.sh` 文件，执行容器后执行：~~(v2.0) {{% /color %}}

通过`hugo server -b http://localhost:1313 --bind=0.0.0.0`来即使查看静态网页情况：

```shell
$ docker run --name my-hugo -v $(pwd):/hugo-site -p 1313:1313 --rm -it orianna/hugo:latest server -b http://localhost:1313 --bind=0.0.0.0
```

即可在[http://localhost:1313](http://localhost:1313 )查看你编写的网页了。

这行命令也可放在 `run.sh` 中以复用，每次打开容器即执行该命令。

### 生成静态页面

{{% color light-grey %}}~~若文件夹中不存在 `run.sh` 文件，执行容器后执行：~~(v2.0) {{% /color %}}

通过`hugo --baseUrl="https://orianna-zzo.github.io/"`来生成静态网页，需要明确静态网页url：

```shell
$ docker run --name my-hugo -v $(pwd):/hugo-site -p 1313:1313 --rm -it orianna/hugo:latest --baseUrl="https://orianna-zzo.github.io/" 
```

将上述命令中网站的url替换成你网站的url。该命令会在文件夹中生成 `public` 文件夹，并将静态网页生成在该文件夹中。

在v0.3版的Hugo镜像之后中，可以将想要将镜像网站生成的文件夹挂载在容器中，可用下面命令执行容器：

```shell
$ docker run --name my-hugo -v $(pwd):/hugo-site -v ~/Documents/WorkingProject/orianna-zzo.github.io:/static-site -p 1313:1313 --rm -it orianna/hugo:latest --baseUrl="https://orianna-zzo.github.io/" 
```

可将上面 `~/Documents/WorkingProject/orianna-zzo.github.io` 的路径替换成自己的文件夹。

然后在执行容器后执行下面命令，则会将静态网页生成到指定文件夹中：

```shell
$ docker run --name my-hugo -v $(pwd):/hugo-site -p 1313:1313 --rm -it orianna/hugo:latest --baseUrl="https://orianna-zzo.github.io/" -d /static-site
```

若要部署在Github Pages上，需要将生成的静态网页push到Github上。

鉴于我嫌上面生成挂载静态网页的文件夹的容器命令太过冗长，因此决定在该文件中建立一个指向 `~/Documents/WorkingProject/orianna-zzo.github.io` 的软连接。

```shell
$ ln -s ~/Documents/WorkingProject/orianna-zzo.github.io public
```

软连接建立后，只需要用下面命令生成容器即可，感觉如此生成静态网页方便很多：

```shell
$ docker run --name my-hugo -v $(pwd):/hugo-site -v $(pwd)/public:/static-site -p 1313:1313 --rm -it orianna/hugo:latest --baseUrl="https://orianna-zzo.github.io/" -d /static-site
```

### 配置alias简化命令

每次都需要翻blog找上述命令复制到terminal中总是有些繁琐，于是决定给上述命令配置别名。

配置需要写到配置文件，有两个选择：

* 对全部用户生效：修改 `/etc/profile`文件
* 对当前用户生效：修改 `~/.profile`文件 （仅针对Mac，linux大多数情况下修改 `~/.bashrc`）

我将用户自定义部分还是放入了`~/.profile`文件：  

```shell
$ vi ~/.profile
```

增加以下内容，给起个别称 `hugo`，这样就可以将`hugo`命令当做本地命令使用了：

```shell
# config alias
alias hugo="docker run -v \$(pwd):/hugo-site -v \$(pwd)/public:/static-site -p 1313:1313 --rm -it orianna/hugo:latest"
```

注意，该命令需要注意执行命令所在的位置，且静态页面生成的文件夹就为`./public`。

记得source一下，使配置文件立即生效：

```shell
$ source ~/.profile
```

现在开始就可以直接使用 `hugo` 来启动hugo的docker镜像了。

除了以上简化hugo内容之外，还可以有以下别名配置：

```shell
# 查看博客的页面生成情况(绝对路径，所以不限执行该命令位置)
alias hugo-dev='docker run --name my-hugo -v /Users/orianna/Projects/homepage/blog-hugo:/hugo-site -v /Users/orianna/Projects/homepage/blog-hugo/public:/static-site -p 1313:1313 --rm -it orianna/hugo:latest server -b http://localhost:1313 --bind=0.0.0.0 --disableFastRender'

# 生成博客的静态页面(绝对路径，所以不限执行该命令位置)
alias hugo-dev-gen='docker run --name my-hugo -v /Users/orianna/Projects/homepage/blog-hugo:/hugo-site -v /Users/orianna/Projects/homepage/blog-hugo/public:/static-site -p 1313:1313 --rm -it orianna/hugo:latest --baseUrl="https://orianna-zzo.github.io/" -d /static-site'

# 可将常用的命令写在执行的文件夹中的run.sh中，镜像直接执行(绝对路径，所以不限执行该命令位置)
alias hugo-dev-run='docker run --name my-hugo -v /Users/orianna/Projects/homepage/blog-hugo:/hugo-site -v /Users/orianna/Projects/homepage/blog-hugo/public:/static-site -p 1313:1313 --rm -it --entrypoint sh orianna/hugo-docker-dev:latest run.sh'

# 可将常用的命令写在执行的文件夹中的run.sh中，镜像直接执行(注意执行命令的路径)
alias hugo-run='docker run -v $(pwd):/hugo-site -v $(pwd)/public:/static-site -p 1313:1313 --rm -it orianna/hugo-docker-dev:latest'

```

这样我最常用的就是`hugo-dev`和`hugo-dev-gen`了

## Github Pages部署

参考[这里](https://help.github.com/articles/user-organization-and-project-pages/)，在Github Pages有四种类型，而对于非组织型用户来说有两种，一种是用户的个人网站，网页域名为 `username.github.io`，另一种为Project的主页，网页域名为 `username.github.io/projectname`。Github Pages对于Project主页的源码要求有了修改，现在也可以放置在master上，之前版本中必须放在`gh-pages` 分支上，不过这里暂且不提，主要还是关心用户个人主页。

这就需要你在Github上建立一个以 `username.github.io` 为名称的repository，对于我来说就是 `orianna-zzo.github.io`。此外，需要将Hugo生成的所有静态网页push到这个repository的master分支上。现在就可以用这个域名打开个人网站了。


## Resource资源链接汇总

我建立的docker for Hugo开发镜像:  [Docker Hub上的repo](https://hub.docker.com/r/orianna/hugo/)、[Github上的repo](https://github.com/orianna-zzo/hugo-docker-dev)。  
我的个人主页Hugo代码:  [blog-hugo](https://github.com/orianna-zzo/blog-hugo)  

[Hugo官网](https://gohugo.io)、[Hugo release版下载](https://github.com/gohugoio/hugo/releases)  
[Docker官网](https://www.docker.com/)、[Docker下载](https://www.docker.com/get-docker)  
[Github Pages个人/项目主页设置](https://help.github.com/articles/user-organization-and-project-pages/)

## 版本控制

| Version | Action                   | Time       |
| ------- | ------------------------ | ---------- |
| 1.0     | Init                     | 2018-01-07 |
| 1.1     | 增加tag、版本控制及资源链接 | 2018-01-17 |
| 1.2     | Config alias             | 2018-03-21 |
| 1.3 | hugo-docker-dev v0.46 + 修改alias用法 | 2018-08-03 |
| 2.0 | hugo修改为ENTRYPOINT + 修改alias用法 | 2018-08-05 |
