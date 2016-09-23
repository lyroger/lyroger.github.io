---
date: 2015-10-22 14:29
status: public
title: 升级XCode7和iOS9相关问题
---

## 一.报Bitcode相关错误。
```
Framework/libraries/extends/QQSDK/TencentOpenAPI.framework/TencentOpenAPI(WeiyunAPI.o)' does not contain bitcode. You must rebuild it with bitcode enabled (Xcode setting ENABLE_BITCODE), obtain an updated library from the vendor, or disable bitcode for this target. for architecture arm64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
```
1.解决方案
工程文件中找到target->Build Settings->搜索bitcode，然后出现Enablecode选项，设置为NO即可。如图：

![](/images/code.jpg)
重新build即可消除bitcode相关问题。

2.问题出现原因可以参考：[理解Bitcode：一种中间代码][mark]
[mark]:http://www.cocoachina.com/ios/20150818/13078.html (理解Bitcode：一种中间代码)
## 二.iOS9设备网络请求失败
1.出现问题原因
iOS9把所有的http请求都改为https了：iOS9系统发送的网络请求将统一使用TLS 1.2 SSL。采用TLS 1.2 协议，目的是:强制增强数据访问安全，而且，系统 Foundation 框架下的相关网络请求，将不再默认使用 Http 等不安全的网络协议，而默认采用 TLS 1.2。服务器因此需要更新，以解析相关数据。如不更新，可通过在 Info.plist 中声明。
2.具体如何在info.plist中声明
```
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```
如图所示:

![](/images/plist.jpg)