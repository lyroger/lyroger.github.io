---
date: 2016-07-08 11:15
status: public
title: MVVM模式的学习
category: 设计模式
---

## 一 前言
这里就不多说为什么要学习MVVM模式了，新东西当然需要有一颗进步的心态去接纳它，不管他是否真的能在实际开发中帮到你，我想至少会在某种场景中用到。好吧，我们先来理解一下什么是MVVM模式吧。
## MVVM的理解
MVVM这种模式其实是来自[微软](https://msdn.microsoft.com/en-us/library/hh848246.aspx)的，我们用一张视图来概括一下MVVM吧

![](/images/mvvm.png)
从这个图可以看出，view和View Controller都归纳为View，即V，视图相关的业务逻辑以后就写到这里了，中间的ViewModel用来写MVC中Controller的一部分逻辑，比如网络请求的业务逻辑，视图展示数据的业务逻辑等等，这样以前的Controller的业务逻辑代码岂不是变少了很多，在实际使用当中，Controller的代码也变得非常清晰，因为你只会关注业务逻辑，而不会去关注这些数据怎么来的，怎么组装的等等这些细节了。Model还是以前MVC中的model没有什么变化，然而这三个层级的关系又是如何的呢？View只引用ViewModel，View通过Action来改变ViewModel，ViewMode的业务来更新View，注意ViewModel不要去引用View，ViewModel只是写了一些组装数据的业务逻辑而已，把控好各个层级的关系可以把你的项目写的很清晰。ViewMode中引用Model，ViewModel的网络请求，数据组装等等业务逻辑的结果都保存到了Model中，Model一旦有更新会通知ViewModel，一旦View有需求更新或ViewModel变更需要通知View，这个时候就可以去更新View了。如果理解的简单点，应该就是一个双向绑定的关系，View跟ViewModel绑定，一旦ViewModel有变更，View更新；ViewModel更Model绑定，一旦Model有变更，则会通知ViewModel，继而会更新View。
## MVVM与MVC的对比
MVVM较MVC的好处个人觉得最重要的应该是将臃肿的Controller解放了，有一大部分业务逻辑分解到了ViewModel中来了；其次应该就是做单元测试比较方便。因为很多业务逻辑已经抽象出成单独的方法，很容易做单元测试。当然个人也有一些用MVVM比较麻烦的地方，在一些较简单的Controller中，写MVVM模式，确实又有点累赘，增加了一些代码量。但总体来说，MVVM确实把代码的复杂度降下来了。
## 推荐
说到最后，有一个推荐的框架比较适合MVVM，也是最近在学习的一个框架[ReactiveCocoa](http://roger.farbox.com/post/reactivecocoaxue-xi)
## 最后
写了这些都是自己的个人理解，还并未去实际，若有理解出入，还望谅解。