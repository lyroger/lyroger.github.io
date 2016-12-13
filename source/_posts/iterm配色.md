---
date: 2016-12-13 09:30
status: public
title: iterm2配色
category: 工具
---

1. 首先查看一下你的~/.bash_profile文件是否存在，如果不存在，那么可以通过命令创建
vim ~/.bash_profile #该命令是查看的，若没有会自动创建该文件。
创建后将下面内容复制进去：

```
#enables colorin the terminal bash shell export

CLICOLOR=1

#sets up thecolor scheme for list export

LSCOLORS=gxfxcxdxbxegedabagacad

#sets up theprompt color (currently a green similar to linux terminal)

exportPS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;36m\]\w\[\033[00m\]\$ '

#enables colorfor iTerm

exportTERM=xterm-color

```
然后按Esc键，继续w q键，保存退出。

2.第二步打开Iterm2->Perferences->Profiles->Terminal
![](/images/iterm2_Color_2016121301.png)

3.第三步选择配色方案。
![](/images/iterm2_Colors2016121302.png)

4.最后，如果想要选择更多的配色方案，可以[这里](https://github.com/mbadolato/iTerm2-Color-Schemes)下载。