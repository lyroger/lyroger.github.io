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
	
### 3.vim命令退出方式
 ":x"和":wq"的真正区别，如下：
        :wq   强制性写入文件并退出。即使文件没有被修改也强制写入，并更新文件的修改时间。
        :x    写入文件并退出。仅当文件被修改时才写入，并更新文件修改时间，否则不会更新文件修改时间。

### 3.git常用命令
1. git clone 'https://github.com/lyroger/lyroger.github.io.git'  #这个会让你输入账号和密码
2. git clone 'git@github.com:lyroger/lyroger.github.io.git' #这个需要你提供sshkey
3. git add . #将所有变动添加到仓库
4. git commit -a '提交备注' #将所有添加到仓库的变动提交到仓库  如果提交失败：‘fatal: Paths with -a does not make sense.’可以使用"git commit -am '提交备注'".
5. git pull #拉取最新文件
5. git push origin master #推送到远程仓库