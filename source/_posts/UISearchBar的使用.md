---
date: 2015-10-14 13:18
status: public
title: UISearchBar的使用
category: 基础篇
---

最近在项目中写搜索比较多，然而UI设计对页面的效果和布局都有所严谨，发布测试版本后设计或多或少会拿着app跑到面前来说这些差那些，作为开发人员，这些都习以为常了。

```
简介的介绍一下，此文主要想要介绍的内容：在搜索框激活时，出现的并不是遮罩层，而是想利用遮罩层做些其他事情，比如提示语、历史搜索记录等需求。
```
为达到此功能，我使用了两个控件:
 
``` objective-c
1. UISearchBar
2. UISearchDisplayController
```
然而使用这两个控件的原始效果我们可以看看iPhone手机短信或通讯录中的搜索效果，我截了一张短信搜索图,如下图：
![](/images/iOS自带效果.png)

搜索框激活的时候自带了一层遮罩的效果，但很多时候，设计需求是有各式各样的变化的，可能你不需要这层遮罩层，而是想利用这个空间来做其他的用途，比如一旦搜索框激活，在遮罩层处，我想把这个空间用来做提示，或做历史搜索记录等需求，我们先来看看微信的搜索是怎么样的吧！

![](/images/微信效果.png)
这个效果不错吧，充分利用了空间，将遮罩层替换成了提示语，“搜索更多内容”，点击提示还是会让UISearchBar失去激活效果，虽然不知道微信是否是使用了UISearchBar和UISearchDisplayController来达到此效果的，但使用这两个控件肯定能实现这个效果。
## 先来了解UISearchBar
UISearchBar主要是用来输入搜索词，系统提供的UISearchBar自定义的方式并不是很友好，扩展性并不理想，想要充分了解UISearchBar，我们先来看看UISearchBar的堆栈结构吧。
```objective-c
(lldb) po [mySearchBar recursiveDescription]
<UISearchBar: 0x13f518bc0; frame = (0 0; 320 40); text = ''; gestureRecognizers = <NSArray: 0x17005cfb0>; layer = <CALayer: 0x1700297e0>>
   | <UIView: 0x17018db60; frame = (0 0; 320 40); clipsToBounds = YES; autoresize = W+H; layer = <CALayer: 0x170034560>>
   |    | <UISearchBarBackground: 0x13f611e10; frame = (0 0; 320 40); opaque = NO; userInteractionEnabled = NO; layer = <CALayer: 0x178039920>>
   |    | <UISearchBarTextField: 0x13f51a170; frame = (0 0; 0 0); text = ''; clipsToBounds = YES; opaque = NO; layer = <CALayer: 0x170034620>>
   |    |    | <_UISearchBarSearchFieldBackgroundView: 0x13f612fa0; frame = (0 0; 0 0); opaque = NO; autoresize = W+H; userInteractionEnabled = NO; layer = <CALayer: 0x178037fc0>>
   ```
我们打印出UISearchBar的堆栈结构后，我们可以清晰的看出它是由一个UIView组成，这个UIView下又有一个UISearchBarBackground和一个UISearchBarTextField，UISearchBarTextField下还有一个_UISearchBarSearchFieldBackgroundView。这些UIView并没有被UISearchBar   提供，所以你想自定义UISearchBar就得从这些视图入手，下面我修改了UISearchBarTextField的背景色，如：
```objective-c
mySearchBar = [[UISearchBar alloc] initWithFrame:CGRectMake(0, 0, mScreenWidth, 40)];
    mySearchBar.delegate = self;
    mySearchBar.barTintColor = [UIColor whiteColor];
    [mySearchBar setPlaceholder:@"搜索"];
    UIColor *bgColor = [UIColor colorWithRed:0xff green:0xff blue:0xff alpha:1];
    mySearchBar.backgroundImage = [self imageFromColor:bgColor frame:mySearchBar.bounds];
    for (UIView *subView in mySearchBar.subviews)
    {
        for (UIView *secondLevelSubview in subView.subviews){
            if ([secondLevelSubview isKindOfClass:[UITextField class]])
            {
                UITextField *searchBarTextField = (UITextField *)secondLevelSubview;                searchBarTextField.backgroundColor = GreyishWhiteColor;
                break;
            }
        }
    }
```
在for循环里 我找到了UISearchBarTextField，并改变了他的背景色。这里不是这篇文章主要讲解的，自定义UISearchBar就不过多讲了。
## 我们再来了解下UISearchDisplayController
只有在激活的情况下，UISearchDisplayController才会显示出来。
```objective-c
searchDisplayController = [[UISearchDisplayController alloc] initWithSearchBar:mySearchBar contentsController:self];
    searchDisplayController.searchResultsDataSource = self;
    searchDisplayController.searchResultsDelegate = self;
    searchDisplayController.delegate = self;
```
在这里先初始化controller,初始化的时候注意了，将先前初始化的UISearchBar作为初始化参数穿进去了，这个时候其实就把UISearchBar和UISearchDisplayController关联起来了。搜索激活时，我们将会看到上面提到的遮罩层，其实这就是UISearchDisplayController下面的_UISearchDisplayControllerDimmingView,为什么这么说呢，我们用老方法吧，使用recursiveDescription 把堆栈结构打印出来

