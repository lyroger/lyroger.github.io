---
date: 2016-07-07 17:16
status: public
title: Mac上制作SSH秘钥
category: 基础篇
---

打开终端，$ ssh-keygen 一路enter下。
生成  在当前用户名下 会有一个.sh文件。
查看 .ssh
$ ls -a ~/.ssh  通过搜索目录
id_rsa
id_rsa.pub
打开  id_rsa.pub文件 里面就有需要的ssh key。
或者使用文本编辑工具打开该文件，命令是：
vim ~/.ssh/id_rsa.pub