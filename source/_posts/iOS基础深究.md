---
date: 2016-02-25 09:37
status: public
title: iOS基础深究
category: 基础篇
---

## 一、assign与weak的区别
assign适用于基本数据类型，weak是适用于NSObject对象，并且是一个弱引用。 
assign其实也可以用来修饰对象，那么我们为什么不用它呢？因为被assign修饰的对象在释放之后，指针的地址还是存在的，也就是说指针并没有被置为nil。如果在后续的内存分配中，刚好分到了这块地址，程序就会崩溃掉。 
而weak修饰的对象在释放之后，指针地址会被置为nil。所以现在一般弱引用就是用weak。 
所以在使用assign修饰对象，就有可能会导致程序EXC_BAD_ACCESS异常，也就是所谓的野指针错误！

## 二、nonatomic与atomic
nonatomic: 非原子性访问，对属性赋值的时候不加锁，多线程并发访问会提高性能。若不加此属性，则默认是两个访问方法都为原子型事务访问。
atomic:是Objc使用的一种线程保护技术，基本上来讲，是防止在写未完成的时候被另外一个线程读取，造成数据错误。而这种机制是耗费系统资源的，所以在iPhone这种小型设备上，如果没有使用多线程间的通讯编程，那么nonatomic是一个非常好的选择。

## 三、ARC模式下重写set方法
网上很多人说直接赋值就OK了，我感觉不太妥，这里我验证了下，如果是strong修饰的属性，是可以直接赋值的，如果是copy，那么需要copy一份了。看看下面代码
1.首先定义两个copy属性及测试方法
```objective-c
@property (nonatomic,copy) NSString *test;
@property (nonatomic,copy) NSMutableString *testMutableString;
......
//测试方法
- (void)testProperty
{
    NSString *testString = @"test";
    NSMutableString *testMutableString = [NSMutableString stringWithFormat:@"testMutableString"];
    self.test = testString;
    self.testMutableString = testMutableString;
}
```
2.重写两个属性的set方法，验证直接赋值
```objective-c
- (void)setTest:(NSString *)test
{
    _test = test;
    NSLog(@"_test = %p,test = %p",_test,test);
}

- (void)setTestMutableString:(NSMutableString *)testMutableString
{
    _testMutableString = testMutableString;
    NSLog(@"_testMutableString = %p,testMutableString = %p",_testMutableString,testMutableString);
}
```
打印出来的日志显示:
```
2016-02-25 10:23:30.618 DemoList[4341:2473539] _test = 0x1001364d0,test = 0x1001364d0
2016-02-25 10:23:30.621 DemoList[4341:2473539] _testMutableString = 0x13ef2b950,testMutableString = 0x13ef2b950
```
直接赋值的方式从这里可以看出来，打印的地址都是一样的，只是引用而已，并没有产生新的对象。
3.我们再来看看copy一份的结果。
```objective-c
- (void)setTest:(NSString *)test
{
    _test = [test copy];
    NSLog(@"_test = %p,test = %p",_test,test);
}

- (void)setTestMutableString:(NSMutableString *)testMutableString
{
    _testMutableString = [testMutableString copy];
    NSLog(@"_testMutableString = %p,testMutableString = %p",_testMutableString,testMutableString);
}
```
//打印出来的日志显示
```
2016-02-25 10:12:26.642 DemoList[4332:2471119] _test = 0x10018a4d0,test = 0x10018a4d0
2016-02-25 10:12:26.645 DemoList[4332:2471119] _testMutableString = 0x15d76b6d0,testMutableString = 0x15d6abd40
```
这个时候你会发现，NSString的属性地址还是一样的，NSMutabeString的属性地址不一样了，这样就对了，NSString的属性其实希望的就是引用一份，而可变字符的就是希望真正的copy一份，如果地址还一样，那么就没必要使用可变的对象了。
总结下ARC模式的set方法，使用copy修饰属性时，最好使用copy去赋值。其他的可以直接赋值就是了。

## 四、block属性为什么需要使用copy修饰
因为block变量默认是声明为栈变量的，为了能够在block的声明域外使用，所以要把block拷贝（copy）到堆，所以说为了block属性声明和实际的操作一致，最好声明为copy。