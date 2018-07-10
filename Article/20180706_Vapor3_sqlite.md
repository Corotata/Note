title: Vapor3_sqlite
date: 2018年 7月 6日 星期五 08时48分37秒 CST
tags: 
---

####如何创建一个Sqlite数据库

创建内存数据库

```swift
let sqlite = try SQLiteDatabase(storage: .memory)
```
创建本地数据库

```swift 
let sqlite = try SQLiteDatabase(storage: .file(path: "/Users/corotata/Library/Mobile Documents/com~apple~CloudDocs/Vapor/todo.sqlite"))

```
然后在在Configure.swift中注册

```swift
var databases = DatabasesConfig()
databases.add(database: sqlite, as: .sqlite)
services.register(databases)
```
Vapor提供了数据库的对象化封装，这样我们就不用去写sql语句了，这边以使用sqlite为例，首先，我们需要在Configurre.swift中注册，

```swift
import FluentSQLite

public func configure(_ config: inout Config, _ env: inout Environment, _ services: inout Services) throws {
    /// Register providers first
    try services.register(FluentSQLiteProvider())
}

```

接下来创建对象，对象需要继承SQLiteModel

```swift
/// A single entry of a Todo list.
final class Todo: SQLiteModel {
    /// The unique identifier for this `Todo`.
    var id: Int?

    /// A title describing what this `Todo` entails.
    var title: String

    /// Creates a new `Todo`.
    init(title: String) {
        self.title = title
    }
}

extension Todo: Model {}

/// Allows `Todo` to be used as a dynamic migration.
extension Todo: Migration { }

/// Allows `Todo` to be encoded to and decoded from HTTP messages.
extension Todo: Content { }

/// Allows `Todo` to be used as a dynamic parameter in route definitions.
extension Todo: Parameter { }

```

下一步，我们需要重新回Configure中配置Todo代表数据库中的一张表,以下给出Configure的完整代码


```swift
import FluentSQLite
import Vapor

/// Called before your application initializes.
public func configure(_ config: inout Config, _ env: inout Environment, _ services: inout Services) throws {
    /// Register providers first
    try services.register(FluentSQLiteProvider())

    /// Register routes to the router
    let router = EngineRouter.default()
    try routes(router)
    services.register(router, as: Router.self)

    // Configure a SQLite database
//    let sqlite = try SQLiteDatabase(storage: .memory)
    let sqlite = try SQLiteDatabase(storage: .file(path: "/Users/corotata/Library/Mobile Documents/com~apple~CloudDocs/Vapor/todo.sqlite"))
    var databases = DatabasesConfig()
    databases.add(database: sqlite, as: .sqlite)
    services.register(databases)

    /// Configure migrations
    var migrations = MigrationConfig()
    migrations.add(model: Todo.self, database: .sqlite)
    services.register(migrations)

}

```
接下来，我们就可以写增删改查的操作了，下面给出api的例子：

```swift
import Vapor
import FluentSQLite

/// Controls basic CRUD operations on `Todo`s.
final class TodoController: RouteCollection {
    func boot(router: Router) throws {
        let group = router.grouped("todos")
        group.post("create", use: create)   //增
        group.post("delete", use: delete)   //删
        group.post("update", use: update)   //改
        group.get("query", use: query)      //查
        
    }
}

struct TodoId: Content {
    var id: Int
}

extension TodoController {
    func create(_ request: Request) throws -> Future<Todo> {
        return try request.content.decode(Todo.self).flatMap { todo  in
            return todo.save(on: request)
        }
    }
    func query(_ request: Request) throws -> Future<[Todo]> {
        return Todo.query(on: request).all()
    }
    
    public func routes(_ router: Router) throws {
        router.post("info") { (request) -> String in
            let name = try request.content.syncGet(String.self, at: "name")
            let no = try request.content.syncGet(Int.self, at: "no")
            return "hello \(name),your number is \(no)"
        }
    }

    
    func delete(_ request: Request) throws -> Future<Response> {
        let id = try request.content.syncGet(Int.self, at: "id")
        return Todo.find(id, on: request).flatMap { todo in
            guard let todo = todo else {
                return try "内容不存在".encode(for: request)
            }
            
            return todo.delete(on: request).flatMap({ _ in
                return try "删除成功".encode(for: request)
            })
        }
    }
    
    func update(_ request: Request) throws -> Future<HTTPStatus> {
        return try request.content.decode(Todo.self).flatMap { todo in
            return todo.update(on: request)
        }.transform(to: .ok)
    }
    
}

```

