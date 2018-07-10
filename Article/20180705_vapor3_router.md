title: Vapor3中的Router
date: 2018年 7月 5日 星期四 12时48分27秒 CST
tags: 
---


Vapor中的Route，其实就是拦截器，所在的文件为routes.swift，主要方法为**public func routes(_ router: Router) throws**

#### 处理不带参数get请求
下面给出一个经典的例子，所有编程都会来的一个hello world:

```swift
public func routes(_ router: Router) throws {
    // Basic "Hello, world!" example
    router.get("hello") { req in
    return "Hello, world!"
}
```
上面表达的意思是，当如果有人用get请求访问接口如**http://api.corotata.com//hello**时，就会被此处的代码所拦截，它处理后返回了"Hello, world!"

#### 处理带子级参数的get请求

例：访问**http://api.corotata.com/hello/corotata**,获取到corotata,并返回Hello,corotata!

```swift
public func routes(_ router: Router) throws {
    router.get("hello",String.parameter) { req -> String in
        guard let name = req.parameters.values.first?.value else {
            return ""
        }
        return "Hello, \(name)!"
    }    
}
```
值得注意的是，这种方式仅能取得hello的后面一级内容，如果你写成**http://api.corotata.com/hello/corotata/123**,那么这将无法被上面的请求响应到了。


#### 处理带参数的get请求
当用户的请求带有参数时，如**http://api.corotata.com/inf?name=sdfsdf&no=1000**，那么我们可以这样写:

```swift
public func routes(_ router: Router) throws {
    router.get("info") { (request) -> String in
        guard let name = request.query[String.self,at: "name"],let no = request.query[Int.self,at: "no" ] else {
            return ""
        }
        return "hello \(name),your number is \(no)"
    }
}
```

#### 处理不带参数的post请求

```swift
public func routes(_ router: Router) throws {
    // Basic "Hello, world!" example
    router.post("hello") { req in
    return "Hello, world!"
}
```

#### 处理带参数的post请求
提交方式可以为form-encoded也可以为JSON-encoded
如下用户采用post的方式来提交参数**name=sdfsdf&no=1000**，我们无法像处理get那样子，通过**request.query**来获取参数，但我们可以这么写；

```
public func routes(_ router: Router) throws {
        router.post("info") { (request) -> String in
            let name = try request.content.syncGet(String.self, at: "name")
            let no = try request.content.syncGet(Int.self, at: "no")
            return "hello \(name),your number is \(no)"
        }
    }
```
另外，从api发现了还有这么个函数,

```swift
public func post<C, T>(_ content: C.Type, at path: PathComponentsRepresentable..., 
use closure: @escaping (Request, C) throws -> T) -> Route<Responder>
        where C: RequestDecodable, T: ResponseEncodable
```

其中要求我们传入content，它遵循RequestDecodable.Protocol，所以我们可以这么来写：

```swift
struct InfoData:Content {
    let name: String
    let no: Int
}

public func routes(_ router: Router) throws {
    router.post(InfoData.self, at: "info") { (request, infodata) -> String in
        //在这里，我们就可以拿到解析好的infodata了
        return "hello \(infodata.name),your number is \(infodata.no)"
    }
}
```

#### 如何返回JSON数据
由上面的例子，我们可以通过Content来将JSON转化为对象，同理我们也可以通过它将对象转化为json,当你直接返回一个遵循Content的对象时，它将会输出成JSON对象：

```swift
struct InfoData:Content {
    let name: String
    let no: Int
}
struct InfoResponse: Content {
    let data: InfoData
}
public func routes(_ router: Router) throws {
    router.post(InfoData.self, at: "info") { (request, infodata) -> InfoResponse in
        //在这里，我们就可以拿到解析好的infodata了
        return InfoResponse(data: infodata)
    }
}
```

访问接口，我们将会得到：

```
{
    "data": {
        "name": "sdfsdf",
        "no": 110
    }
}
```

#### 引入group，来处理同一级下的多种请求

```swift 
public func routes(_ router: Router) throws {
    // Basic "Hello, world!" example
    let group = router.grouped("user")
    
    group.get("info") { (request) -> InfoData in
        return InfoData(name: "corotata", no: 110)
    }
    
    group.get("action") { (request) -> String in
        return  "Hello,i like code!"
    }
}

```

#### 引入Controller来减少routes.swift中的代码
当我们像上面的方式开发时，当后面api数量增多时，routes.swift中的代码量就会急剧增加，Vapor里面定义了一个协议为RouteCollection，

```swift
public protocol RouteCollection {
    /// Registers routes to the incoming router.
    ///
    /// - parameters:
    ///     - router: `Router` to register any new routes to.
    func boot(router: Router) throws
}

extension Router {
    /// Registers all of the routes in the group to this router.
    ///
    /// - parameters:
    ///     - collection: `RouteCollection` to register.
    public func register(collection: RouteCollection) throws {
        try collection.boot(router: self)
    }
}

```

代码很简单，其实就是让你创建一个实例，将原来写在routes.swift中的代码隔离出去，那么我们就来写一个来看看吧！

```swift
class UserController: RouteCollection {
    
    func boot(router: Router) throws {
        let group = router.grouped("user")
        
        group.get("info") { (request) -> InfoData in
            return InfoData(name: "corotata", no: 110)
        }
        
        group.get("action") { (request) -> String in
            return  "Hello,i like code!"
        }
        
    }
    
}

public func routes(_ router: Router) throws {
    try router.register(collection: UserController())
}

```
这么一改，routes.swift就精简多了，但UserController中的**func boot(router: Router)**就会出现像上面这样大片大片的代码，我们也想办法来调整一下吧:

```swift
class UserController: RouteCollection {
    func boot(router: Router) throws {
        let group = router.grouped("user")
        group.get("info", use: info)
        group.get("action", use:action)
    }
}

extension UserController {
    func info(_ req: Request) throws -> Future<Response> {
        return try InfoData(name: "corotata", no: 110).encode(for: req)
    }
    
    func action(_ req: Request) throws -> Future<Response> {
        return try "Hello,i like code!".encode(for: req)
    }
}


public func routes(_ router: Router) throws {
    try router.register(collection: UserController())
}

```
这样，终于看着舒服很多了，而关于route相关的内容也基本已经讲完，比如什么是Future，为什么代码可以像上面这样子写，其实我也还没有深入研究，因为我想快点上手写东西，所以就先知道这个要怎么写，那个要怎么写，直接给出例子，别人一看就知道，这么做就可以出效果了，至于概念和为什么的问题，在会调用后，后续我们再慢慢补充。

