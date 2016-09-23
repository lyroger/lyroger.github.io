---
title: 使用Cordova搭建混合框架
date: 2016-09-23 14:22:31
tags: Hybrid App
category: 框架

---

## 前言
  入职公司不久，花了一个多月开发了一个原生APP，这也只是个开始，首先强调的是，我们公司并不是外包公司，但需要开发的项目其实还挺多的，为了解决这个问题，我们需要周期短，响应快，来满足公司的需求，我们只能抛弃纯原生APP的开发方案，选择目前市场比较热门的技术方案：*Hybrid App*。使用*Hybrid App*的技术框架其实有很多开源的技术方案，比如使用[Cordova](http://cordova.apache.org)，[React Native](http://reactnative.cn)，等。这两个技术方案其实都还不错，由于我们公司有人以前有使用*Cordova*框架的经验，所以我们选择了*Cordova*,这个理由听起来感觉有些过不去，但有时候领导就是喜欢听取有经验的方案。不过走到现在确实觉得*Cordova*也很不错,没出什么问题，也都能满足项目上的需求。
  
## 搭建框架
 接下来我们来搭建一个*Hybrid App*试试手吧，创建一个`MyHybridDemo`，使用`Pod`来管理*Cordova*框架，可以通过命令`pod 'Cordova'`从Github上下载*Cordova*，集成*Cordova*到工程中后，还缺html5与原生代码交互的桥梁，需要一些js的支撑，你可以查看[官网](http://cordova.apache.org/docs/en/latest/guide/cli/index.html)的文档，一步一步创建相应文件。也可以直接从我的[MyHybridDemo]()中拷贝www文件夹和config.xml文件集成到你的工程中。到这里集成框架算是ok了，接下来就是写业务代码了。在写业务代码之前首先来介绍一下www目录下的文件和config.xml文件的作用。
 使用终端cd到www目录下`tree -L 2`（需安装tree工具）命令查看www目录：
 ```js
.
├── assets
│   ├── images
│   ├── js
│   └── style
├── cordova-js-src
│   ├── exec.js
│   └── platform.js
├── cordova.js
├── cordova_plugins.js
├── index.html
└── plugins
    ├── com.exmind.photopickerplugin
    ├── com.exmind.shareplugin
 ```
* `assets`:该目录是用来放资源文件的，比如h5相关的业务图片，视频等资源文件。
* `cordova-js-src`:该目录是存放cordova的js框架代码，这个你直接拷贝过来就ok了，不用多管。
* `cordova.js`:该文件是cordova的js框架代码。直接拷贝。
* `cordova_plugins.js`:该js文件是cordova来定义js插件文件，他包含定义插件的文件的路径，插件ID，以及插件名称。最终获取一个插件集合。
* `plugins`:该文件夹是js调用原生插件的入口，所有插件可以放到此文件夹中。比如拍照，定位，分享等插件js。
* 最后还有一个config.xml文件,该xml文件主要是用作配置属性的。可以配置h5入口，也可以配置UIWebView的属性等。

