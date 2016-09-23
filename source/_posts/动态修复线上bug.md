---
date: 2016-01-07 14:23
status: public
title: 动态修复线上bug
category: 高效篇
---

### 前言
这个主题是在QQ空间终端开发团队公众号的一篇文章看到的([链接地址](http://zhuanlan.zhihu.com/magilu/20288657))，平时开发中确实没有使用过，为了以后派上用途，在这写这篇文章记录一下。QQ空间终端开发团队公众号一文中提到的使用TPatch来动态修复补丁，我找了很久确没找到TPatch的下载地址，而且他还不开源，但通过他我却了解到其他比较好用的动态修复补丁的框架，比如[JSPatch](http://jspatch.com),这篇文章我就JSPatch来记录如何使用JSPatch修复线上APP的bug。
### 修复的基本原理
能实现线上修复bug的最根本的原理是因为 Objective-C 是一门动态语言，OC上所有方法的调用/类的生成都通过 Objective-C Runtime 在运行时进行，所以我们可以通过类名/方法名反射得到相应的类和方法，也可以替换某个类的方法为新的实现，还可以新注册一个类，为类添加方法。
### JSPatch的使用
1. 获得AppKey
首先你得先去[JSPatch申请账号](http://jspatch.com/Index/invite)，获得AppKey，每个App都有对应的一个唯一AppKey。
2. 下载SDK并导入工程
下载 SDK 后解压，将 JSPatch.framework 导入到工程中，并添加两个系统库文件：libz.dylib 和 JavaScriptCore.framework。
3. 添加代码
在 AppDelegate.m 里载入文件，并调用 +startWithAppKey: 方法，参数为第一步获得的 AppKey。例子：
```objective-c
#import <JSPatch/JSPatch.h>
@implementation AppDelegate
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [JSPatch startWithAppKey:@"你申请的AppKey"];
    // your code
}
@end
```
至此 JSPatch 接入完毕，接着你就可以在后台为这个 APP 添加JS补丁了。
4.JS补丁代码如何写
写到这里，其实自己也并不太熟悉 JS 补丁代码的基本语法，不过还好他提供了[文档](https://github.com/bang590/JSPatch/wiki/基础用法)，更好的是提供了机器人帮你将OC代码转换成JS代码，[转换地址](http://bang590.github.io/JSPatchConvertor/)，不过提供转换的功能实现简单的OC代码还行，比较复杂的还需人工修复，所以：如果有使用到动态修复线上 APP Bug，并且选择使用了 JSPatch，那么你就不的不好好学习一下 JS 补丁代码的语法了。

### 补丁版本支持
1. 可针对APP中对应的版本发布补丁代码。
2. 可修改已经发布的补丁脚本代码，既重新上传该版本的脚本代码，APP重新启动时，会请求判断是否已经更新，若更新了会下载最新脚本代码覆盖原来的脚本代码，启动后并执行。
3. 可移除已经发布的补丁脚本代码，只要在后台页面删除该版本的APP，客户端APP请求是发现该版本已经被删除，则会自动删除本地的补丁脚本代码。

### 总结
既然有这么好用的东西，记录下来并不是一件坏事，说不定以后能排上用场。另外，估计大家也会想到，既然这么强大，那为啥还要发布新版本，直接都通过 JS 脚本代码来实现新需求，这不避免了重新上线中一大堆麻烦的事情了吗？其实解决一些小问题还是可以，如果有大批需求需要上线，估计使用这种方式还是不太妥当，毕竟通过脚本代码转换到OC是需要消耗一定的性能的。

----------
> 参考文献:
> [JSPatch实现原理详解](http://blog.cnbang.net/tech/2808/)
> [JSPatch](http://jspatch.com)