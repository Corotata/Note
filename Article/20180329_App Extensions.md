title: App Extensions

date: 2018年 3月29日 星期四 10时33分04秒 CST

tags: 

---

# App Extensions
App Extension（应用扩展）是苹果于iOS8就已经推出来的新特性，应用扩展允许开发者扩展应用的自定义功能和内容，能够让用户在使用其他App时使用该项功能。你可以开发一个应用扩展来执行某些特定的任务，用户使用该扩展后就可以在多个上下文环境中执行该任务。苹果提供了很多种应用扩展的模板，今天主要讲开发中最常用的Today Extension，其他的有兴趣的请查阅[官方文档](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/)了解，本文仅通过记录一些开发中，我自己觉得需要注意的地方，其他使用内容请查看推荐文章，再拷一份好像也没啥意思。


## 共享数据的方式
首先需要创建App Group，如下图所示：

![App_Group](7xli4x.com1.z0.glb.clouddn.com
http://7xli4x.com1.z0.glb.clouddn.com/App_Group.png)

而随后所有操作都需要这个key，如group.com.corotata.PhotoExtension



### 使用UserDefaults共享数据
有时，开发中，我们可能在主应用做一些选项设置，而希望扩展则通过这些设置来显示或者隐藏某些内容，那么我们可以这么做。

```Swift
//存
let userDefault = UserDefaults(suiteName: "group.com.corotata.PhotoExtension")
userDefault?.setValue(11, forKey: "number")    
userDefault?.synchronize()

//取
userDefault?.integer(forKey: "number")
```

### 使用数据库共享数据
这边用Realm来举例，你只需要分别在主应用的AppDelegate和扩展的相应ViewController里面注册以下代码，你就会发现这路径和我们平常常打开的沙盒路径不一样，它是应用与扩展共享的路径，然后使用正常的Realm存读数据就可以了。

![AppGroup_SharePath](7xli4x.com1.z0.glb.clouddn.com
http://7xli4x.com1.z0.glb.clouddn.com/AppGroup_SharePath.png)


```
var config = Realm.Configuration()
config.fileURL = FileManager.default
                .containerURL(forSecurityApplicationGroupIdentifier: "group.com.corotata.PhotoExtension")!
                .appendingPathComponent("Library/Caches/default.realm")
Realm.Configuration.defaultConfiguration = config
```

## 代码共用
比如有这么个场景，词典应用有一日一句的模块，希望在应用主页和今日扩展都显示相同的数据，而往往的操作方式都基本一致，就是网络获取下数据，然后将数据缓存起来，然后界面显示，而唯一不同的基本就是UI了，那么我们能怎么处理呢？

1. 相同的逻辑拷一份（不推荐）
2. 将相同的逻辑放到相应的文件，点击文件，在Target Membership中选上（不推荐）
3. 这相应的代码文件封成静态库（推荐，合适单工程）
4. 使用CocoaPod来集成（推荐，适组件化的工程）


## 推荐文章 
1. [官方文档](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/)
2. [Today Extension](https://segmentfault.com/a/1190000010797856)
3. [揭秘 iOS App Extension 开发 —— Today 篇](http://www.cocoachina.com/ios/20160620/16760.html)
4. [官方文档翻译](http://www.cocoachina.com/ios/20141023/10027.html)

