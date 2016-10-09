---
title: 使用Cordova搭建混合框架
date: 2016-09-23 14:22:31
tags: Hybrid App
category: 框架

---

## 前言
  入职公司不久，花了一个多月开发了一个原生APP，这也只是个开始，首先强调的是，我们公司并不是外包公司，但需要开发的项目其实还挺多的，为了解决这个问题，我们需要周期短，响应快，来满足公司的需求，我们只能抛弃纯原生APP的开发方案，选择目前市场比较热门的技术方案：*Hybrid App*。使用*Hybrid App*的技术框架其实有很多开源的技术方案，比如使用[Cordova](http://cordova.apache.org)，[React Native](http://reactnative.cn)，等。这两个技术方案其实都还不错，由于我们公司有人以前有使用*Cordova*框架的经验，所以我们选择了*Cordova*,这个理由听起来感觉有些过不去，但有时候领导就是喜欢听取有经验的方案。不过走到现在确实觉得*Cordova*也很不错,没出什么问题，也都能满足项目上的需求。
  
## 搭建框架
 接下来我们来搭建一个*Hybrid App*试试手吧，创建一个`MyHybridDemo`，使用`Pod`来管理*Cordova*框架，可以通过命令`pod 'Cordova'`从Github上下载*Cordova*，集成*Cordova*到工程中后，还缺html5与原生代码交互的桥梁，需要一些js的支撑，你可以查看[官网](http://cordova.apache.org/docs/en/latest/guide/cli/index.html)的文档，一步一步创建相应文件。也可以直接从我的[MyHybridDemo](https://github.com/lyroger/MyHybridDemo)中拷贝www文件夹和config.xml文件集成到你的工程中。到这里集成框架算是ok了，接下来就是写业务代码了。在写业务代码之前首先来介绍一下www目录下的文件和config.xml文件的作用。
 使用终端cd到www目录下，使用`tree -L 4`（需安装tree工具）命令查看www目录：
 ```js
.
├── assets
│   ├── images
│   ├── js
│   └── style
├── cordova-js-src
│   ├── exec.js
│   └── platform.js
├── cordova.js
├── cordova_plugins.js
├── index.html
└── plugins
    ├── com.exmind.photopickerplugin
 ```
* `assets`:该目录是用来放资源文件的，比如h5相关的业务图片，视频等资源文件。
* `cordova-js-src`:该目录是存放cordova的js框架代码，这个你直接拷贝过来就ok了，不用多管。
* `cordova.js`:该文件是cordova的js框架代码。直接拷贝。
* `cordova_plugins.js`:该js文件是cordova来定义js插件文件，他包含定义插件的文件的路径，插件ID，以及插件名称。最终获取一个插件集合。
* `plugins`:该文件夹是js调用原生插件的入口，所有插件可以放到此文件夹中。比如拍照，定位，分享等插件js。
* 最后还有一个config.xml文件,该xml文件主要是用作配置属性的。可以配置h5入口，也可以配置UIWebView的属性等。

## 插件的配置和定义
  了解了框架后，来实战一下，用例就拿web端调用APP的系统相机和相册吧。
  
  一. 首先我们用`Objective-C`原生代码写好调用系统相机和相册的方法。具体实现我就不写了，需要注意的一点就是该类需要继承Cordova的`CDVPlugin`类，调用的插件方法需要传一个`CDVInvokedUrlCommand`类型的参数，以便回调。
  ```objective-c
@interface PhotoPickerPlugin : CDVPlugin
// 获取图片
- (void)getPictures:(CDVInvokedUrlCommand *)command;
// 删除图片文件
- (void)deleteFile:(CDVInvokedUrlCommand *)command;
@end
  ```
  
  二. 写完原生代码后，如何让web端调起我们的代码呢？框架搭建好后，其实只要稍作配置就ok了，你只需要动动四个文件。
  1. 第一个就是config.xml，这个是配置你的插件名称，告诉Cordova你定义了这么个插件类（这里我们定义的是：`PhotoPickerPlugin`）。  
  2. 第二个文件:添加一个`PhotoPickerPlugin.js`插件类，供web端调用的。
 ```js 
cordova.define("com.exmind.photopickerplugin.PhotoPickerPlugin", function(require, exports, module) {
           
   var argscheck = require('cordova/argscheck');
   var exec = require('cordova/exec');
   var PhotoPickerPlugin = function() {
   
   };
   // 获取系统相册图片
   PhotoPickerPlugin.prototype.getPictures = function(success, fail, maxCount) {
       exec(success, fail, "PhotoPickerPlugin", "getPictures", [maxCount]);
   };
   // 删除图片
   PhotoPickerPlugin.prototype.deleteFile = function(success, fail, filePaths) {
       exec(success, fail, "PhotoPickerPlugin", "deleteFile", filePaths);
   };
   
   var me = new PhotoPickerPlugin();
   module.exports = me;
});
 ```
  3. 第三个文件：`cordova_plugins.js`，在这个文件中你需要定义插件js插件类以及他的路径和唯一标示。
 ```js 
{
  "file": "plugins/com.exmind.photopickerplugin/www/PhotoPickerPlugin.js",
  "id": "com.exmind.photopickerplugin.PhotoPickerPlugin",
  "clobbers": [
               "PhotoPickerPlugin"
               ]
}
 ```
  4. 最后一个文件就是在使用该插件的地方引用`cordova_plugins.js` 和 `cordova.js`。
  
修改好这四个文件后，js代码就可以直接调用`PhotoPickerPlugin`的`getPictures:`,`deleteFile:`方法了。
调用方式:
```js
PhotoPickerPlugin. getPictures(function (success) {}, function (fail) {},6);
```
如何让原生代码返回数据给web端呢？web端拿到success，和fail回调，原生代码如何将数据组织到这个回调用呢？我们回到`PhotoPickerPlugin.m`类中，来看看实现。
```objective-c
- (void)imagePickerController:(TZImagePickerController *)picker didFinishPickingPhotos:(NSArray<UIImage *> *)photos sourceAssets:(NSArray *)assets isSelectOriginalPhoto:(BOOL)isSelectOriginalPhoto
{
    CDVPluginResult* result = nil;
    NSMutableArray *resultStrings = [[NSMutableArray alloc] init];
    NSData* data = nil;
    NSError* err = nil;
    NSFileManager* fileMgr = [[NSFileManager alloc] init];
    NSString* filePath;
    
    for (UIImage *image in photos) {
        int i = 1;
        do {
            filePath = [NSString stringWithFormat:@"%@/%@%04d.%@", [self getFileDocPath], @"cdv_photo_", i++, @"jpg"];
        } while ([fileMgr fileExistsAtPath:filePath]);
        
        @autoreleasepool {
            data = UIImageJPEGRepresentation(image,0.5);
            if (![data writeToFile:filePath options:NSAtomicWrite error:&err]) {
                result = [CDVPluginResult resultWithStatus:CDVCommandStatus_IO_EXCEPTION messageAsString:[err localizedDescription]];
                break;
            } else {
                [resultStrings addObject:[[NSURL fileURLWithPath:filePath] absoluteString]];
            }
        }
    }
    result = [CDVPluginResult resultWithStatus:CDVCommandStatus_OK messageAsArray:resultStrings];
    [self.commandDelegate sendPluginResult:result callbackId:self.pCommand.callbackId];
}
```

看最后一句代码，便是将结果通过`CDVPluginResult`类发送给对应的command。
到此我们应该掌握了如何从web端调用App端的代码了。在其他场景，其实还需要App端调用web端的方法，那如何调用呢？熟悉UIWebView的同学应该都知道UIWebView中有一个`stringByEvaluatingJavaScriptFromString:`方法。对，你可以使用该方法调用js方法，不过cordova也封装了一个方法供你使用，你可以通过`CDVCommandDelegateImpl`类的`evalJs:`方法实现调用js代码。