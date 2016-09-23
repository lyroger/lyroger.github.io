---
date: 2015-12-25 15:41
status: public
title: Objective-C代码混淆
category: 高效篇
---

### 前言
本方法参考CSDN博主 念茜[iOS安全攻防（二十三）：Objective-C代码混淆](http://blog.csdn.net/yiyaaixuexi/article/details/29201699),为深度理解，自己按照步骤做了一遍。并写下收获和体会。

#### 一、为什么需要对工程代码进行混淆
这里我不多说了，可以参考[念茜](http://my.csdn.net/yiyaaixuexi)的iOS安全攻防系列博客。

#### 二、混淆代码原理
操作过程中，我使用的是 #define ,将工程中的类中所有的方法名替换成随机字符串，当然，字符串需保证不是关键字。这里的替换方法是由博主 念茜 写的一段脚本。这里就不贴代码了，可以直接去她的[iOS安全攻防（二十三）：Objective-C代码混淆](http://blog.csdn.net/yiyaaixuexi/article/details/29201699)博文中参阅。

#### 三、操作步骤
1.将混淆脚本 confuse.sh 放到工程目录下。
2.添加头文件```"codeObfuscation.h"```到pch文件中最前面的位置
```objective-c
#ifdef __OBJC__ 
    //添加混淆作用的头文件（这个文件名是脚本confuse.sh中定义的）  
    #import "codeObfuscation.h"  
    ....
#endif 
```
3.配置 Build Phase，在工程 Build Phase 中添加执行脚本操作，执行 confuse.sh 脚本，如图：
![](/images/48D4E311-9DA2-4A18-A16C-BAF46CAEEE5C.jpg)
4.创建函数名列表 func.list ，写入待混淆的函数名。
5.跑起工程。

#### 四、一路走来，遇到的问题
1. `confuse.sh: No such file or directory`
解决办法：先看看你Build Phases中的Run Script中写的路径是否跟你confuse.sh的路径一致。
2. `confuse.sh: Permission denied`
解决办法:chmod a+x confuse.sh  //对confuse.sh文件增加可执行权限
3. `func.list: No such file or directory`
解决办法:确保func.list文件与confuse.sh文件在同一个文件夹里面，然后修改`念茜`的脚本文件：`STRING_SYMBOL_FILE="func.list"`为`STRING_SYMBOL_FILE="$PROJECT_DIR/$PROJECT_NAME/func.list"`，其实就是改为相对路径，注意这个路径是否对应你的func.list文件路径。

#### 五、享受一下成果
1.首先我func.list中写的要混淆的函数名如下图:
![](/images/0A2DD61D-5C75-4F36-B7A5-A01A14D75040.jpg)
2.使用class-dump查看分析app头文件
class-dump命令:
```swift
class-dump -H ....demo.app -o ..../dump-head
```
-H后面是app文件路径，-o后面导出头文件的文件夹
导出头文件后，查找func.list中替换的类中的函数名如下图:
![](/images/DEF7F821-515F-467E-B1EE-1123F1CC2F4B.jpg)
这样下来，就算拿到分析出头文件，也不太好判断方法是干什么用的。从而又一步提高了app的安全性。

#### 六、建议
如果一个是一个庞端的工程，我想大家都会觉得，自己还需要一个一个将方法名列入到func.list文件中，这可不是一个小的工作量，这里我看到有人优化了这个操作([详见](http://blog.csdn.net/yxh265/article/details/38438959))，无需自己亲手去填充func.list文件，自动将工程中的所有.m对应的.h文件的函数名导入到func.list，确实是个不做的做法，但我在使用过程中，跑到到自动导入的那段代码，总会遇到奔溃。还望作者能多测试一下。