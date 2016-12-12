---
date: 2016-11-29 10:02:51
status: public
title: JS与OC交互
category: 基础篇
---
### 前言
  平时开发中，很多页面并非全由原生代码来编写，需求求快，求可扩展性，往往希望页面能够很轻松替换或更改，所以有些页面在不追求体验之下由web来展示是一个比较好的方案。web页面在app端展示其实也有两种方案，一种是把web页面放在服务器端，一种是把web页面等资源一起打包放在App端，这两种方案的各自好处就不在这里详解，第二种方案在我的[使用Cordova搭建混合框架](http://blog.devyan.cn/2016/09/23/使用Cordova搭建混合框架/)文章中有提到，H5与原生代码直接的交互都有详解。这篇文章主要讲讲H5跟原生代码之间的交互。
  
### 原生代码调用H5
  这个方案估计很多人都熟悉，也调用的比较频繁。比如现在在我们最近的一个项目中，我们把一个Web页面封装成一个BaseWebController页面了，所有基于远程web页面来展示的页面都通过这个封装好了的BaseWebController页面展示，那么在这个封装好了的页面中我们做了些什么事情呢？想想平时我们在这些web页面中都会做些什么，右上角更多按钮，右上角分享按钮，Web页面的title，下拉web页面时，能看到该页面由谁提供的，等等这些小功能，我们都可以在`- (void)webViewDidFinishLoad:(UIWebView *)webView`方法执行后，通过`stringByEvaluatingJavaScriptFromString:`方法去获取该页面中的配置，这个配置信息其实也是于H5有个协议，我们与H5端先定好一个协议，比如`msg_can_share`字段值为true时，表示可以分享，那么就会接着配置分享相关信息字段，比如分享的title，分享的url，分享的描述等等信息，这些信息都可以直接通过`stringByEvaluatingJavaScriptFromString:`去获取。
### H5调用原生代码
  原生代码调用H5用一个方法就可以搞定，很简单，那么H5调用原生的又怎么来实现呢，又有什么场景呢？驱使我想用实现这种方案的动力来自一个问题，由于H5端查看文章详情时，footer下还有一个查看下一篇文章，点击查看下一篇文章时，需改变我们BaseWebController的title，问题来了，点击查看下一篇文章是，H5是由异步调用服务器数据，所以不会再次进入`- (void)webViewDidFinishLoad:(UIWebView *)webView`，因此我们无法通过`stringByEvaluatingJavaScriptFromString:`去拿到最新的title，我们也不知道数据时什么时候来的，这时候我们就只好通过H5来调用原生代码，当H5那边数据已更新后，再调用原生代码来更新title。相信还有更多如此的场景，那么如何来实现这种方案呢？在iOS7之后，我想，大家应该都了解了一些JavaScriptCore.framework的框架吧，对，我们就用这个来实现这个方案，我们看看下面几步，很简单：
1. 首先我们定义一个代理：
```objective-c
@protocol JSObjcDelegate <JSExport>
- (void)webViewJSAction:(NSString*)action :(NSString*)arg;
@end
```
2. 然后在BaseWebController中定义一个`@property (nonatomic, strong) JSContext  *jsContext;`属性。
3. 然后再在恰当的时候（建议写到`- (void)webViewDidFinishLoad:(UIWebView *)webView`中）去获取H5的上下文，将刚刚写好的代理赋值给到H5。
```objective-c
	_jsContext = [webView valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
   _jsContext[@"WebViewObject"] = self;
   _jsContext.exceptionHandler = ^(JSContext *context, JSValue *exceptionValue) {
       context.exception = exceptionValue;
       NSLog(@"异常信息：%@", exceptionValue);
   };
```
4. 最后在BaseWebController中实现代理就ok了。
```objective-c
- (void)webViewJSAction:(NSString*)action :(NSString*)arg
{
    if ([action isEqualToString:@"UpdateWebViewTitle"]) {
        self.title = arg;
    }
}
```
写到这里，我们都是站在原生代码的角度去考虑，实现。那么H5端需要什么做呢？很简单，只需要通过`window.WebViewObject.webViewJSAction('UpdateWebViewTitle','title')`就可以将需要的值传过来。调用原生的方法。