---
date: "2018-01-18T09:14:15+08:00"
draft: false
title: "在公司建立python虚拟环境"
tags: ["python", "conda", "环境配置", "公司"]
series: []
categories: ["杂技浅尝"]
toc: true

---


## 前言

尽管官方已经声明python2.7在2020年就不再进行维护，但很多企业应用和第三方包还是建立在python2.7的版本上，进行切换有一定成本。而相比python3，python2对于中文编码处理也相对繁琐的多，因此新的应用一般都会建立在python3上。而python3的版本选择也有分歧，比如说，尽管截止目前为止最新的版本是python3.6，但是链接oracle的包cx_Oracle只在python3.5上成功链接，python3.6上尽管能够安装，但是在实际使用中却无法成功读数。除了python主版本外，所依赖的第三方包版本在不同的应用上可能也会不同。

复杂的版本问题可能在个人开发时并不会有很大影响 (除非有强迫症需要在一个干净的开发环境)，但是当多人需要在同一个开发机/测试机/生产机上跑应用或者模型时，对于各个环境依赖的隔离就急需找到解决方案。依赖隔离问题其实不仅仅在python环境中十分重要，在很多其他程序中也是如此，不过本文还是主要以python环境为主要出发点。

## python环境隔离方案

经过网上搜索，目前为止对于python的依赖隔离主要有以下几种主要方法：

* docker
* virtualenv
* venv
* conda

### 方案选择

#### docker

其实docker很不错。容器化是现在非常火的一个方案。docker容器崩溃也不会影响主机，而且环境镜像可移植性非常强，能非常方便地把环境移植到不同的主机上去而不需要重新配置安装。现在我自己电脑也尽量在docker中开发，希望能不扰乱电脑本身的环境。可惜的是，尽管网上有信息说新版的docker容器使用可以不使用root权限，也有教程说建立一个可以使用docker的用户组，但是docker的安装还是避免不了需要root权限，而对于公司的环境来说，主机系统环境版本较旧且需要运营安装配置，灵活度不够。（插一句吐槽，其实对于正常有运营的组来说非常正常，但现在新到的组十分不规范，虽然不使用生产数据库但开发机都放在生产环境，其他人都不知道怎么找人root安装软件。）所以在公司使用的场景下只能忍痛放弃。如果没有这个限制，我还是很推荐这个方案，具体使用方法可以参考我另外的docker系列文章。

#### virtualenv vs venv

virtualenv是一个针对python建立定制的虚拟环境的工具，可以在虚拟环境中指定python版本并使用pip安装到激活的虚拟环境中。在使用virtualenv时，虚拟环境会依赖系统环境中的site-packages，可以添加 `—no-site-packages` 表明虚拟环境不依赖这些包建立一个干净的环境。默认情况下，virtualenv并不会复制一个环境，而是建立一个软连接到现有环境，因此若是需要完全独立的环境，需要添加 `—always-copy ` 来说明。virtualenv存在时间已经很长了，网上有很多相关使用方法。

venv在python3.3之后集成在python标准库中。在python3.4之后可以直接使用venv，而较早版本在venv外包了一层pyvenv，所以很多地方都会提及pyvenv而不是venv，但建议如果允许还是直接使用venv。（注意，pyvenv与pyenv并不同，不要混淆。）尽管一些细节上还是有所区别，venv的实现很大程度上基于virtualenv，因此如果使用python3.3之后的版本，完全可以使用venv替代virtualenv。当然如果你乐意还是依旧可以再装个virtualenv的。

#### virtualenv vs conda

venv相关信息比较少，但因为virtualenv与之相似度很大，就用virtualenv与conda进行比较了。

