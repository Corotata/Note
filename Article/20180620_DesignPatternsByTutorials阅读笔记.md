title: Design Patterns By Tutorials 阅读笔记
date: 2018年 6月20日 星期三 06时13分11秒 CST
tags: Design Patterns

---

这两天开始阅读Design Patterns By Tutorials，由于这本书只是初版，内容并不是很很多，我竟然翻着翻着就看完了，在这边，我并不打算搬书里面的内容来讲，因为有些例子，说实话，有些感觉真的是为了使用设计模式而使用的，我只记一些对我自己有用处的，开始正文吧。

### Model View Controller Pattern (MVC模式)

####什么是MVC模式？

MVC在iOS编程中非常常见，因为它是Apple选择在UIKit中大量采用的设计模式。

1. 模型持有应用程序数据 他们通常是结构或简单的类。
2. 视图在屏幕上显示视觉元素和控件。 它们通常是UIView的子类。
3. 控制器在模型和视图之间进行协调。 它们通常是UIViewController的子类

#### UML图
![Model-View-Controller-Pattern](http://7xli4x.com1.z0.glb.clouddn.com/Model-View-Controller-Pattern.png)

#### 什么时候使用MVC模式？

好像开发中，一般文件分层就已经是MVC了，所以这个好像也没啥好讲的了，只是往往我们使用时，Model与 ViewController 交互这一层上，我们往往都是直接ViewController修改完数据后直接去刷新UI，而并不像上面图画的，使用通知中心去更新，因为定义太多的通知中心，其实在开发中并不好，特别存在很多页面共用的情况。

### Strategy Pattern (策略者模式)
####什么是策略者模式？

策略模式定义了可以在运行时设置或切换的可互换对象的算法族或行为族，具体来说有以下四点:

1. 在 iOS 应用开发中通常会以ViewController做为策略对象使用该模式时, 但在技术上它可以是任何需要可互换行为的对象，而不限于某一类。
2. 策略者模式通常会定义一个Protocol，声明必须实现的策略方法
3. 所有的策略都必须遵循并实现定义好的策略Protocol
4. 策略者的精髓 - 运行时替换


#### UML图
![Strategy Pattern](http://7xli4x.com1.z0.glb.clouddn.com/Strategy Pattern.png)

#### 什么时候使用策略者模式？

通常有你有两种或者两种可互换的行为时，简单讲就是同一个操作需要有多种不同的实现方式共存时，这时你就可以考虑使用策略者模式了。

举例来说，当你出行去一个地点时，你可以选择不同的交通工具，你可以坐飞机，坐火车或者坐大巴，而这三种方式，就可以考虑为三种出行策略。

再举例iOS中，比如你要将一个对象缓存起来，你可以考虑用归档，可以考虑用数据库，可以考虑存文件，他们有共同的动作就是将内容存储起来，而你可以通过三种不同的实现方式。

*Strategy Pattern*与*Delegate pattern*它们都是依赖Protocol而不是具体对象来提高灵活性。因此，任何实现策略协议的对象都可以在动作时被当作一个策略来实用。

和*Delegate pattern*不同的是，*Strategy Pattern*通常是以一个类群的方式展示。*Delegate pattern*，举例来说，UITableView的dataSource和delegate可以从Interface Builder中设置，一经设定，在运行时将很少再去改变它。然而*Strategy Pattern*的设计旨在运行时容易互换。