```
po [searchDisplayController recursiveDescription]
error: Execution was interrupted, reason: Attempted to dereference an invalid ObjC Object or send it an unrecognized selector.
The process has been returned to the state before expression evaluation.
```
发现他并不是UIView的子类，我们跟进去查看一下，原来是继承NSObject的，那他的所呈现的UIView是放在什么视图上面的呢，我们看看他的属性，发现还有一个searchContentsController，那么我们把searchContentsController堆栈打印出来看看
```objective-c
(lldb) po [controller.searchContentsController.view recursiveDescription]
<UIView: 0x1781971b0; frame = (0 0; 320 568); autoresize = W+H; layer = <CALayer: 0x178228fa0>>
   | <UITableView: 0x14c867a00; frame = (0 0; 320 454); clipsToBounds = YES; gestureRecognizers = <NSArray: 0x17805d190>; animations = { bounds.origin=<CABasicAnimation: 0x17022e1c0>; bounds.size=<CABasicAnimation: 0x17022e220>; }; layer = <CALayer: 0x17803c380>; contentOffset: {0, -20}; contentSize: {320, 478}>
   |    | <UIView: 0x178197760; frame = (0 -0.5; 320 0.5); hidden = YES; autoresize = W; layer = <CALayer: 0x17822a3a0>>
   |    | <UITableViewWrapperView: 0x14c5137c0; frame = (0 0; 320 454); gestureRecognizers = <NSArray: 0x17805d3d0>; layer = <CALayer: 0x17822a2c0>; contentOffset: {0, 0}; contentSize: {320, 454}>
   |    | <UISearchBar: 0x14c514cc0; frame = (0 0; 320 44); text = ''; gestureRecognizers = <NSArray: 0x17805cf50>; layer = <CALayer: 0x17822a000>>
   |    |    | <UIView: 0x1781975c0; frame = (0 0; 320 44); autoresize = W+H; layer = <CALayer: 0x17822a0c0>>
   |    |    |    | <UISearchBarBackground: 0x14c5122b0; frame = (0 -20; 320 64); opaque = NO; userInteractionEnabled = NO; animations = { position=<CABasicAnimation: 0x17022f140>; bounds.origin=<CABasicAnimation: 0x17022f380>; bounds.size=<CABasicAnimation: 0x17022f3e0>; contentsCenter=<CABasicAnimation: 0x17022f4c0>; }; layer = <CALayer: 0x17822a160>>
   |    |    |    | <UISearchBarTextField: 0x14c518b40; frame = (8 8; 239 28); text = ''; clipsToBounds = YES; opaque = NO; gestureRecognizers = <NSArray: 0x17805d970>; animations = { position=<CABasicAnimation: 0x17022f560>; bounds.origin=<CABasicAnimation: 0x17022f6a0>; bounds.size=<CABasicAnimation: 0x17022f700>; }; layer = <CALayer: 0x17003e500>>
   |    |    |    |    | <_UISearchBarSearchFieldBackgroundView: 0x14c617ff0; frame = (0 0; 239 28); opaque = NO; autoresize = W+H; userInteractionEnabled = NO; animations = { position=<CABasicAnimation: 0x17022f740>; bounds.origin=<CABasicAnimation: 0x17822bea0>; bounds.size=<CABasicAnimation: 0x17822bda0>; }; layer = <CALayer: 0x170225de0>>
   |    |    |    |    | <UISearchBarTextFieldLabel: 0x14c618950; frame = (29 1; 203 25); text = '搜索'; opaque = NO; userInteractionEnabled = NO; animations = { position=<CABasicAnimation: 0x17822c560>; bounds.origin=<CABasicAnimation: 0x17822c6a0>; bounds.size=<CABasicAnimation: 0x17822c700>; }; layer = <_UILabelLayer: 0x170087f80>>
   |    |    |    |    |    | <_UILabelContentLayer: 0x17822b240> (layer)
   |    |    |    |    | <UIFieldEditor: 0x14c522e10; frame = (28 2; 204 24); text = ''; clipsToBounds = YES; opaque = NO; gestureRecognizers = <NSArray: 0x17805a3d0>; layer = <CALayer: 0x1782285e0>; contentOffset: {0, 0}; contentSize: {204, 24}>
   |    |    |    |    |    | <_UIFieldEditorContentView: 0x1781981f0; frame = (0 0; 204 24); opaque = NO; userInteractionEnabled = NO; gestureRecognizers = <NSArray: 0x1702441d0>; layer = <CALayer: 0x178227d40>>
   |    |    |    |    |    |    | <UITextSelectionView: 0x14c50e2d0; frame = (0 0; 0 0); userInteractionEnabled = NO; layer = <CALayer: 0x178227aa0>>
   |    |    |    |    |    | <UIImageView: 0x1701ef500; frame = (200.5 12; 3.5 12); alpha = 0; opaque = NO; autoresize = LM; userInteractionEnabled = NO; layer = <CALayer: 0x17022f900>>
   |    |    |    |    |    | <UIImageView: 0x1781ef200; frame = (197 20.5; 7 3.5); alpha = 0; opaque = NO; autoresize = TM; userInteractionEnabled = NO; layer = <CALayer: 0x17822ca60>>
   |    |    |    |    | <UIImageView: 0x1701ee700; frame = (8 7.5; 13 13); opaque = NO; userInteractionEnabled = NO; animations = { position=<CABasicAnimation: 0x17822c0e0>; }; layer = <CALayer: 0x17022a3c0>>
   |    |    |    | <UINavigationButton: 0x14c615af0; frame = (258 6; 54 30); opaque = NO; animations = { position=<CABasicAnimation: 0x17822bc60>; opacity=<CABasicAnimation: 0x17822bf60>; }; layer = <CALayer: 0x17003d180>>
   |    |    |    |    | <UIButtonLabel: 0x14c60e1a0; frame = (0 4; 53.5 20.5); text = 'Cancel'; opaque = NO; userInteractionEnabled = NO; layer = <_UILabelLayer: 0x170087da0>>
   |    | <UIView: 0x170198600; frame = (0 44; 320 0); animations = { position=<CABasicAnimation: 0x17022e180>; }; layer = <CALayer: 0x17022aa00>>
   | <UISearchDisplayControllerContainerView: 0x1701efe00; frame = (0 0; 320 568); autoresize = W+H; layer = <CALayer: 0x17022d620>>
   |    | <UIView: 0x170196e70; frame = (0 0; 320 568); layer = <CALayer: 0x17022d800>>
   |    |    | <UISearchResultsTableView: 0x14d046200; frame = (0 0; 320 568); clipsToBounds = YES; hidden = YES; autoresize = W+H; gestureRecognizers = <NSArray: 0x170240450>; layer = <CALayer: 0x17022aa20>; contentOffset: {0, 0}; contentSize: {320, 44}>
   |    |    |    | <UITableViewWrapperView: 0x14c6198f0; frame = (0 0; 320 568); gestureRecognizers = <NSArray: 0x170240690>; layer = <CALayer: 0x17022ab00>; contentOffset: {0, 0}; contentSize: {320, 568}>
   |    |    |    |    | <UITableViewCell: 0x14c60fe10; frame = (0 0; 320 44); text = '搜索第一行'; autoresize = W; layer = <CALayer: 0x170225ac0>>
   |    |    |    |    |    | <UITableViewCellContentView: 0x178196b30; frame = (0 0; 320 43.5); gestureRecognizers = <NSArray: 0x17805a760>; layer = <CALayer: 0x17803a680>>
   |    |    |    |    |    |    | <UITableViewLabel: 0x14c5049b0; frame = (15 0; 290 43.5); text = '搜索第一行'; userInteractionEnabled = NO; layer = <_UILabelLayer: 0x178084f10>>
   |    |    |    |    |    |    |    | <_UILabelContentLayer: 0x17003e820> (layer)
   |    |    |    |    |    | <_UITableViewCellSeparatorView: 0x1781c3840; frame = (15 43.5; 305 0.5); layer = <CALayer: 0x17822bb20>>
   |    |    |    | <UIImageView: 0x1701f0800; frame = (0 564.5; 320 3.5); alpha = 0; opaque = NO; autoresize = TM; userInteractionEnabled = NO; layer = <CALayer: 0x17022e6a0>>
   |    |    |    | <UIImageView: 0x1701f0900; frame = (316.5 524; 3.5 44); alpha = 0; opaque = NO; autoresize = LM; userInteractionEnabled = NO; layer = <CALayer: 0x17022e880>>
   |    | <UIView: 0x170197eb0; frame = (0 20; 320 44); animations = { position=<CABasicAnimation: 0x17022f0c0>; }; layer = <CALayer: 0x17022d7c0>>
   |    | <UIView: 0x170197f80; frame = (0 64; 320 504); animations = { bounds.origin=<CABasicAnimation: 0x17022f100>; bounds.size=<CABasicAnimation: 0x17022f160>; position=<CABasicAnimation: 0x17022f220>; }; layer = <CALayer: 0x17022d7e0>>
   |    |    | <_UISearchDisplayControllerDimmingView: 0x1701c3840; frame = (0 0; 320 504); alpha = 0.4; opaque = NO; autoresize = W+H; animations = { position=<CABasicAnimation: 0x17022f1a0>; bounds.origin=<CABasicAnimation: 0x17022f2c0>; bounds.size=<CABasicAnimation: 0x17022f320>; opacity=<CABasicAnimation: 0x17822c8c0>; }; layer = <CALayer: 0x17022df60>>
```
打印出来的东西有点多，不要着急，要了解清楚这里面的结构还需要耐心和细心，我们先找到UISearchDisplayControllerContainerView，这个view下面有三个子view，其中一个子view中加载着UISearchResultsTableView，这个UISearchResultsTableView是估计大家都很熟悉吧，搜索出来的数据就是加载到这个UISearchResultsTableView上的，然后我们再仔细看看，三个子view中还有一个view下加载着_UISearchDisplayControllerDimmingView，我们试图在这个view上加一些东西，看看会有什么变化,我这里把预先初始化好了的提示页面加载到这个view上，代码如下：
```objective-c
- (void)addTipsViewWithController：(UISearchDisplayController*)controller
{
    UIView *supV = controller.searchResultsTableView.superview;
    UIView *supsupV = supV.superview;
    for (UIView *view in supsupV.subviews) {
        for (UIView *sencondView in view.subviews) {
            if ([sencondView isKindOfClass:[NSClassFromString(@"_UISearchDisplayControllerDimmingView") class]]) {
                NSLog(@"_UISearchDisplayControllerDimmingView");
                if (![sencondView viewWithTag:99]) {
                    [sencondView addSubview:self.tempSearchDisplayBackgroungView];
                }
                sencondView.alpha = 1;
            }
        }
    }
}
```
或许你的 NSLog(@"_UISearchDisplayControllerDimmingView");日志重来就没打印出来，不用着急，你可以延时加载此view
```objective-c
- (void)searchDisplayControllerWillBeginSearch:(UISearchDisplayController *)controller
{
    [self performSelector:@selector(addTipsViewWithView:) withObject:controller afterDelay:0.1];
}
```
这样就没问题了。然后我们来看看效果，是不是发现阴影层上有了我们自己加的页面呢？我们再修改下自己定义的提示页面，设置frame的大小为阴影层的大小，放在阴影层上，但每次都需要将阴影层的alpha设为1，如果每次设置，第二次以及后面搜索时，你加载的提示页面讲是透明的。最后来看看我的效果见下图：

![](/images/最终效果.png)


## 总结
遇到一些扩展性不是很强，或者布局复杂都不要着急，一定要静下心来慢慢查看，熟悉使用UIView的recursiveDescription，强调一定是UIView对象，如果觉得recursiveDescription打印出来的不够直观，推荐使用工具Reveal工具，不过他不是免费的哦。这款软件能直观立体的将试图展示出来，很快的定位试图结构。如有需要该文中提到的相关工程，可以到[这里][mark_SearchBar]下载。还有其他更好的方法，也希望大家多多交流。祝好！
[mark_SearchBar]: https://github.com/lyroger/SearchBarPro (SearchBarPro)
-------
关注我的「lyroger」微信公众号：

![](/images/qrcode_for_gh_6f53ca8d5aea_258.jpg)