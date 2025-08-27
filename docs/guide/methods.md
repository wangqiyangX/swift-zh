---
description: 定义和调用属于实例或类型的函数。
---

# 方法

> 定义和调用属于实例或类型的函数。

方法是与特定类型关联的函数。类、结构和枚举都可以定义实例方法，这些方法封装了与给定类型的实例一起使用的特定任务和功能。类、结构和枚举还可以定义类型方法，这些方法与类型本身相关联。类型方法类似于 Objective-C 中的类方法。

在 Swift 中，结构体和枚举可以定义方法这一事实是与 C 和 Objective-C 的主要区别。在 Objective-C 中，类是唯一可以定义方法的类型。在 Swift 中，您可以选择定义类、结构体或枚举，并且仍然可以灵活地在您创建的类型上定义方法。

## [实例方法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/methods#Instance-Methods)

实例方法是属于特定类、结构体或枚举实例的函数。它们支持这些实例的功能，提供访问和修改实例属性的方法，或提供与实例目的相关的功能。实例方法的语法与函数完全相同，如函数中所述。

您在属于该类型的开括号和闭括号之间编写实例方法。实例方法对该类型的所有其他实例方法和属性具有隐式访问权限。实例方法只能在属于它的特定实例上调用。它不能在没有现有实例的情况下单独调用。

这里有一个示例，定义了一个简单的 `Counter` 类，可以用来计算某个动作发生的次数：

```swift
class Counter {
    var count = 0
    func increment() {
        count += 1
    }
    func increment(by amount: Int) {
        count += amount
    }
    func reset() {
        count = 0
    }
}
```

`Counter` 类定义了三个实例方法：

- `increment()` 将计数器增加 `1` 。
- `increment(by: Int)` 将计数器增加一个指定的整数值。
- `reset()` 将计数器重置为零。

`Counter` 类还声明了一个变量属性 `count` ，用于跟踪当前计数器值。

您可以使用与属性相同的点语法调用实例方法：

```swift
let counter = Counter()
// the initial counter value is 0
counter.increment()
// the counter's value is now 1
counter.increment(by: 5)
// the counter's value is now 6
counter.reset()
// the counter's value is now 0
```

函数参数可以同时具有名称（在函数体内使用）和参数标签（在调用函数时使用），如函数参数标签和参数名称中所述。方法参数也是如此，因为方法只是与类型关联的函数。

### [self 属性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/methods#The-self-Property)

每个类型的实例都有一个隐式属性，称为 `self` ，它与实例本身完全相同。您可以使用 `self` 属性在其自身的实例方法中引用当前实例。

上面示例中的 `increment()` 方法可以这样编写：

```swift
func increment() {
    self.count += 1
}
```

实际上，您不需要在代码中经常写 `self` 。如果您没有明确写出 `self` ，Swift 会假设您在使用已知属性或方法名称时引用的是当前实例的属性或方法。这种假设在 `Counter` 的三个实例方法中使用 `count` （而不是 `self.count` ）得到了体现。

这个规则的主要例外发生在实例方法的参数名称与该实例的属性同名的情况下。在这种情况下，参数名称具有优先权，因此有必要以更明确的方式引用属性。您可以使用 `self` 属性来区分参数名称和属性名称。

在这里， `self` 用于区分一个名为 `x` 的方法参数和一个同样名为 `x` 的实例属性：

```swift
struct Point {
    var x = 0.0, y = 0.0
    func isToTheRightOf(x: Double) -> Bool {
        return self.x > x
    }
}
let somePoint = Point(x: 4.0, y: 5.0)
if somePoint.isToTheRightOf(x: 1.0) {
    print("This point is to the right of the line where x == 1.0")
}
// Prints "This point is to the right of the line where x == 1.0"
```

如果没有 `self` 前缀，Swift 会假设 `x` 的两个用法都指的是名为 `x` 的方法参数。

### [从实例方法内部修改值类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/methods#Modifying-Value-Types-from-Within-Instance-Methods)

结构和枚举是值类型。默认情况下，值类型的属性不能在其实例方法中被修改。

