---
date: "2018-02-14T01:49:13+08:00"
draft: true
title: "Mac小白探索记(2) 我的Mac入门设置"
tags: ["mac", "软件安装"]
series: ["Mac小白探索记"]
categories: ["杂技浅尝"]
toc: true
---

### Finder配置

#### 显示隐藏文件夹

Mac系统的 `/usr`、`/etc`等文件夹都是隐藏文件，如果不进行设置用户是无法见到的。估计是因为mac用户并不是所有人都对linux的操作十分熟悉，所以把这些对于系统十分重要的文件夹都进行隐藏了，免得用户误删等操作把系统玩坏。

在terminal中输入下面命令：

```

```

```
$ defaults write com.apple.finder AppleShowAllFiles -bool true
```

然后重启Finder，在terminal 中输入：

```

```

```
$ killall Finder
```

当当当当，隐藏的文件夹就显示出来了！

#### 显示工具栏

选择 ⌈显示⌋ > ⌈显示标签页栏⌋、⌈显示路径栏⌋、⌈显示边栏⌋、⌈显示预览⌋。

<img name="Finder-view" src="/images/blog/2018-02/Finder-view.png" width='300px'/>

对工具栏自定义：⌈显示⌋ > ⌈自定义工具栏⌋，增加⌈新建文件夹⌋，或者还可以添加其他想要添加工具栏。

<img name="Finder-toolbox" src="/images/blog/2018-02/Finder-toolbox.png" width='500px'/>

#### Finder文件夹快速打开Terminal

其实这部分应该算是效率工具，但是更像是针对Finder路径这一问题的解决方案，因此勉强放在这里了。

其实还可以打开Terminal打上`cd` 之后直接把文件夹拖进去，就会显示文件夹的路径，但是这样十分繁琐。

这里提供两个方案。方案一：设置快捷键，但是只能在上层文件夹中选择需要的路径的文件夹后才能打开Terminal。方案二：安装Go2Shell，可以在Finder的Toolbox中安装一个插件，非常方便。

##### 方案一：设置快捷键

⌈系统偏好设置⌋ > ⌈键盘⌋ > ⌈快捷键⌋ > ⌈服务⌋ > ⌈新建位于文件夹位置的终端窗口⌋。

<img name="open-terminal" src="/images/blog/2018-02/open-terminal.png" width='500px'/>

这样在Finder中选中文件夹，双指右击，就可以在服务中看到打开终端的选项。

<img name="service-open-terminal" src="/images/blog/2018-02/service-open-terminal.png" width='500px'/>

还可以设置快捷键，我设置了 `⌘⌥⌃T`，选中文件夹后使用快捷键后即可快速打开终端。

##### 方案二：安装Go2Shell

<img name="Go2Shell" src="/images/blog/2018-02/Go2Shell.png" width='100px'/>

不要通过App Store安装，无法使用，直接通过Homebrew安装：



```
$ brew cask install go2shell
```

安装后打开，点击⌈Install Go2Shell to Finder⌋

<img name="go2shell-finder" src="/images/blog/2018-02/go2shell-finder.png" width='300px'/>

然后就可以在Finder的工具栏发现Go2Shell的图标了。点击这个图标就会打开一个在当前文件夹的Terminal，很方便。

![go2shell-toolbox](/images/blog/2018-02/go2shell-toolbox.png)

#### Finder复制文件夹路径

直接快捷键 `⌘⌥C` 即可。

### 字符设置

⌈系统偏好设置⌋ > ⌈键盘⌋ > ⌈键盘⌋ > 勾选⌈在menu bar显示键盘及emoji显示器⌋

<img name="show-emoji-view" src="/images/blog/2018-02/show-emoji-view.png" width='500px'/>

然后就可以点击menu bar上的输入法图标，点击⌈显示表情与符号⌋

<img name="open-emoji-view" src="/images/blog/2018-02/open-emoji-view.png" width='300px' />

按照下图，先点击左上角的⌈齿轮⌋符号打开设置，选择⌈自定列表⌋，选择⌈⌘技术符号⌋

<img name="show-tech-emoji" src="/images/blog/2018-02/show-tech-emoji.png" />

如果嫌打开⌈表情与符号⌋太过繁琐，可以设置文本快捷键。

⌈系统偏好设置⌋ > 键盘 > 文本，通过⌈+⌋增加文本快捷键

<img name="sig-shortcut" src="/images/blog/2018-02/sig-shortcut.png" />

### Touchbar设置

⌈系统偏好设置⌋ > ⌈键盘⌋ > ⌈键盘⌋ > 点击⌈自定义控制条⌋ 即可进行设置。

此外有很多推荐的BetterTouchTool也可以，不过这款软件收费。

### 常用快捷键

这里罗列下常用的快捷键。部分可能涉及之后安装的软件和设置。特别针对软件的就不在这里罗列。

快捷键可在⌈系统偏好设置⌋ > ⌈键盘⌋ > ⌈快捷键⌋中进行设置，若 ⌈服务⌋中不存在的，可通过Automator添加服务后添加快捷键 。

下表中无特殊说明是默认设置。

**全局快捷键**

| 快捷方式 | 动作                                               |
| -------- | -------------------------------------------------- |
| ⌃␣       | 切换输入法                                         |
| ⌘,       | 打开spotlight搜索                                  |
| ⌘⌥F      | 选中窗口最大化（安装Spectacle默认设置）            |
| ⌘⌥←/⌘⌥→  | 选中窗口放在左半边/右半边（安装Spectacle默认设置） |
| ⌘长按    | 查看快捷键（安装Cheatsheet）                       |

**大多数软件通用快捷键**

| 快捷方式 | 动作      |
| -------- | --------- |
| ⌘C/⌘V    | 复制/粘贴 |
| ⌘N       | 新建      |
| ⌘S       | 保存      |

## Resource资源链接汇总

[Homebrew官网](https://brew.sh/)、[Cask官网](http://caskroom.github.io/)

## 版本控制

| Version | Action                   | Time       |
| ------- | ------------------------ | ---------- |
| 1.0     | Init                     | 2018-01-30 |
| 1.1     | Finder打开终端、复制路径 | 2018-02-14 |