首先非常重要的一点，venv也好，virtualenv也好都只针对python，也就是无法用在其他语言环境，而conda并不局限于python，它可以管理任何其他语言。我找到一篇关于澄清对conda误解的[博文](https://jakevdp.github.io/blog/2016/08/25/conda-myths-and-misconceptions/)，看完就能对conda有个大致的认识。其中，第5条说明了作者认为conda相比于virtualenv/venv的优点，之后还给出了virtualenv与conda如何结合使用。对我来说最重要的是：conda环境完全隔离，连执行路径都不一致，还有很重要的一点，conda虚拟环境也方便迁移，可以在有外网的电脑生成后打包上传到无网的服务器上使用。这一点就基本决定了要使用conda建立python开发环境。

此外，针对python，[Anaconda的文档](https://docs.anaconda.com/_downloads/conda-pip-virtualenv-translator.html)中对conda、pip和virtualenv进行了简要的比较，其中点明了pip是个包管理器、virtualenv是环境管理器，而conda两者兼顾，还可以升级python核心程序。这个很好地说明了conda的全能。具体对比如下：

| Task                                 | Conda package and environment manager command | Pip package manager command              | Virtualenv environment manager command   |
| ------------------------------------ | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Install a package                    | `conda install $PACKAGE_NAME`            | `pip install $PACKAGE_NAME`              | X                                        |
| Update a package                     | `conda update --name $ENVIRONMENT_NAME$PACKAGE_NAME` | `pip install --upgrade $PACKAGE_NAME`    | X                                        |
| Update package manager               | `conda update conda`                     | Linux/OSX： `pip install -U pip` Win： `python -m pipinstall -U pip` | X                                        |
| Uninstall a package                  | `conda remove --name $ENVIRONMENT_NAME$PACKAGE_NAME` | `pip uninstall $PACKAGE_NAME`            | X                                        |
| Create an environment                | `conda create --name $ENVIRONMENT_NAMEpython` | X                                        | `cd $ENV_BASE_DIR; virtualenv$ENVIRONMENT_NAME` |
| Activate an environment              | `source activate $ENVIRONMENT_NAME`      | X                                        | `source$ENV_BASE_DIR/$ENVIRONMENT_NAME/bin/activate` |
| Deactivate an environment            | `source deactivate`                      | X                                        | `deactivate`                             |
| Search available packages            | `conda search $SEARCH_TERM`              | `pip search $SEARCH_TERM`                | X                                        |
| Install package from specific source | `conda install --channel $URL$PACKAGE_NAME` | `pip install --index-url $URL $PACKAGE_NAME` | X                                        |
| List installed packages              | `conda list --name $ENVIRONMENT_NAME`    | `pip list`                               | X                                        |
| Create requirements file             | `conda list --export`                    | `pip freeze`                             | X                                        |
| List all environments                | `conda info --envs`                      | X                                        | Install virtualenv wrapper, then `lsvirtualenv` |
| Install other package manager        | `conda install pip`                      | `pip install conda`                      | X                                        |
| Install Python                       | `conda install python=x.x`               | X                                        | X                                        |
| Update Python                        | `conda update python`                    | X                                        | X                                        |

## conda使用

### 安装conda

一般如果通过Anaconda安装的python，conda就已经默认安装好了。

也可以在[github的repo](https://github.com/conda/conda)上下载发行版的源码仅安装conda及其依赖 (称之为Miniconda)。源码下载后解压缩，进入文件夹后在终端输入： 

```shell
$ python setup.py install
```

或者在[这里](https://conda.io/miniconda.html)下载对应版本的bash文件，在终端执行：

```shell
$ bash Miniconda_file_name.sh
```

>    这部分不确定：
>    
>    网上有介绍说安装完成后，conda下的bin文件会添加到环境变量里面，这时候需要source一下bash文件：
>    
>    ```shell
$ source ~/.bashrc
```



安装完成后可以通过下面的命令查看conda版本以及进行conda版本更新：

```shell
$ conda -V
$ conda update conda
```

### python项目虚拟环境使用

#### 查看、创建、移除虚拟环境

可通过在终端输入下面命令，查看已经存在的虚拟环境：

```shell
$ conda env list
```

若需要建立一个使用pythonx.x版本建立的虚拟环境，虚拟环境名为envname：

```shell
$ conda create -n envname python=x.x [anaconda]
```

这回在你anaconda目录下 `envs/envname` 中安装对应版本的python及相关的[anaconda]包 (可省略或者换成其他包)。如果需要复制现在系统python的环境可以用下面的命令：

```shell
$ conda create -n envname --clone root
```

若是这个虚拟环境envname你不再需要，想要移除整个环境，可以通过下面命令进行移除：

```shell
$ conda remove -n envname --all
```

#### 激活/失效conda虚拟环境

若要激活或者说切换到你建立的虚拟环境envname，需要在终端键入下面命令：

```shell
$ # Linux
$ source activate envname
$
$ # Windows
$ activate envname
```

激活后，终端的当前目录前会显示你的虚拟环境名称。此时执行的就是虚拟环境envname中的包依赖，这样就可以开始使用啦。可以用 `which python` 查看现在使用的python执行程序，就能发现已经不是使用系统路径下的python了。

使用结束后，可以通过下面命令来关闭当前环境：

```shell
Linux: $ source deactivate
Windows: $ deactivate
```

#### conda虚拟环境包管理

添加包：

```shell
$ conda install -n envname [package]
```

如果没有 `-n envname`，则会安装到系统的python目录里。

查看已安装的包：

```shell
$ conda list
```

移除包：

```shell
$ conda remove --name envname package
```

查找需要安装的包，比如说tensorflow：
```shell
$ conda search tensorflow
```

在实际使用中发现conda search得到的版本不够新，因此在激活虚拟环境后使用 `pip install` 或者`python setup.py install` 来安装包。

## 实施流程

1. 自己电脑建立与服务器系统一致的docker环境镜像，以mint18为例：

	用以下dockerfile建立以下镜像：

	```
	FROM vcatechnology/base-linux-mint
	MAINTAINER Zi'ou Zheng <zhengziou@gmail.com>
	
	# Mount volume to host
	VOLUME /output
	CMD ["/bin/bash"]
	```
	
	若为其他系统，base image最好为official image。
	
	镜像建立：
	```shell
	$ docker build -t orianna/mint:18 .
	```

2. 新建容器并配置环境

	在当前文件夹新建文件夹python3.6来挂在在容器上，来与容器进行数据交流。网上下载需要的环境包，例如Anaconda3，并放在python3.6文件中。
新建容器my-mint:

	```shell
	$ docker run --name "my-mint" -v $(pwd)/python3.6:/output -it orianna/mint:18
	```

	进入 `/output` 后，根据指示安装Anaconda3:
	```shell
	$ sh Anaconda3-5.0.1-Linux-x86_64.sh
	```

	查看python是否安装完成:
	```shell
	$ python
	```

	如果显示的不是 `Python 3.6.3 |Anaconda, Inc.|`，则还需要输入以下命令 (具体路径需要参考安装Anaconda3时给出的提示)，使 `.bashrc`中能正常使用
	```shell
	$ source /root/.bashrc
	```

	新建conda虚拟环境 `py3.6-tf`，虚拟环境以现有python3环境作为基础:
	```shell
	$ conda create -n py3.6-tf --clone root
	```

	在虚拟环境中安装其他需要的包:
	```shell
	$ # 进入虚拟环境
	$ source acivate py3.6-tf
	(py3.6-tf)$ # 安装其他包
	(py3.6-tf)$ pip install tensorflow
	(py3.6-tf)$ pip install jieba
	(py3.6-tf)$ pip install gensim
	(py3.6-tf)$ pip install tqdm
	```

	安装完成后进行查看:
	```shell
	(py3.6-tf)$ python
	(py3.6-tf)> import tensorflow
	(py3.6-tf)> import jieba
	(py3.6-tf)> import gensim
	(py3.6-tf)> import tqdm

	```

	如果能够正常引入，则安装成功。
	可以退出python和虚拟环境:
	```shell
	(py3.6-tf)> # 退出python
	(py3.6-tf)> exit()
	(py3.6-tf)$ # 退出虚拟环境
	(py3.6-tf)$ source deactivate
	```
	
	退出该docker镜像:
	```shell
	$ exit
	```
	
3. 更新镜像，并将新建的虚拟环境导出
	更新镜像，并删除已有容器:
	```shell
	$ # 更新镜像
	$ docker commit my-mint orianna/mint:18
	$ # 删除该容器
	$ docker rm my-mint
	```
	
	新打开一个容器，这个容器关闭后会自动删除:
	```shell
	$ docker run -v $(pwd)/python3.6:/output --rm -it orianna/mint:18
	```
	
	进入 `Anaconda3/envs` 文件夹，并对py3.6-tf进行打包:
	```shell
	$ cd /root/Anaconda3/envs
	$ tar -czxf py3.6-tf_mint.tar.gzip py3.6-tf
	```
	
	将压缩文件移到output文件夹，可在宿主机中直接访问:
	```shell
	$ mv py3.6-tf_mint.tar.gzip /output/
	```

	之后只需要将该压缩文件在服务器对应的 `Anaconda/envs` 中解压缩即可。

## Resource资源链接汇总

[conda github repo](https://github.com/conda/conda)、[conda用户手册](https://conda.io/)

## 版本控制

| Version | Action     | Time       |
| ------- | ---------- | ---------- |
| 1.0     | Init       | 2018-01-18 |
| 1.1     | 增加公司流程 | 2018-01-19 |
| 1.2     | fix quote bug | 2018-03-20 |
