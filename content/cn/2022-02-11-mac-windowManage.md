---
title: 高效Mac学术指南-窗口管理篇
author: Tintingo
date: '2022-02-11'
slug: mac-window-manage
show_toc: true
toc_depth: 3
categories:
  - MacOS
tags:
  - MacOS
  - 效率工具
---

## 前言
这篇文章是本人对于 MacOS 的键盘流窗口管理的一些总结和心得。由于很早之前使用 Linux 的 i3 等平铺式桌面的经历，我对于窗口以及桌面的键盘控制流一直很喜欢。因此，自从换了mac以后，我一直在寻找 MacOS 上键盘流桌面管理的方法，最近这段时间觉得逐渐形成了一套稳定的流程，遂写文记录。

MacOS 中平铺式桌面的替代方案我选择的是 [yabai](https://github.com/koekeishiya/yabai)，yabai 中快捷键操作是使用 [skhd](https://github.com/koekeishiya/skhd)实现的，因此我们需要同时安装yabai以及skhd，安装方法就不再赘述了，可以查看项目的 github。另外有关 yabai 的更多说明参见[官方wiki](https://github.com/koekeishiya/yabai/wiki/)。

值得一提的是，安装完成以后想要实现开机启动以及后台运行，需要将它们加入到 `brew` 的 `service` 中：
```shell
brew services start yabai
brew services start skhd
# 停止运行
brew services stop yabai
brew services stop skhd
```
yabai 和 skhd 都是需要我们使用配置文件进行配置的，配置文件的默认位置为: `~/.yabairc`以及`~/.skhdrc`。修改了 `~/.skhdrc` 之后配置文件可立刻生效，而修改了 `~/.yabairc` 以后需要重启 yabai 方能生效，因此在 `~/.skhdrc` 中加入以下命令，可以使用快捷键重启 yabai。
```shell
# Restart Yabai
ctrl + cmd + alt - r :
    /usr/bin/env osascript <<< \
        "display notification \"Restarting Yabai\" with title \"Yabai\""; \
    launchctl kickstart -k "gui/${UID}/homebrew.mxcl.yabai"
```
> 我在刚开始使用 yabai 的时候，看了网上其他的文章推荐后，也对 [simple-bar](https://github.com/Jean-Tinland/simple-bar) 和 [stack-line](https://github.com/AdamWagner/stackline) 作了尝试，用了很长时间以后觉得实际用处并不大，现在已经弃用了，因此不作讲解。

### 浮动式桌面 or 平铺式桌面？
yabai 中默认的是平铺式布局，如果不习惯使用平铺式布局，可以改为浮动式布局（大多数用户习惯的方式）。本篇文章接下来将对以平铺式布局的配置方式进行说明。
```shell
yabai -m config layout                       bsp #默认使用平铺式布局
# yabai -m config layout                       float #默认使用浮动式布局
# 对特定的space指定布局方式
yabai -m config --space 2 layout float
```
## 窗口的美化
### 半透明效果的实现
具有半透明效果的窗口可能在视觉上给人一种更美观的感觉。幸运的是在 yabai 中我们可以使用很简单的方式实现窗口的半透明效果，而且可以为聚焦窗口和非聚焦窗口设置不同的透明度，以起到简单区分聚焦窗口的效果。在 `~/.yabairc` 中进行如下设置：
```shell
# window opacity (default: off)
yabai -m config window_opacity on
yabai -m config active_window_opacity 0.95  #为聚焦窗口设置95%的透明度
yabai -m config normal_window_opacity 0.9   #为非聚焦窗口设置90%的透明度
```
利用 skhd 可以使用快捷键快速为当前聚焦窗口切换透明度，比如在 `~/.skhdrc` 中的如下实现：
```shell
# toggle window transparency
ctrl + alt - p : yabai -m window --opacity 1.0
ctrl + alt - l : yabai -m window --opacity 0.95
```
### 窗口边框(border)的实现
在我截图中大家可以发现在窗口的周围有一圈深红色边框，这是 yabai 自带的 border。我们可以自己配置聚焦窗口的边框颜色与非聚焦窗口的边框颜色，这样就可以对聚焦窗口进行非常明显的区分了。在 `~/.yabairc` 中进行如下配置：
```shell
# ====== Border settings =======================
yabai -m config window_border                on
yabai -m config window_border_width          6
# red or gray
yabai -m config active_window_border_color   0xfac23541
yabai -m config normal_window_border_color   0x7f6123A
yabai -m config insert_window_border_color   0xffCF515C
```
在实际使用时，发现有一个 bug 就是在移动窗口时有时会出现 border 不随之移动的情况，非常影响体验，这时可以使用快捷键取消窗口的边框，再打开边框即可。在 `~/.skhdrc` 中设置 `alt b` 对边框进行打开或关闭。
```shell
# toggle window border
alt - b : yabai -m window --toggle border
```
## space 的基础操作
MacOS 在系统级别中为我们提供了 10 个 Space（即桌面1-10），通过 <系统偏好设置-快捷键-调度中心> 对切换桌面的快捷键进行配置，默认是`ctrl 1/9 0`。在这个页面也可以查看MacOS自带的快捷键列表并自行配置，也方便我们查看冲突的快捷键或取消不需要的快捷键（以免不小心触发）。

![-w668](https://vde05-1256575153.cos.ap-beijing.myqcloud.com/2022/02/11/16445595603091.jpg)

在 yabai 中，我们可以充分利用这10个桌面，对窗口进行统一的管理。首先建议对每个桌面分配不同的工作属性，例如在桌面2中显示终端；在桌面7打开 matlab、pycharm 等代码编辑工具；在桌面9打开网易云、INNA 视频播放器等影音娱乐程序等等。接下来对这部分在配置文件`~/.yabai`中的设置进行说明。
```shell
# 可以从左上角苹果符号 () 旁边的 macOS 菜单栏中检查应用程序名称。
# workd after open a new software or restart yabai
##### space 1 -- main
yabai -m space 1 --label main
yabai -m rule --add label=zotero app="^Zotero$" space=main

##### space 2 -- Shell
yabai -m space 2 --label shell 
yabai -m rule --add label=iterm app="^iTerm2$" space=shell

##### space 3
# 1. chrome -- browser
yabai -m space 3 --label surf
yabai -m rule --add label=chrome app="^Google Chrome$" space=surf
# 2. Safari 
yabai -m rule --add label=safari app="^Safari浏览器$" space=surf
```
限于篇幅，使用前三个 space 的配置进行示例。我们可以使用 `--label` 对每一个 space 进行命名，并将应用程序指定到该 space 中。

> 上述配置的作用是当打开应用程序或重启 yabai 时，自动将该应用程序移动到指定的 space 中。

清楚了效果以后，我们可以想像一下应用场景。我的使用方式是将功能划分很明显的应用分配到固定的 space 中，一般不会改变它的 space；对于可能在多个 space 中打开的应用（例如 Finder），不设置它的固定 space。

然而有些情况我们还是需要将某个应用程序移动到别的 space 中的，并且我们需要很频繁地在多个 space 中切换。为了解决以上两个问题，我们可以在 `~/.skhdrc` 中进行如下配置：
```shell
# 使用 alt - 1/2/3 进行space的快速切换
alt - 1 : yabai -m space --focus main
alt - 2 : yabai -m space --focus shell
alt - 3 : yabai -m space --focus surf
# 使用 ctrl+alt-1/2/3 将当前聚焦的应用程序移动到 space 1/2/3 中
ctrl + alt - 1 : yabai -m window --space main
ctrl + alt - 2 : yabai -m window --space shell
ctrl + alt - 3 : yabai -m window --space surf
```

## window 的基础操作
window（窗口）的基础操作大致可以分为三类，为了方便记忆，我为其分配了不同的快捷键前缀：

1. 聚焦到某一窗口：`ctrl + alt`
2. 窗口的移动、旋转：`shift + alt`
3. 窗口的大小调整：`shift + cmd`

这部分没什么特别需要讲解的，大家可以自行构想出方便使用的快捷键，直接看 `~/.skhdrc` 中的代码吧！

```shell
# =======================  window focus ==========================
# 只在平铺的窗口上生效，浮动的窗口之间移动实现起来比较麻烦
# focus window in direction of focused window (options: north, east, south, west)
ctrl + alt - l : yabai -m window --focus east
ctrl + alt - h : yabai -m window --focus west
ctrl + alt - j : yabai -m window --focus south
ctrl + alt - k : yabai -m window --focus north
#
# focus window that was previously focused
ctrl + alt - b : yabai -m window --focus recent
```
```shell
# =======================  window layout ==========================
# 将窗口移动到上/下/左/右
shift + alt - h : yabai -m window --warp west
shift + alt - j : yabai -m window --warp south
shift + alt - k : yabai -m window --warp north
shift + alt - l : yabai -m window --warp east

# toggle window split type-水平排列转为垂直排列
shift + alt - g : yabai -m window --toggle split

# 窗口顺时针旋转 90 度,一开始垂直的半边屏幕总是一个总体,用的少
shift + alt - r : yabai -m space --rotate 270
# 窗口逆时针旋转 90 度
# shift + alt - r : yabai -m space --rotate 90

# Rotate on X and Y Axis
shift + alt - x : yabai -m space --mirror x-axis
shift + alt - y : yabai -m space --mirror y-axis

# Float and center window-bsp/float
# The grid format is <rows>:<cols>:<start-x>:<start-y>:<width>:<height>
shift + alt - c : yabai -m window --toggle float;\
                  yabai -m window --grid 6:6:1:1:4:4
# 更小的浮动窗口
shift + alt - s : yabai -m window --toggle float;\
                  yabai -m window --grid 4:4:1:1:2:2
```
```shell
# =======================  window resize ==========================
# Make window native fullscreen-float
alt - f : yabai -m window --toggle zoom-fullscreen
# Resize windows
shift+ cmd - h : \
    yabai -m window --resize left:-20:0 ; \
    yabai -m window --resize right:-20:0

shift + cmd - j : \
    yabai -m window --resize bottom:0:20 ; \
    yabai -m window --resize top:0:20

shift + cmd - k : 
    yabai -m window --resize top:0:-20 ; \
    yabai -m window --resize bottom:0:-20

shift + cmd - l : \
    yabai -m window --resize right:20:0 ; \
    yabai -m window --resize left:20:0

# Equalize size of windows
shift + cmd - 0 : yabai -m space --balance

```

## 快速打开应用
在 MacOS 中有很多种方式快速打开应用（如果已经打开则会聚焦到该应用窗口），目前是使用 Hammerspoon 来定义快速打开应用的快捷键。有关 Hammerspoon 的使用方法和 Case，我估计会专门出一篇文章介绍，这里只列出快速打开应用的代码。在 `~/.hammerspoon/init.lua` 文件中加入以下代码段，就可以使用 `alt` 加对应的按键快速打开应用程序了。
```shell
--- 快捷键小写大写等效
hs.hotkey.bind({"alt"}, "E", open("Microsoft Word"))
hs.hotkey.bind({"alt"}, "W", open("WeChat"))
hs.hotkey.bind({"alt"}, "C", open("Google Chrome"))
hs.hotkey.bind({"alt"}, "I", open("iTerm"))
hs.hotkey.bind({"alt"}, "M", open("NeteaseMusic"))
hs.hotkey.bind({"alt"}, "P", open("PDF Expert"))
-- hs.hotkey.bind({"alt"}, "S", open("Emacs"))
hs.hotkey.bind({"alt"}, "L", open("Notion"))
hs.hotkey.bind({"alt"}, "S", open("Logseq"))
hs.hotkey.bind({"alt"}, "R", open("Mweb"))
```
这里应用程序的名称可以在 `/Applications` 目录下找到，找到对应的应用程序后，去掉 `.app` 后缀即可。


