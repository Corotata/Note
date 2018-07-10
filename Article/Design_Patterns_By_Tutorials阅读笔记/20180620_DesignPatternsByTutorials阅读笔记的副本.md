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

好像开发中，一般文件分层就已经是MVC了，所以这个好像也没啥好讲的了，只是往往我们使用时，Model与 ViewController 交互这一层上，我们往往都是直接ViewController修改完数据后直接去刷新UI，而并不像上面图画的，使用通知中心去更新，因为定义太多的通知中心，其实在开发中并不好，特别存在很多页面共用的情况。因此，很多时候，尽管我们不知道那并不规范，但我们还是会去那样做，因此我并不是很喜欢MVC。

#### 使用样例

```swift
import UIKit

public class MoviewRatingViewController: UIViewController {
  
  // MARK: - Properties
  public var movieRatingClient: MovieRatingStrategy!
  
  // MARK: - Outlets
  @IBOutlet public var movieTitleTextField: UITextField!
  @IBOutlet public var ratingServiceNameLabel: UILabel!
  @IBOutlet public var ratingLabel: UILabel!
  @IBOutlet public var reviewLabel: UILabel!
  
  // MARK: - View Lifecycle
  public override func viewDidLoad() {
    super.viewDidLoad()
    ratingServiceNameLabel.text = 
      movieRatingClient.ratingServiceName
  }
  
  // MARK: - Actions
  @IBAction public func searchButtonPressed(sender: Any) {
    guard let movieTitle = movieTitleTextField.text
      else { return }
    
    movieRatingClient.fetchRating(for: movieTitle) {
      (rating, review) in
      self.ratingLabel.text = rating
      self.reviewLabel.text = review
    }
  }
}

// MARK: - AddressView
public final class AddressView: UIView {
  @IBOutlet public var streetTextField: UITextField!
  @IBOutlet public var cityTextField: UITextField!
  @IBOutlet public var stateTextField: UITextField!
  @IBOutlet public var zipCodeTextField: UITextField!
}

// MARK: - AddressViewController
public final class AddressViewController: UIViewController {
  
  // MARK: - Properties
  public var address: Address? {
  didSet {
    updateViewFromAddress()
  }
}

public var addressView: AddressView! {
    guard isViewLoaded else { return nil }
    return view as! AddressView
  }
}

// MARK: - View Lifecycle
public override func viewDidLoad() {
  super.viewDidLoad()
  updateViewFromAddress()
}

private func updateViewFromAddress() {
  guard let addressView = addressView,
    let address = address else { return }
  addressView.streetTextField.text = address.street
  addressView.cityTextField.text = address.city
  addressView.stateTextField.text = address.state
  addressView.zipCodeTextField.text = address.zipCode
}

// MARK: - Actions
@IBAction public func updateAddressFromView(_ sender: AnyObject) {
  guard let street = addressView.streetTextField.text, street.count > 0,
    let city = addressView.cityTextField.text, city.count > 0,
    let state = addressView.stateTextField.text, state.count > 0,
    let zipCode = addressView.zipCodeTextField.text, zipCode.count > 0
    else {
      // TO-DO: show an error message, handle the error, etc
      return
  }
  address = Address(street: street, city: city,
                    state: state, zipCode: zipCode)
}
```

### Delegation Pattern （代理模式）

####什么是代理模式？

代理模式使*某个对象*能够使用另一个“帮手”来提供数据或者执行任务，而不是自己。通常它有三个部分：

1. 需要代理的对象，也称为代理对象。 这是拥有代理的对象。 代理通常被定义为弱属性，以避免循环引用，该代理保留代理对象。
2. 代理协议，它定义代理人可选的或必须实现的方法。
3. 一个代理，它是实现代理协议的帮助对象。


