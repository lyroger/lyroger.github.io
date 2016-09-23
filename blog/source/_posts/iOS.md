---
date: 2015-09-08 14:18
status: public
title: iOS学习笔记
category: 基础篇
---

f## 一.LLDB的使用
1. po: 对一个对象的打印，即“print-object”.
1. p: 对一个简单类型的打印.
1. recursiveDescription: UIView私有方法，可以打印更具体更详细的布局结构.比如 :
``` objective-c
po [view recursiveDescription]
<UIView: 0x178199640; frame = (0 64; 320 568); userInteractionEnabled = NO; tag = 99; layer = <CALayer: 0x178421480>>
   | <UILabel: 0x13ce20170; frame = (90 50; 140 23.86); text = '搜索更多的内容'; userInteractionEnabled = NO; layer = <_UILabelLayer: 0x17008f190>>
   | <CALayer: 0x170034e60> (layer)
```
这样你能更清晰的知道view的布局结构，它下面包含了一个UILabel和一个CALayer。利用这个私有方法，有时候确实能解决一些难题，曾经在做一个搜索功能的时候，遇到一些瓶颈，使用这个方法让我找到了解决问题的地方。在[这篇文章](http://lyboy.farbox.com/post/uisearchbarde-shi-yong)中有相关提到。[源代码][mark_SearchBar]
[mark_SearchBar]: https://github.com/lyroger/SearchBarPro (SearchBarPro)
## 二.数据库相关
1. 当你有多条数据插入的时候，但又不清楚数据库是否已经存在该记录，怕导致重复添加数据，你首先想到的可能就是先查询一次，然后判断是否存在相同记录，如果不存在则添加，存在则更新。但这样的操作并不适用于数据量稍微多一点的场合，现在有一种方法可以有效的解决这个问题适用,如下代码:
```objective-c
replace into table (a,b) values ('1','2')
```
使用这条sql语句的有效性的前提是table中必须必须有唯一标示 UNIQUE，
```objective-c
CREATE TABLE IF NOT EXISTS TestTable ('a' VARCHAR PRIMARY KEY NOT NULL UNIQUE, 'b' VARCHAR)
```
定义好a为唯一标示后，上面的replace into 才会起效。
如果我的数据中一个字段并不能确定唯一性，那需要加约束；
```objective-c
CREATE TABLE IF NOT EXISTS TestTable ('a' VARCHAR PRIMARY KEY NOT NULL UNIQUE, 'b' VARCHAR,'c'VARCHAR,UNIQUE(b,c) ON CONFLICT REPLACE)
```
这个时候，如果你插入的数据b，c有跟以前记录中都相同的话，那么他只会做更新操作，并不会做插入操作。这便是replace into 语句的好处。避免重复。
## 三.Auto Layout
> Auto Layout + UITableView-FDTemplateLayoutCell + Masonry
## 四.时间差
```objective-c
    mach_timebase_info_data_t info;
    if (mach_timebase_info(&info) != KERN_SUCCESS) return -1.0;
    uint64_t start = mach_absolute_time ();
    // 这里写入需要统计时间的执行代码
    // ......
    uint64_t end = mach_absolute_time ();
    uint64_t elapsed = end - start;
    uint64_t nanos = elapsed * info.numer / info.denom;
    return (CGFloat)nanos / NSEC_PER_SEC;
```
## 五.pod使用提高篇
#### pod更新:
更新新的依赖库时，使用pod update 命令很多时候都卡在了Analyzing dependencies不动，使用这个命令会升级CocoaPods的spec仓库，所以跟新非常慢，要等很久。
在网上搜索解决此方法的命令
```
pod update --verbose --no-repo-update
```
#### 通过vim命令创建文件
1：cd到相应文件夹
2：输入vim Podfile
3:这时已经创建了文件，可以在dos中输入文件内容，
platform :ios, '7.0'
pod 'Masonry'
4:按Esc键，然后输入":"，然后输入"wq",Podfile文件及内容创建完毕
## 六.iOS中使用的锁
1. @synchronized 关键字加锁
2. NSLock 对象锁
3. NSCondition
4. NSConditionLock 条件锁
5. NSRecursiveLock 递归锁
6. pthread_mutex 互斥锁（C语言）
7. dispatch_semaphore 信号量实现加锁（GCD）
8. OSSpinLock

## 七.UIViewController页面内容适配
在滚动视图中 设置
automaticallyAdjustsScrollViewInsets属性可以控制滚动内容是否会在bar下方显示。
## 八.使用终端统计项目代码总行数
1、打开终端
2、cd 进入项目根目录
3、输入命令 find . "(" -name "*.m" -or -name "*.mm" -or -name "*.cpp" -or -name "*.h" -or -name "*.rss" ")" -print | xargs wc -l
4、回车