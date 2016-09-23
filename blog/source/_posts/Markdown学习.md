---
date: 2015-10-14 13:16
status: public
title: Markdown学习
category: 基础篇
---

## 一.标题
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题


## 二.无序列表
* 我的
* 你的
* 他的

## 三.有序列表
1. 我的
2. 你的
3. 她的

## 四.引用
> 这里是引用

## 五.图片与链接
1. ![Mou的icon](http://mouapp.com/Mou_128.png)
2. [Markdown学习链接](http://www.jianshu.com/p/1e402922ee32/)
3. 建议您参考[Markdown——入门指南][mark]或者参考[Markdown语法说明(简体中文版)][mark2]
[mark]: http://www.jianshu.com/p/1e402922ee32/ (Markdown——入门指南)
[mark2]: http://wowubuntu.com/markdown/ (Markdown语法说明(简体中文版))

![](/images/jiafei.gif)

    
```
    链接内容定义的形式为：
    1.方括号（前面可以选择性地加上至多三个空格来缩进），里面输入链接文字
    接着一个冒号
    接着一个以上的空格或制表符
    接着链接的网址
    选择性地接着 title 内容，可以用单引号、双引号或是括弧包着
    下面这三种链接的定义都是相同：
    1. [foo]: http://example.com/  "Optional Title Here"
    2. [foo]: http://example.com/  'Optional Title Here'
    3. [foo]: http://example.com/  (Optional Title Here)
    请注意：有一个已知的问题是 Markdown.pl 1.0.1 会忽略单引号包起来的链接 title。
    链接网址也可以用尖括号包起来：
    * [id]: <http://example.com/>  "Optional Title Here"
```
## 六.粗体与斜体
1. 这是 **粗体**
1. 这是 *斜体*
1. 这是 常态

## 七.表格
|  name(巨左对齐) |  age(居中对齐)  | sex(居右对齐) |
|-------|:------:|------:|
|ly     |28     |男      |

## 八.代码框
```objective-c
- (UIImage *)imageFromColor:(UIColor *)color frame:(CGRect)rect
{
    UIGraphicsBeginImageContext(rect.size);
    CGContextRef context = UIGraphicsGetCurrentContext();
    CGContextSetFillColorWithColor(context, [color CGColor]);
    CGContextFillRect(context, rect);
    UIImage *img = UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();
    return img;
}
```

``` 
# 标题一
## 标题二
### 标题三
```
## 九.分割线
***************
----------

## 十.区域引用
> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
> consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
> Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.
 
> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
> id sem consectetuer libero luctus adipiscing.