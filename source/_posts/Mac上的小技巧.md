---
date: 2016-07-07 17:16
status: public
title: Mac上的小技巧
category: 工具
---
### 1.制作SSH秘钥
打开终端，$ ssh-keygen 一路enter下。
生成  在当前用户名下 会有一个.sh文件。
查看 .ssh
$ ls -a ~/.ssh  通过搜索目录
id_rsa
id_rsa.pub
打开  id_rsa.pub文件 里面就有需要的ssh key。
或者使用文本编辑工具打开该文件，命令是：`vim ~/.ssh/id_rsa.pub`

### 2.显示或隐藏mac下的隐藏文件
1. 显示Mac隐藏文件的命令：`defaults write com.apple.finder AppleShowAllFiles  YES`
2. 隐藏Mac隐藏文件的命令：`defaults write com.apple.finder AppleShowAllFiles  NO`
3. 设置完后，不会立马起效，需要重新启动Finder.可以通过命令:`killall Finder` 。
	
