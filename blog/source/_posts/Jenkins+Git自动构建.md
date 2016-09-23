---
date: 2016-08-25 17:52
status: public
title: Jenkins+Git自动构建
category: 工具
---

## 前言
   想想以前，发给测试的测试包都是通过手动使用XCode来打包，如果项目工程比较大，Archive过程时间还是比较长的，打多了觉得特没劲，非常浪费时间，特别是使用了敏捷开发流程的，要是还用手动打包，估计你在打包上耗得时间都够你解决几个bug了。敏捷开发也是强调使用自动化的，所以也少不了自动构建的环节，那么接下来说说如何使用Jenkins+Git来自动构建我们的项目，从而方便测试，也让开发有更多的经历投入到编码和业务中而不是浪费在这些无用的流程中。
## 一.Jenkins安装
1.下载最新的版本（一个 WAR 文件）。[Jenkins官方网址](http://Jenkins-ci.org/)
2.运行 Java -jar jenkins.war 。
如果你的java环节低于java7 它会提示你升级java环节，你可以到[这里下载](https://www.java.com/en/download/)，安装最新的java环境。
如果一切顺利，那么只要在你本机浏览器中输入[127.0.0.1:8080](http://127.0.0.1:8080)即可看到Jenkins的主页面。
![](/images/jenins首页.png)
## 二.Jenkins配置
1.先新建一个项目。
![](/images/创建项目.png)
2.输入项目名称和构建风格。
![](/images/创建项目1.png)
3.项目配置

![](/images/配置项目-项目名称.png)
4.源码管理

![](/images/项目配置-源码管理.png)
这一步输入你项目在Git上的仓库地址，这里的授权有两种形式，一种是使用http登录，一种是使用ssh授权；
*如果使用http授权的看下图：

![](/images/项目配置-源码管理添加http授权.png)
这里输入你git上的登录名和密码。
*如果使用SSH授权看下图：

![](/images/项目配置-源码管理SSH授权创建.png)
kind要选择SSH Username with private key,Username可以随便输入，一个描述而已，Private Key选Enter directly，然后在Key中输入你在Git上的秘钥，这个秘钥的制作在我前面的[博客](http://roger.farbox.com/post/macshang-zhi-zuo-sshmi-yao)中有提到，这里就不多说了。最后点击Add，SSH的授权就制作OK了。
5.构建
源码可以下载了，那么接下来就是如何去生成一个ipa包了，我们通过构建的方式来实现，接下来我们来配置一下构建流程，构建的方式很多中，这里讲讲比较简单的一种，使用shell来实现。

![](/images/构建1.png)

![](/images/项目配置-构建配置.png)
编写脚本，这个脚本支持的是workspace的，如果没有使用workspace的，问题不大，稍作修改即可。脚本：
```
cd $WORKSPACE
SDK_VERSION="iphoneos8.3"

PROJECT_NAME="JenkinsDemo"
PROVISION_UUID="你的描述文件的UUID，可以通过在XCode选中描述文件ctr+c的方法获取到"
PROVISONING_PROFILE="JenkinsDemo_Distribution"

XCODE_PRJ="xcodeproj"
FILE_EXTENSION='xcworkspace'
PROJECT_DIR=`pwd`
CD_XCODE_PRJ=`pwd`/$PROJECT_NAME.$XCODE_PRJ
PROJECT_WORKSPACE=$PROJECT_DIR.
PROJECT_BUILD=`pwd`/$PROJECT_NAME/BUILD

APP_PATH="build/Release-iphoneos/${PROJECT_NAME}.app"
TARGET_APP_PATH="build/Release-iphoneos/${PROJECT_NAME}.ipa"
ARCHIVEPATH="build/Release-iphoneos/${PROJECT_NAME}.xcarchive"

cd "$PROJECT_DIR"

cd ..
cd "$CD_XCODE_PRJ"
sed -i '' "s/\(PROVISIONING_PROFILE.=.\"\).*\(\"\)/\1$PROVISION_UUID\2/g" project.pbxproj
cd ..
rm -rf build
xcodebuild -workspace $PROJECT_DIR/$PROJECT_NAME.$FILE_EXTENSION -scheme $PROJECT_NAME -destination generic/platform=iOS archive -archivePath $ARCHIVEPATH
xcodebuild -exportArchive -exportFormat IPA -archivePath $ARCHIVEPATH -exportPath $TARGET_APP_PATH -exportProvisioningProfile "${PROVISONING_PROFILE}"
curl -F "file=@build/Release-iphoneos/JenkinsDemo.ipa" -F "uKey=蒲公英上的uKey" -F "_api_key=蒲公英上的apikey" https://www.pgyer.com/apiv1/app/upload
```
*构建成功后这里可能会出现一个问题，你的项目工程中的配置需要设置一下shared，不然构建的会一直报错。找不到工程文件，如下图设置：

![](/images/工程配置.png)
*OK，保存构建后，接下来就可以回到项目管理页面了。

![](/images/项目管理-立即构建.png)
*一般构建在一分钟左右，先下载源代码，然后打包，最后将ipa包上传到蒲公英平台。
*可以看出，构建后蓝色的表示构建成功，红色和灰色表示构建失败，可以点击相应的图标，查看构建日志。