但是，如果您需要在特定方法中修改结构或枚举的属性，可以选择在该方法中启用可变行为。然后，该方法可以在方法内部修改（即更改）其属性，并且它所做的任何更改将在方法结束时写回到原始结构中。该方法还可以将一个全新的实例分配给其隐式 `self` 属性，并且当方法结束时，这个新实例将替换现有实例。

您可以通过在该方法的 `func` 关键字之前放置 `mutating` 关键字来选择这种行为：

```swift
struct Point {
    var x = 0.0, y = 0.0
    mutating func moveBy(x deltaX: Double, y deltaY: Double) {
        x += deltaX
        y += deltaY
    }
}
var somePoint = Point(x: 1.0, y: 1.0)
somePoint.moveBy(x: 2.0, y: 3.0)
print("The point is now at (\(somePoint.x), \(somePoint.y))")
// Prints "The point is now at (3.0, 4.0)"
```

上面的 `Point` 结构定义了一个变异的 `moveBy(x:y:)` 方法，该方法将 `Point` 实例移动一定量。此方法并不返回一个新的点，而是实际修改了调用它的点。 `mutating` 关键字被添加到其定义中，以使其能够修改其属性。

请注意，您不能在结构类型的常量上调用可变方法，因为其属性无法更改，即使它们是可变属性，如《常量结构实例的存储属性》中所述：

```swift
let fixedPoint = Point(x: 3.0, y: 3.0)
fixedPoint.moveBy(x: 2.0, y: 3.0)
// this will report an error
```

### [在变异方法中给自身赋值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/methods#Assigning-to-self-Within-a-Mutating-Method)

变异方法可以将一个全新的实例分配给隐式 `self` 属性。上面显示的 `Point` 示例可以用以下方式编写：

```swift
struct Point {
    var x = 0.0, y = 0.0
    mutating func moveBy(x deltaX: Double, y deltaY: Double) {
        self = Point(x: x + deltaX, y: y + deltaY)
    }
}
```

这个变异 `moveBy(x:y:)` 方法的版本创建了一个新的结构，其 `x` 和 `y` 值被设置为目标位置。调用这个替代版本的方法的最终结果将与调用早期版本的结果完全相同。

枚举的变异方法可以将隐式 `self` 参数设置为同一枚举的不同案例：

```swift
enum TriStateSwitch {
    case off, low, high
    mutating func next() {
        switch self {
        case .off:
            self = .low
        case .low:
            self = .high
        case .high:
            self = .off
        }
    }
}
var ovenLight = TriStateSwitch.low
ovenLight.next()
// ovenLight is now equal to .high
ovenLight.next()
// ovenLight is now equal to .off
```

此示例定义了一个三状态开关的枚举。每当调用其 `next()` 方法时，开关在三个不同的电源状态（ `off` 、 `low` 和 `high` ）之间循环。

## [类型方法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/methods#Type-Methods)

实例方法，如上所述，是您在特定类型的实例上调用的方法。您还可以定义在类型本身上调用的方法。这些方法被称为类型方法。您通过在方法的 `func` 关键字之前写入 `static` 关键字来指示类型方法。类可以使用 `class` 关键字，允许子类重写超类对该方法的实现。

注意

在 Objective-C 中，您只能为 Objective-C 类定义类型级方法。在 Swift 中，您可以为所有类、结构体和枚举定义类型级方法。每个类型方法的作用域明确限定于它所支持的类型。

类型方法使用点语法进行调用，就像实例方法一样。然而，您是在类型上调用类型方法，而不是在该类型的实例上。以下是如何在名为 `SomeClass` 的类上调用类型方法：

```swift
class SomeClass {
    class func someTypeMethod() {
        // type method implementation goes here
    }
}
SomeClass.someTypeMethod()
```

在类型方法的主体中，隐式 `self` 属性指的是类型本身，而不是该类型的实例。这意味着您可以使用 `self` 来区分类型属性和类型方法参数，就像您区分实例属性和实例方法参数一样。

更一般地说，您在类型方法的主体中使用的任何不带限定符的方法和属性名称将引用其他类型级别的方法和属性。类型方法可以使用其他方法的名称调用另一个类型方法，而无需在前面加上类型名称。同样，结构和枚举上的类型方法可以通过使用类型属性的名称而不带类型名称前缀来访问类型属性。

