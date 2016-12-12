---
date: 2016-11-16 17:16
status: public
title: NSURL通过URLWithString:创建为nil的问题
category: 基础篇
---
### NSURL通过URLWithString:创建为nil的问题
  最近有使用NSURL请求连接时发现一些小问题，服务端给一个网页链接字符串，如`NSURL *linkURL = [NSURL URLWithString:@"http://192.168.12.28:8000/#/faq?xt=123"];`这样生成的url没问题，可以访问，但将请求变成`NSURL *linkURL = [NSURL URLWithString:[NSString stringWithFormat:@"http://192.168.12.28:8000/#/faq?xt=%@",[value urlEncode]]];`,xt的参数值是变化的，有可能会是任何一种字符，如果该值中为非普通的字符，如带有中文或`#`等之类的字符，创建的url竟然是nil。不太明白NSURL里面是如何创建对象的，想要解决这个问题，只能将参数的值通过UTF-8编码，传进去，服务端接受该值时再解码就好了。
  