#### UML图
![Delegation Pattern](http://7xli4x.com1.z0.glb.clouddn.com/Delegation Pattern.png)

#### 什么时候使用代理模式？

当存在需要向外界获取数据或者让外界帮忙处理相应任务时，就可以考虑使用代理模式，开发中，我们大量使用，比如我们熟悉的UITabelView的Delegate和DataSource。

#### 使用样例

```swift
import UIKit

public class MoviewRatingViewController: UIViewController {
  
  // MARK: - Properties
  public var movieRatingClient: MovieRatingStrategy!
  
  // MARK: - Outlets
  @IBOutlet public var movieTitleTextField: UITextField!
  @IBOutlet public var ratingServiceNameLabel: UILabel!
  @IBOutlet public var ratingLabel: UILabel!
  @IBOutlet public var reviewLabel: UILabel!
  
  // MARK: - View Lifecycle
  public override func viewDidLoad() {
    super.viewDidLoad()
    ratingServiceNameLabel.text = 
      movieRatingClient.ratingServiceName
  }
  
  // MARK: - Actions
  @IBAction public func searchButtonPressed(sender: Any) {
    guard let movieTitle = movieTitleTextField.text
      else { return }
    
    movieRatingClient.fetchRating(for: movieTitle) {
      (rating, review) in
      self.ratingLabel.text = rating
      self.reviewLabel.text = review
    }
  }
}
```

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

*Strategy Pattern*与*Delegation pattern*它们都是依赖Protocol而不是具体对象来提高灵活性。因此，任何实现策略协议的对象都可以在动作时被当作一个策略来实用。

和*Delegation pattern*不同的是，*Strategy Pattern*通常是以一个类群的方式展示。*Delegation pattern*，举例来说，UITableView的dataSource和delegate可以从Interface Builder中设置，一经设定，在运行时将很少再去改变它。然而*Strategy Pattern*的设计旨在运行时容易互换。

#### 使用样例

``` swift
public protocol MovieRatingStrategy {
  // 1
  var ratingServiceName: String { get }
  
  // 2
  func fetchRating(for movieTitle: String,
    success: (_ rating: String, _ review: String) -> ())
}

public class RottenTomatoesClient: MovieRatingStrategy {
  public let ratingServiceName = "Rotten Tomatoes"
  
  public func fetchRating(
    for movieTitle: String,
    success: (_ rating: String, _ review: String) -> ()) {
    
    // In a real service, you'd make a network request...
    // Here, we just provide dummy values...
    let rating = "95%"
    let review = "It rocked!"
    success(rating, review)
  }
}


public class MoviewRatingViewController: UIViewController {
  
  // MARK: - Properties
  public var movieRatingClient: MovieRatingStrategy!
  
  // MARK: - Outlets
  @IBOutlet public var movieTitleTextField: UITextField!
  @IBOutlet public var ratingServiceNameLabel: UILabel!
  @IBOutlet public var ratingLabel: UILabel!
  @IBOutlet public var reviewLabel: UILabel!
  
  // MARK: - View Lifecycle
  public override func viewDidLoad() {
    super.viewDidLoad()
    ratingServiceNameLabel.text = 
      movieRatingClient.ratingServiceName
  }
  
  // MARK: - Actions
  @IBAction public func searchButtonPressed(sender: Any) {
    guard let movieTitle = movieTitleTextField.text
      else { return }
    
    movieRatingClient.fetchRating(for: movieTitle) {
      (rating, review) in
      self.ratingLabel.text = rating
      self.reviewLabel.text = review
    }
  }
}

```
### Singleton Pattern(单例模式)



#### 什么是单例模式？

单例模式将一个类限制为只有一个实例。 每个对类的引用都指向相同的底层实例。 它在iOS应用程序开发中很常见，比如UserDefault,FileManager等。

#### UML图
![Singleton Pattern](http://7xli4x.com1.z0.glb.clouddn.com/Singleton Pattern.png)

#### 什么时候使用单例模式？

当有一个类有多个实例时会导致问题或者不合逻辑时，一般就是使用单例模式的契机。

如果共享的实例大部分时间都有用，那么使用单例模式，但同时你也希望允许它创建自定义的实例，比如FileManager，它处理与文件系统访问有关的所有事情，它提供如FileManager.default的默认实例，但你也可以自己创建。通常如果你在后台线程使用它时，通常会自己创建。

单例模式提供了一种便捷的使用方式，但无限制的使用也会造成灾难性的问题，比如我曾经接手过一个项目，项目里面使用了大量的单位，基本应用的大部分页面都被挂载到一个APPContext的单例上，然后你会发现，竟然在某个子View中会APPContext.default.viewController1去获取相应的控制器，在子View中去跨层处理问题，导致App结构十分混乱。

#### 使用样例

```swift
public class MySingleton {
  // 1
  static let shared = MySingleton()
  // 2
  private init() { }
}
// 3
let mySingleton = MySingleton.shared
// 4
// let mySingleton2 = MySingleton()
```

### Memento Pattern(备忘录模式)

#### 什么是备忘录模式？

备忘录模式允许将一个对象储存或者还原，它涉及三部分。

1. Originator是要保存或者恢复的对象。
2. Memento要求Originator保存，并且记录一个状态。（用人话讲就是Originator包装成一个Memento的存档，比如状态就是日期）
3. Caretaker向Originator请求一份要存储的数据，并在稍后将获得一份存档，即Memento，可以进行存储或者用以恢复Originator的状态。

#### UML图
![Memento Pattern](http://7xli4x.com1.z0.glb.clouddn.com/Memento Pattern.png)

#### 什么时候使用备忘录模式？
每当你想保存和稍后恢复一个对象的状态时，使用备忘录模式。
例如，你可以使用这种模式实现一个保存游戏系统，其中Originator是游戏状态（如游戏进度，等级等），Memento是保存的数据，即存档，而Caretaker则是游戏系统。
你也可以存储一系列的Memento，代表一堆以前的状态，用于实现在IDE或者图象处理软件中的撤销，重做等功能。

### Observer Pattern（通知者模式）

#### 什么是通知者模式？
观察者模式让一个对象观察另一个对象的变化。它由两部分组成：

1. Subject(主题）是被观察的对象。
2. Observer是进行观察的对象。
 
#### UML图
![Observer Pattern](http://7xli4x.com1.z0.glb.clouddn.com/Observer Pattern.png)

#### 什么时候使用通知者模式？
当某个对象发生更改时，你想监听到它改变，那么你就可以使用观察者模式。
可以参考我写的一个[开源](https://github.com/Corotata/KingObserver)，比原生的好用。

### Builder Pattern（构建者模式）

#### 什么是构建者模式？
构建器模式允许通过初始化器一步一步地创建复杂对象，而不是一次创建所有对象。 这种模式涉及三种主要类型：

1. *Director*接受*Builder*的输入和协调。 这通常是视图控制器使用的视图控制器或助手类。
2. *Product*是要创建的复杂对象。 这可以是结构或类，具体取决于所需的引用语义。 它通常是一个模型，但它可以是任何类型，具体取决于您的用例。
3. *Builder*接受逐步输入并处理产品的创建。 这通常是一个类，所以可以通过引用重用。
 
#### UML图
![Observer Pattern](http://7xli4x.com1.z0.glb.clouddn.com/Observer Pattern.png)

#### 什么时候使用构建者模式？
当你想需要通过很多步骤组成时才能创建出一个复杂的对象时，你就可以使用构建者模式来简化这一过程。

当*Product*需要多个输入时, 此模式尤其有效。*Builder*抽象出这些输入是如何用于创建*Product*的, 并且它按照*Director*希望提供的任何顺序接受它们。（用人话来说就是，*Director*只要按照*Builder*的要求提供数据，*Builder*就能创建出*Product*）

例如protobuf的构建过程就使用了大量的Builder.


#### 使用样例


```swift
// MARK: - Product
// 1
public struct Hamburger {
  public let meat: Meat
  public let sauce: Sauces
  public let toppings: Toppings
}

extension Hamburger: CustomStringConvertible {
   public var description: String {
    return meat.rawValue + " burger"
  }
}

// 2
public enum Meat: String {
  case beef
  case chicken
  case kitten
  case tofu
}

// 3
public struct Sauces: OptionSet {
  public static let mayonnaise = Sauces(rawValue: 1 << 0)
  public static let mustard = Sauces(rawValue: 1 << 1)
  public static let ketchup = Sauces(rawValue: 1 << 2)
  public static let secret = Sauces(rawValue: 1 << 3)
  
  public let rawValue: Int
  public init(rawValue: Int) {
    self.rawValue = rawValue
  }
}

// 4
public struct Toppings: OptionSet {
  public static let cheese = Toppings(rawValue: 1 << 0)
  public static let lettuce = Toppings(rawValue: 1 << 1)
  public static let pickles = Toppings(rawValue: 1 << 2)
  public static let tomatoes = Toppings(rawValue: 1 << 3)
  
  public let rawValue: Int
  public init(rawValue: Int) {
    self.rawValue = rawValue
  }
}

public enum Error: Swift.Error {
    case soldOut
}

// MARK: - Builder
public class HamburgerBuilder {
  // 1
  public private(set) var meat: Meat = .beef
  public private(set) var sauces: Sauces = []
  public private(set) var toppings: Toppings = []
  private var soldOutMeats: [Meat] = [.kitten]
  // 2
  public func addSauces(_ sauce: Sauces) {
    sauces.insert(sauce)
  }
  
  public func removeSauces(_ sauce: Sauces) {
    sauces.remove(sauce)
  }
    
  public func addToppings(_ topping: Toppings) {
    toppings.insert(topping)
  }
  
  public func removeToppings(_ topping: Toppings) {
    toppings.remove(topping)
  }
  
  public func setMeat(_ meat: Meat) throws {
    guard isAvailable(meat) else { throw Error.soldOut }
    self.meat = meat
  }

  public func isAvailable(_ meat: Meat) -> Bool {
    return !soldOutMeats.contains(meat)
  }
 
  // 3
  public func build() -> Hamburger {
    return Hamburger(meat: meat,
                     sauce: sauces,
                     toppings: toppings)
  }
}

// MARK: - Director
public class Employee {
  
  public func createCombo1() throws -> Hamburger {
    let builder = HamburgerBuilder()
    try builder.setMeat(.beef)
    builder.addSauces(.secret)
    builder.addToppings([.lettuce, .tomatoes, .pickles])
    return builder.build()
  }
  
  public func createKittenSpecial() throws -> Hamburger {
    let builder = HamburgerBuilder()
    try builder.setMeat(.kitten)
    builder.addSauces(.mustard)
    builder.addToppings([.lettuce, .tomatoes])
    return builder.build()
  }
}


// MARK: - Example
let burgerFlipper = Employee()

if let combo1 = try? burgerFlipper.createCombo1() {
  print("Nom nom " + combo1.description)
}

if let kittenBurger = try?
  burgerFlipper.createKittenSpecial() {
  print("Nom nom nom " + kittenBurger.description)
  
} else {
  print("Sorry, no kitten burgers here... :[")
}

```

### Model View Controller Pattern（MVVM模式）

#### 什么是MVVM模式？
MVVM 是一种结构设计模式, 它将对象分为三个不同的组:

1. Model持有应用数据，通常是简单的类或才结构体
2. View是在UI上显示的元素或者控件，它们通常是UIView的子类
3. ViewModel将模型转换成View可以显示的值，他们通常是类，所以他们可以用于参数传递。

它是由MVC衍生而来的，主要解决ViewController太过臃肿的问题。
 
#### UML图
![Model-View-Controller-Pattern](http://7xli4x.com1.z0.glb.clouddn.com/Model-View-Controller-Pattern.png)


#### 什么时候使用MVVM模式？

当您需要将Model转换为View的另一个表示形式时，请使用此模式。 例如，您可以使用视图模型将日期转换为日期格式的字符串，将十进制转换为货币格式的字符串或许多其他有用的转换。

这种模式特别适合MVC。 如果没有ViewModel，您可能会在ViewCOntroller中放入Model到View的转换代码。 然而，ViewCOntroller已经做了很多事情：处理viewDidLoad和其他视图生命周期事件，通过IBAction处理视图回调以及其他一些任务。

#### 使用样例

```swift
import PlaygroundSupport
import UIKit

// MARK: - Model
public class Pet {
  public enum Rarity {
    case common
    case uncommon
    case rare
    case veryRare
  }
  
  public let name: String
  public let birthday: Date
  public let rarity: Rarity
  public let image: UIImage
  
  public init(name: String,
              birthday: Date,
              rarity: Rarity,
              image: UIImage) {
    self.name = name
    self.birthday = birthday
    self.rarity = rarity
    self.image = image
  }
}

// MARK: - ViewModel
public class PetViewModel {
  
  // 1
  private let pet: Pet
  private let calendar: Calendar
  
  public init(pet: Pet) {
    self.pet = pet
    self.calendar = Calendar(identifier: .gregorian)
  }
  
  // 2
  public var name: String {
    return pet.name
  }
  
  public var image: UIImage {
    return pet.image
  }
  
  // 3
  public var ageText: String {
    let today = calendar.startOfDay(for: Date())
    let birthday = calendar.startOfDay(for: pet.birthday)
    let components = calendar.dateComponents([.year],
                                             from: birthday,
                                             to: today)
    let age = components.year!
    return "\(age) years old"
  }
  
  // 4
  public var adoptionFeeText: String {
    switch pet.rarity {
    case .common:
      return "$50.00"
    case .uncommon:
      return "$75.00"
    case .rare:
      return "$150.00"
    case .veryRare:
      return "$500.00"
    }
  }
}

// MARK: - View
public class PetView: UIView {
  public let imageView: UIImageView
  public let nameLabel: UILabel
  public let ageLabel: UILabel
  public let adoptionFeeLabel: UILabel
  
  public override init(frame: CGRect) {
    
    var childFrame = CGRect(x: 0, y: 16,
                            width: frame.width,
                            height: frame.height / 2)
    imageView = UIImageView(frame: childFrame)
    imageView.contentMode = .scaleAspectFit
    
    childFrame.origin.y += childFrame.height + 16
    childFrame.size.height = 30
    nameLabel = UILabel(frame: childFrame)
    nameLabel.textAlignment = .center
    
    childFrame.origin.y += childFrame.height
    ageLabel = UILabel(frame: childFrame)
    ageLabel.textAlignment = .center
    
    childFrame.origin.y += childFrame.height
    adoptionFeeLabel = UILabel(frame: childFrame)
    adoptionFeeLabel.textAlignment = .center
    
    super.init(frame: frame)
    
    backgroundColor = .white
    addSubview(imageView)
    addSubview(nameLabel)
    addSubview(ageLabel)
    addSubview(adoptionFeeLabel)
  }
  
  @available(*, unavailable)
  public required init?(coder: NSCoder) {
    fatalError("init?(coder:) is not supported")
  }
}

// MARK: - Example
// 1
let birthday = Date(timeIntervalSinceNow: (-2 * 86400 * 366))
let image = UIImage(named: "stuart")!
let stuart = Pet(name: "Stuart",
                 birthday: birthday,
                 rarity: .veryRare,
                 image: image)

// 2
let viewModel = PetViewModel(pet: stuart)

// 3
let frame = CGRect(x: 0, y: 0, width: 300, height: 420)
let view = PetView(frame: frame)

// 4 
view.nameLabel.text = viewModel.name
view.imageView.image = viewModel.image
view.ageLabel.text = viewModel.ageText
view.adoptionFeeLabel.text = viewModel.adoptionFeeText

// 5
PlaygroundPage.current.liveView = view

```

