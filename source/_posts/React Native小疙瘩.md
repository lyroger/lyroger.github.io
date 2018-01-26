---
title: React Native小疙瘩
status: public
date: 2017-06-15 14:17:48
categories: summary
---
### 前言
  从去年开始，混合框架开发模式就开始步入到我的实际工作当中，听说过[React Native](http://reactnative.cn),但重来没有真正花时间和精力去了解和学习使用他。去年还写过一篇关于混合框架开发的[使用Cordova搭建混合框架](http://blog.devyan.cn/2016/09/23/使用Cordova搭建混合框架/)文章。主要是介绍使用Cordova框架，这个框架已经在我们公司实践一年多时间了，到目前为止已经有六个APP使用了该框架，效果还是挺突出的，第一个混合框架的出生，也让我感到很惊讶，使用H5也能写出交互效果如此不逊的APP，顿时也开始担心自己的小饭碗了。
然而最近为什么突然想起了`React Native`呢？总感觉在一个“圈子”里混熟了，混久了，自己很没安全感呢？对，我就是这么一个人，我想多了解一下这方面的技术。毕竟像QQ空间这么热门的APP都在使用该框架，我有什么权利不去了解该框架呢？好了，不多说了，开始学习`React Native`框架吧！

### ‘boost/iterator/iterator_adaptor.hpp’ file not found 问题
按照[官网](http://reactnative.cn/docs/0.45/getting-started.html#content)的指示，我做的挺顺利的。到最后一步的时，被一个`‘boost/iterator/iterator_adaptor.hpp’ file not found`编译问题折腾了我大半天，google百度出现同样的问题的很多，唯一看到一篇很靠谱的解决方案的[文章](http://vanessa.b3log.org/articles/2017/06/12/1497235254333.html)，却感觉说的很含糊，或着说吧，扯进了一些无关紧要的信息进来，导致我无从下手，其实只需要将`boost_1_63_0.tar.gz`下载下来解压放到`node_modules/react-native/third-party`下即可,跟.rncache文件夹没有任何关系，误导了我找这个文件夹就花了半天，也不知道如何下载该文件！不过总之还是得感谢该作者！让我从不断更新和重装node版本，各个软件环境等错误的尝试中找到一丝灵感。找到了.rncache文件夹，尝试这解压下面的压缩文件，只有`boost_1_63_0.tar.gz`文件解压有问题，所以去[官网](https://sourceforge.net/projects/boost/files/boost/1.63.0/)下载了该文件。总之解决该问题之后，迫不及待的就想要写出第一个Hellow World程序了。