下面的示例定义了一个名为 `LevelTracker` 的结构，它跟踪玩家在游戏的不同关卡或阶段中的进度。这是一个单人游戏，但可以在单个设备上存储多个玩家的信息。

游戏的所有关卡（除了第一关）在游戏首次播放时都是锁定的。每当玩家完成一个关卡时，该关卡将对设备上的所有玩家解锁。 `LevelTracker` 结构使用类型属性和方法来跟踪游戏中已解锁的关卡。它还跟踪单个玩家的当前关卡。

```swift
struct LevelTracker {
    static var highestUnlockedLevel = 1
    var currentLevel = 1


    static func unlock(_ level: Int) {
        if level > highestUnlockedLevel { highestUnlockedLevel = level }
    }


    static func isUnlocked(_ level: Int) -> Bool {
        return level <= highestUnlockedLevel
    }


    @discardableResult
    mutating func advance(to level: Int) -> Bool {
        if LevelTracker.isUnlocked(level) {
            currentLevel = level
            return true
        } else {
            return false
        }
    }
}
```

`LevelTracker` 结构跟踪任何玩家已解锁的最高级别。该值存储在一个名为 `highestUnlockedLevel` 的类型属性中。

`LevelTracker` 还定义了两个类型函数来处理 `highestUnlockedLevel` 属性。第一个是一个名为 `unlock(_:)` 的类型函数，它会在解锁新等级时更新 `highestUnlockedLevel` 的值。第二个是一个名为 `isUnlocked(_:)` 的便利类型函数，如果某个特定级别编号已经解锁，它将返回 `true` 。 (请注意，这些类型方法可以访问 `highestUnlockedLevel` 类型属性，而无需您将其写成 `LevelTracker.highestUnlockedLevel` 。）

除了类型属性和类型方法， `LevelTracker` 还跟踪单个玩家在游戏中的进度。它使用一个名为 `currentLevel` 的实例属性来跟踪玩家当前正在玩的级别。

为了帮助管理 `currentLevel` 属性， `LevelTracker` 定义了一个名为 `advance(to:)` 的实例方法。在更新 `currentLevel` 之前，此方法会检查请求的新级别是否已经解锁。 `advance(to:)` 方法返回一个布尔值，以指示是否能够实际设置 `currentLevel` 。因为调用 `advance(to:)` 方法的代码忽略返回值并不一定是错误的，所以此函数被标记为 `@discardableResult` 属性。如需有关此属性的更多信息，请参阅属性。

`LevelTracker` 结构与 `Player` 类一起使用，如下所示，用于跟踪和更新单个玩家的进度：

```swift
class Player {
    var tracker = LevelTracker()
    let playerName: String
    func complete(level: Int) {
        LevelTracker.unlock(level + 1)
        tracker.advance(to: level + 1)
    }
    init(name: String) {
        playerName = name
    }
}
```

`Player` 类创建一个新的 `LevelTracker` 实例来跟踪该玩家的进度。它还提供了一个名为 `complete(level:)` 的方法，每当玩家完成特定关卡时会调用该方法。这个方法为所有玩家解锁下一个关卡，并更新玩家的进度以将其移动到下一个关卡。（ `advance(to:)` 的布尔返回值被忽略，因为通过上面的 `LevelTracker.unlock(_:)` 调用已经知道该关卡被解锁。）

您可以为新玩家创建一个 `Player` 类的实例，并查看当玩家完成第一关时会发生什么：

```swift
var player = Player(name: "Argyrios")
player.complete(level: 1)
print("highest unlocked level is now \(LevelTracker.highestUnlockedLevel)")
// Prints "highest unlocked level is now 2"
```

如果您创建第二个玩家，并试图将其移动到游戏中尚未被任何玩家解锁的关卡，则设置该玩家的当前关卡的尝试将失败：

```swift
player = Player(name: "Beto")
if player.tracker.advance(to: 6) {
    print("player is now on level 6")
} else {
    print("level 6 hasn't yet been unlocked")
}
// Prints "level 6 hasn't yet been unlocked"
```
