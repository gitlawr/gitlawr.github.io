---
published: true
title: 配置iTerm终端使用随机图片背景
layout: post
comments: true
author: Lawrence 
category: articles

tags:
- terminal
- iTerm
- trick
- script
- automation
- 脚本
- 终端
- 自动化
- 原创
---


Chrome有个叫Momentum的插件是我比较喜欢用的，其中一点是它在打开新标签页时，每天都有一张新的地理图片显示。那么问题来了，终端是平时工作中经常使用和面对的界面，能不能在终端实现类似效果提高日常工作的体验（以及demo时的逼格）？

**目标**

每次创建一个新的终端Tab的时候，有随机的背景图片，效果如下

![Sample](https://github.com/gitlawr/gitlawr.github.io/raw/master/images/2018-07-27/demo.gif)

**事前准备** :

1. Mac OS 环境
2. 安装iTerm2

iTerm2 是个在Mac OS环境下可以取代原生终端的利器，它的安装和使用就不在这里赘述了，具体可以访问它的[官网](https://www.iterm2.com/)或者搜索引擎查下。

**配置步骤** :

iTerm的配置很丰富，但是像我们这样的需求自然是不能直接配出来的。但我们可以利用它的脚本实现扩展和自动化，具体步骤如下：

1. 首先进入iTerm的Preferences，选中Profiles

2. 选择你用到的Profile，在右边的Profile详细配置中点击Window标签

	![window-conf](https://github.com/gitlawr/gitlawr.github.io/raw/master/images/2018-07-27/window-conf.jpg)

3. 我们要用到的就是这里的修改背景图片的设置，但是这里的设置是作用于所有标签的，不能达到我们的目的。我们先在这里调整Blending设置。这可以改变背景图片显示效果的深浅。我们自然不希望背景会喧宾夺主影响我们查看终端的输入输出信息。可以先任意配一张背景图片，调整完blending值再去掉勾选。像下图这样能看清楚终端文字：

	![bg-test](https://github.com/gitlawr/gitlawr.github.io/raw/master/images/2018-07-27/bg-test.jpg)

4. 实现创建tab时更新背景图片的脚本，保存在任意路径下，如`/Users/abc/NewTabWithBg.scpt`。
```applescript
tell application "iTerm"
    tell current window
        create tab with default profile
    end tell
    tell current session of current window
        set background image to "/tmp/rdimg.jpg"
    end tell
end tell
do shell script "/usr/local/bin/wget -O /tmp/tmprdimg.jpg https://picsum.photos/1400/800?random && cp /tmp/tmprdimg.jpg /tmp/rdimg.jpg"
```
上面使用了AppleScript，关于iTerm对AppleScript的支持，可以参考[此处](https://www.iterm2.com/documentation-scripting.html)。
这里做的，就是创建一个新的iTerm标签，并且设置它的背景图片为`/tmp/rdimg.jpg`。由于从网络下载图片会有延时，所以选择在建完标签页之后，再更新和替换一张图片。这样每次打开新标签页都是新图片。一个缺点是第一次打开的时候由于`/tmp/rdimg.jpg`不存在，没有背景图。另一点是在离线环境，或者连续新建多个标签页且新图片还没下载完，使用的还是先前的图片。当然你也可以选择在本地存一批图片作为图源来避免延时和网络要求，你可以权衡利弊按需调整脚本。

5. 设置快捷键来使用上述脚本。在iTerm Preferences - Keys 中，点击右下方的`+`按钮，添加热键。

	![set-key](https://github.com/gitlawr/gitlawr.github.io/raw/master/images/2018-07-27/set-key.png)

6. 点击 Keyboard Shortcut，然后键盘输入`⌘+t`，即默认的新建标签快捷键。在Action列表中，选择Run Coprocess。 在底下的命令输入框中输入`osascript "/Users/abc/NewTabWithBg.scpt"`。此处的路径按上述脚本保存的路径进行配置。最后点击Ok进行保存。

7. 完成！接下来就可以打开iTerm，用`⌘+t`创建标签页，来体验效果。

**备注**

关于图源的选择，除了上述的picsum.photos，还有不少网站可以提供随机图片获取的服务，比如：

  - http://placeimg.com/1400/800/any
  - https://source.unsplash.com/random/1400x800

Unsplash 还可以按某个collection，某个用户喜好，以及某些关键字进行图片获取。
我期待地搓了搓手，然后把脚本中的图源URL改成：
```
https://source.unsplash.com/featured/1400x800?lady,beauty
```

建个Tab看看

![set-key](https://github.com/gitlawr/gitlawr.github.io/raw/master/images/2018-07-27/lady.png)

# ？？？？？？