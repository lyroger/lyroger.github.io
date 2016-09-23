---
date: 2016-01-21 14:26
status: public
title: 动态添加需求(动态库)
category: 高效篇
---

## 前言
前些天，公司提出需要动态添加一些需求给线上App，当时觉得有点不太好办，因为当时还没有头绪，后来上网查了些资料，如[这篇文章](http://blog.csdn.net/like7xiaoben/article/details/44081257)，不过他这只是将写好的动态库拷贝到本地的沙盒中，也就是有这个方法然后我有了头绪，我只需将写好的动态库压缩上传到服务器，提供线上APP下载，解压后加载动态库执行代码即可实现其功能。接下来还是来看看具体操作和过程中遇到的一些问题和注意点吧。整个需求我写了一个[demo](https://github.com/lyroger/DynamicDemo),你可以先下载这个[demo](https://github.com/lyroger/DynamicDemo),对照此文章来理解。
### 一、生成动态库
Xcode6就支持动态库了，所以只要使用XCode6及更高版本的XCode便可以生成动态库。
![](/images/9E729546-8D22-48A6-A3D7-88FBDD787DD9.png)
此demo中满足需求的动态库我取名叫DynamicFramework.framework。在这个动态库中我实现了弹出一个UIViewController和在这个UIViewController中加载了动态库中的资源文件。并在这个动态库中引用了其他的动态库（此的demo中引用的是CommonFramework.framework），并调用其他库中的方法。这样就可以满足灵活调用和灵活添加需求的要求了。如果你需要给线上app提供动态库，你可以将写好的动态库压缩上传到服务器提供下载。
### 二、加载动态库
在主工程中我们来加载写好的动态库，从网络下载的过程我在demo就没实现了，只做了将下下来的zip文件解压的操作，加载动态库步骤很简单，先将服务器上的DynamicFramework.framework.zip文件下载到沙盒的document中（当然，如果只是写Demo，你完全可以手动拷贝到document中），找到该zip文件，解压到同一个目录下，然后将动态库复制到程序中，即可使用，具体代码如下:
``` objective-c
- (void)loadDynamicFrameworkModel
{
    NSArray* paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory,NSUserDomainMask,YES);
    NSString *documentDirectory = nil;
    if ([paths count] != 0)
        documentDirectory = [paths objectAtIndex:0];
    
    //本地动态库文件
    NSString *libName = @"DynamicFramework.framework";
    NSString *destLibPath = [documentDirectory stringByAppendingPathComponent:libName];
    
    //第一步，判断是否存在动态库文件
    NSFileManager *manager = [NSFileManager defaultManager];
    if (![manager fileExistsAtPath:destLibPath]) {
        NSLog(@"没有动态库文件");
        return;
    }
    //第三步，复制到程序中
    NSBundle *frameworkBundle = [NSBundle bundleWithPath:destLibPath];
    if (frameworkBundle && [frameworkBundle load]) {
        NSLog(@"load Bundle success");
    } else {
        NSLog(@"load Bundle failed");
        return;
    }
}
```
注意:如果出问题了，是不是跟如下类似的错误。
``` 
Error loading ...... no suitable image found.  Did find: ...
```
其实这个错误就是我当初最当心的问题，这个就是由于写动态库时打包的签名和主工程打包的签名不一致导致主工程加载下载下来的动态库失败产生的错误。解决这个错误的办法就是保证你后续写的动态库打包时签名一定要跟线上发布的APP签名一致，否则App并不会执行你的动态库。
### 三、调用动态库
将动态库加载到程序中后，接下来就是调用动态库中的代码了。
1.首先我们用运行时来加载动态库中的类，窥探是否有该类，以防使用报错奔溃。
2.如果有动态库入口的方法类，我们则使用NSObject类 "- (id)performSelector:(SEL)aSelector withObject:(id)object1 withObject:(id)object2"来执行。
这一步我们可以将主工程中的一些变量传入动态库中，供动态库使用，完成新需求。特别是资源信息，以下将frameworkBundle信息传入动态库中供期获取资源。
调用代码如下:
``` objective-c
    //第四步，获取动态库中入口类
    Class pacteraClass = NSClassFromString(@"DynamaicEnterance");
    if (!pacteraClass) {
        NSLog(@"Unable to get DynamicFramework class");
        return;
    }
    //第五步，执行动态库中入口类的方法
    NSObject *pacteraObject = [pacteraClass new];
    [pacteraObject performSelector:@selector(showViewOnController:withBundle:) withObject:self withObject:frameworkBundle];
```
动态库中DynamaicEnterance类只要实现showViewOnController:withBundle:方法就可以了。
在此方法中你可以做你想做的事情，这个桥接过程就已经完成了。此demo中就实现弹出一个UIViewcontroller并加载动态库中的图片资源和调用其他库中的函数。
### 四、总结
总而言之，自从苹果支持动态库后，确实方便了许多。
1. 如果你跟我的需求一致，使用方式一致，请一定记住，你的动态库的签名需跟你的主App的签名一致，否则你的动态库将无法加载到主工程中，你的代码也将无法执行。
2. 生成动态库只在iOS8及更高版本支持，但并不影响低版本使用。
3. 使用动态库实现动态新增需求我并没有在线上APP中实现过，只实现与自己写的Demo。理论上在线下能实现这些功能，上传到Appstore的App也应该没问题。如有朋友遇到问题，欢迎随时留言。