---
layout:     post
title:      My travel dairy
subtitle:   I belong with the world
date:       2017-07-26
author:     chenxi
header-img: img/article2.jpeg
catalog: true
tags:
    - Photogragh
    - Travel
    - Life
---


## CHINA

--Beijing<br/>
I was born in Beijing and spentover 20 years in this city. But not enough for me. This is a city full of energy and multiculture. Everyday there're some incredible things happen on this ground. Excited and proud every time I introduce my hometown.
![alt text](/img/china1.jpeg "CHINA")![alt text](/img/china2.jpeg "CHINA")


### France

我最近的项目中，退出登录后（跳转到登录页），发现首页控制器没有被销毁，依旧能接收通知。

退出登录代码：

```objc
UIStoryboard *storyboard = [UIStoryboard storyboardWithName:@"Login" bundle:[NSBundle mainBundle]];
AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.window.rootViewController = [storyboard instantiateViewControllerWithIdentifier:@"LoginVC"];
```

很明显发生了循环引用导致的内测泄漏。

接下来就使用 **Debug Memory Graph** 来查看内测泄漏了。

### 运行程序

首先启动 Xcode 运行程序。

### Debug Memory Graph

![](https://ws3.sinaimg.cn/large/006tNc79gy1fhxend1a8aj315y0s3gw5.jpg)

点击 Debug Memory Graph 按钮后，可以看到红框内的是当前内存中存在的对象。其中，绿色的就是视图控制器。

这样，我们随时都可以查看内测中存在的对象，换句话说，就是可以通过观察 Memory Graph 查看内测泄漏。

### 调试你的App

继续运行你的程序

![](https://ws2.sinaimg.cn/large/006tNc79gy1fhxeuh1np5j30v90kvn03.jpg)

然后对App进行调试、push、pop 操作，再次点击 Debug Memory Graph 按钮。那些该释放而依旧在内测中的 `控制器` 或 `对象` 就能一一找出来了。

接下来，只要进入对应的控制器找到内测泄漏的代码就OK了，一般是Block里引用了 `self`，改为 `weakSelf` 就解决了。

```objc
#define WS(weakSelf) __weak __typeof(&*self)weakSelf = self;

WS(weakSelf)
sView.btnBlock = ^(NSInteger idx){
    [weakSelf.tableView reloadSections:[NSIndexSet indexSetWithIndex:idx] withRowAnimation:UITableViewRowAnimationAutomatic];
};
```

## 结语

就这样，利用 Debug Memory Graph，可以简单快速的检测内测泄漏。

一般由两个对象循环引用的内测泄漏是比较好发现的，如果是由三个及其三个以上的对象形成的大的循环引用，就会比较难排查了。
