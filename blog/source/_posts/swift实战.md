---
date: 2016-07-18 11:26
status: public
title: swift实战
category: swift
tag: swift
---

## OC调用swift方式
在OC代码中引入头文件 #import "项目名称-swift.h"
即可用OC语法调用swift写的代码了。
## swift中如何写Block
在swift中定义一个闭包的关键词为 `typealias`
在A类中定义一个闭包
```swift
//定义一个闭包
typealias LoginSuceesedBlock = (successed:Bool)->Void
class LoginViewController:UIViewController{
    //将函数指针赋值给loginActionBlock闭包
    func initWithBlock(closure:LoginSuceesedBlock?){
        loginActionBlock = closure
    }
    private func loginAction(sender:AnyObject?) {
        let loginSuccessed:Bool = loginViewModel.loginRequestAction(textUserName.text!,passWord: textPwd.text!)
        if loginSuccessed {
            //block 回调
            if (loginActionBlock != nil) {
                self.loginActionBlock!(successed: true)
            }
        } else {
            let alert:UIAlertView =  UIAlertView()
            alert.title = "提示"
            alert.message = "登录失败"
            alert.addButtonWithTitle("OK")
            alert.show()
        }
    }
}
```
在B类中传一个函数到A类
```swift
func authorizeOperation() {
        let loginVC = LoginViewController()
        loginVC.initWithBlock(loginActionSuccessed)
        self.navigationController?.pushViewController(loginVC, animated: true)
    }
func loginActionSuccessed(successed:Bool) -> Void {
        if successed {
            //todo loginSuccessed something
        }
    }
```

