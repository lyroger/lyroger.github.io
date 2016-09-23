---
date: 2015-12-23 17:33
status: public
title: FMDB使用SQLCipher加密
---

### 前言
数据库数据加密的必要性就不多说了，直接进入主题，如何使用FMDB对数据加密。
这里我建议使用cocospod来管理第三方库，如果没有安装可以参考[这里](http://blog.devtang.com/blog/2014/05/25/use-cocoapod-to-manage-ios-lib-dependency/)来安装.
#### 1.首先，我们的下载SQLCipher依赖库
1.1 ```pod 'SQLCipher', '~> 3.1.0'```
#### 2.其次，我们需要一个具有加密功能的FMDB版本；
加密的FMDB其实是一个分支，也就是说，如果你的FMDB版本不支持SQLCipher加密，那么你就需要替换FMDB。Github上关于该分支的安装只提供了cocospod的安装方式。Github上FMDB的[地址](https://github.com/ccgus/fmdb);
使用cocospod来跟新具有加密功能的FMDB
2.1 将以前```pod 'FMDB'``` 改成```pod 'FMDB/SQLCipher'```
2.2 更新pod ```pod update```
#### 3.最后，修改一下你的代码
定义一个加密的key,这里我自定义了一个宏```DB_SECRETKEY```,然后在FMDB中的FMDatabase.m类中加一句代码，如下图:
![](/images/F0547994-4A8E-43A8-A937-DC94A4637CA2.png)
#### 4.效果
run下你的工程，并对数据库做一些操作，然后在使用其他管理工具打开数据时，你就发现他会提示你file is encrypted or is not a database
![](/images/AFB7BECA-A6FF-4CE4-BAA1-99097EFAD5BF.png)