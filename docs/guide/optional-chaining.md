# 可选链

> 在不解包的情况下访问可选值的成员。

可选链是一种查询和调用可能当前为 `nil` 的可选项的属性、方法和下标的过程。如果可选项包含一个值，则属性、方法或下标调用成功；如果可选项为 `nil` ，则属性、方法或下标调用返回 `nil` 。多个查询可以链式连接，如果链中的任何链接为 `nil` ，整个链将优雅地失败。

> 注意
>
> 在 Swift 中的可选链式调用类似于在 Objective-C 中发送消息 `nil` ，但它可以适用于任何类型，并且可以检查成功或失败。

## [可选链作为强制解包的替代方案](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/optionalchaining#Optional-Chaining-as-an-Alternative-to-Forced-Unwrapping)

您通过在希望调用属性、方法或下标的可选值后面放置一个问号（ `?` ）来指定可选链，如果可选值是非- `nil` 。这与在可选值后面放置一个感叹号（ `!` ）以强制解包其值非常相似。主要的区别在于，当可选值是 `nil` 时，可选链优雅地失败，而当可选值是 `nil` 时，强制解包会触发运行时错误。

为了反映可选链可以在 `nil` 值上调用的事实，可选链调用的结果始终是可选值，即使您查询的属性、方法或下标返回的是非可选值。您可以使用这个可选返回值来检查可选链调用是否成功（返回的可选值包含一个值），或者由于链中存在 `nil` 值而未成功（返回的可选值是 `nil` ）。

具体来说，选项链调用的结果与预期返回值的类型相同，但被封装在一个可选项中。一个通常返回 `Int` 的属性在通过选项链访问时将返回 `Int?` 。

接下来的几个代码片段演示了选项链如何与强制解封不同，并使您能够检查成功。

首先，定义了两个类，称为 `Person` 和 `Residence` ：

```swift
class Person {
    var residence: Residence?
}


class Residence {
    var numberOfRooms = 1
}
```

`Residence` 实例具有一个名为 `numberOfRooms` 的单一 `Int` 属性，默认值为 `1` 。 `Person` 实例具有类型为 `Residence?` 的可选 `residence` 属性。

如果您创建一个新的 `Person` 实例，它的 `residence` 属性默认初始化为 `nil` ，因为它是可选的。在下面的代码中， `john` 的 `residence` 属性值为 `nil` ：

```swift
let john = Person()
```

如果您尝试通过在 `residence` 后面放置一个感叹号来强制解包其值，访问此人的 `residence` 的 `numberOfRooms` 属性，您会触发运行时错误，因为没有 `residence` 值可以解包：

```swift
let roomCount = john.residence!.numberOfRooms
// this triggers a runtime error
```

当 `john.residence` 具有非 `nil` 值时，上面的代码成功，并将 `roomCount` 设置为包含适当数量房间的 `Int` 值。然而，当 `residence` 为 `nil` 时，这段代码总是会触发运行时错误，如上所示。

可选链提供了一种访问 `numberOfRooms` 值的替代方法。要使用可选链，请用问号代替感叹号：

```swift
if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")
}
// Prints "Unable to retrieve the number of rooms."
```

这告诉 Swift 在可选的 `residence` 属性上“链式”操作，并在 `residence` 存在时检索 `numberOfRooms` 的值。

因为访问 `numberOfRooms` 的尝试可能会失败，所以可选链式尝试返回类型为 `Int?` 的值，或称为“可选 `Int` ”。当 `residence` 为 `nil` 时，如上面的例子，这个可选 `Int` 也将是 `nil` ，以反映无法访问 `numberOfRooms` 的事实。可选 `Int` 通过可选绑定访问，以解包整数并将非可选值分配给 `roomCount` 常量。

请注意，即使 `numberOfRooms` 是一个非可选的 `Int` ，这也是正确的。通过可选链查询的事实意味着对 `numberOfRooms` 的调用将始终返回一个 `Int?` 而不是 `Int` 。

您可以将一个 `Residence` 实例分配给 `john.residence` ，这样它就不再具有 `nil` 值：

```swift
john.residence = Residence()
```

`john.residence` 现在包含一个实际的 `Residence` 实例，而不是 `nil` 。如果您尝试使用之前相同的可选链访问 `numberOfRooms` ，它现在将返回一个包含默认 `numberOfRooms` 值 `1` 的 `Int?` ：

```swift
if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")
}
// Prints "John's residence has 1 room(s)."
```

## [定义可选链的模型类](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/optionalchaining#Defining-Model-Classes-for-Optional-Chaining)

您可以在对属性、方法和下标的调用中使用可选链，这些调用的层级超过一个。这使您能够深入到复杂模型中相互关联类型的子属性，并检查是否可以访问这些子属性上的属性、方法和下标。

下面的代码片段定义了四个模型类，以便在后续的几个示例中使用，包括多层可选链的示例。这些类通过添加一个 `Room` 和 `Address` 类，及其相关的属性、方法和下标，扩展了上面的 `Person` 和 `Residence` 模型。

`Person` 类的定义与之前相同：

```swift
class Person {
    var residence: Residence?
}
```

`Residence` 类比之前更复杂了。这次， `Residence` 类定义了一个名为 `rooms` 的变量属性，该属性初始化为类型为 `[Room]` 的空数组：

```swift
class Residence {
    var rooms: [Room] = []
    var numberOfRooms: Int {
        return rooms.count
    }
    subscript(i: Int) -> Room {
        get {
            return rooms[i]
        }
        set {
            rooms[i] = newValue
        }
    }
    func printNumberOfRooms() {
        print("The number of rooms is \(numberOfRooms)")
    }
    var address: Address?
}
```

因为这个版本的 `Residence` 存储了一个 `Room` 实例的数组，因此它的 `numberOfRooms` 属性被实现为计算属性，而不是存储属性。计算 `numberOfRooms` 属性仅仅返回 `rooms` 数组中 `count` 属性的值。

作为访问其 `rooms` 数组的快捷方式，这个版本的 `Residence` 提供了一个读写下标，可以访问 `rooms` 数组中请求的索引处的房间。

这个版本的 `Residence` 还提供了一个名为 `printNumberOfRooms` 的方法，它简单地打印出住宅中的房间数量。

最后， `Residence` 定义了一个可选属性，名为 `address` ，类型为 `Address?` 。该属性的 `Address` 类类型定义如下。

用于 `rooms` 数组的 `Room` 类是一个简单的类，具有一个名为 `name` 的属性，并带有一个初始化器将该属性设置为适当的房间名称：

```swift
class Room {
    let name: String
    init(name: String) { self.name = name }
}
```

该模型中的最终类称为 `Address` 。此类有三个可选的 `String?` 类型属性。前两个属性 `buildingName` 和 `buildingNumber` 是识别特定建筑物作为地址的一种替代方式。第三个属性 `street` 用于命名该地址的街道：

```swift
class Address {
    var buildingName: String?
    var buildingNumber: String?
    var street: String?
    func buildingIdentifier() -> String? {
        if let buildingNumber = buildingNumber, let street = street {
            return "\(buildingNumber) \(street)"
        } else if buildingName != nil {
            return buildingName
        } else {
            return nil
        }
    }
}
```

`Address` 类还提供了一个名为 `buildingIdentifier()` 的方法，该方法的返回类型为 `String?` 。此方法检查地址的属性，如果有值则返回 `buildingName` ，如果两个都有值则返回 `buildingNumber` 连接 `street` ，否则返回 `nil` 。

## [通过可选链访问属性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/optionalchaining#Accessing-Properties-Through-Optional-Chaining)

如在可选链作为强制解包替代方案中所示，您可以使用可选链访问可选值上的属性，并检查该属性访问是否成功。

使用上面定义的类创建新的 `Person` 实例，并尝试像以前一样访问其 `numberOfRooms` 属性：

```swift
let john = Person()
if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")
}
// Prints "Unable to retrieve the number of rooms."
```

因为 `john.residence` 是 `nil` ，所以这个可选链调用以与之前相同的方式失败。

您还可以通过可选链尝试设置属性值：

```swift
let someAddress = Address()
someAddress.buildingNumber = "29"
someAddress.street = "Acacia Road"
john.residence?.address = someAddress
```

在此示例中，尝试设置 `address` 属性的 `john.residence` 将失败，因为 `john.residence` 当前是 `nil` 。

赋值是可选链的一部分，这意味着 `=` 操作符右侧的代码都不会被求值。在上一个示例中，不容易看出 `someAddress` 从未被求值，因为访问一个常量没有任何副作用。下面的清单执行相同的赋值，但是它使用一个函数来创建地址。该函数在返回值之前打印“函数被调用”，这让您看到 `=` 操作符的右侧是否被求值。

```swift
func createAddress() -> Address {
    print("Function was called.")


    let someAddress = Address()
    someAddress.buildingNumber = "29"
    someAddress.street = "Acacia Road"


    return someAddress
}
john.residence?.address = createAddress()
```

您可以看出 `createAddress()` 函数没有被调用，因为没有任何输出。

## [通过可选链调用方法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/optionalchaining#Calling-Methods-Through-Optional-Chaining)

您可以使用可选链在可选值上调用方法，并检查该方法调用是否成功。即使该方法没有定义返回值，您也可以这样做。

`Residence` 类上的 `printNumberOfRooms()` 方法打印 `numberOfRooms` 的当前值。以下是该方法的样子：

```swift
func printNumberOfRooms() {
    print("The number of rooms is \(numberOfRooms)")
}
```

此方法没有指定返回类型。然而，没有返回类型的函数和方法具有隐式返回类型 `Void` ，如《没有返回值的函数》中所述。这意味着它们返回一个值 `()` ，或一个空元组。

如果您在具有可选链的可选值上调用此方法，则该方法的返回类型将是 `Void?` ，而不是 `Void` ，因为通过可选链调用时返回值始终为可选类型。这使您能够使用 `if` 语句检查是否可以调用 `printNumberOfRooms()` 方法，即使该方法本身并不定义返回值。将 `printNumberOfRooms` 调用的返回值与 `nil` 进行比较，以查看方法调用是否成功：

```swift
if john.residence?.printNumberOfRooms() != nil {
    print("It was possible to print the number of rooms.")
} else {
    print("It was not possible to print the number of rooms.")
}
// Prints "It was not possible to print the number of rooms."
```

如果您尝试通过可选链设置属性，也是如此。上面的例子在通过可选链访问属性中尝试为 `john.residence` 设置一个 `address` 值，尽管 `residence` 属性是 `nil` 。通过可选链设置属性的任何尝试都会返回 `Void?` 类型的值，这使您能够与 `nil` 进行比较，以查看属性是否成功设置：

```swift
if (john.residence?.address = someAddress) != nil {
    print("It was possible to set the address.")
} else {
    print("It was not possible to set the address.")
}
// Prints "It was not possible to set the address."
```

## [通过可选链访问下标](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/optionalchaining#Accessing-Subscripts-Through-Optional-Chaining)

您可以使用可选链来尝试从可选值的下标中检索和设置值，并检查该下标调用是否成功。

> 注意
>
> 当您通过可选链访问可选值上的下标时，您应该将问号放在下标的括号之前，而不是之后。可选链的问号总是紧跟在表达式中可选的部分之后。

下面的示例尝试使用在 `Residence` 类上定义的下标，从 `john.residence` 属性的 `rooms` 数组中检索第一个房间的名称。因为 `john.residence` 当前是 `nil` ，所以下标调用失败：

```swift
if let firstRoomName = john.residence?[0].name {
    print("The first room name is \(firstRoomName).")
} else {
    print("Unable to retrieve the first room name.")
}
// Prints "Unable to retrieve the first room name."
```

在此下标调用中，可选连锁的问号紧接在 `john.residence` 之后，位于下标括号之前，因为 `john.residence` 是正在尝试进行可选连锁的可选值。

类似地，您可以尝试通过带有可选连锁的下标设置新值：

```swift
john.residence?[0] = Room(name: "Bathroom")
```

此下标设置尝试也失败，因为 `residence` 当前是 `nil` 。

如果您创建并将一个实际的 `Residence` 实例赋值给 `john.residence` ，并在其 `rooms` 数组中包含一个或多个 `Room` 实例，您可以通过可选链使用 `Residence` 下标访问 `rooms` 数组中的实际项：

```swift
let johnsHouse = Residence()
johnsHouse.rooms.append(Room(name: "Living Room"))
johnsHouse.rooms.append(Room(name: "Kitchen"))
john.residence = johnsHouse


if let firstRoomName = john.residence?[0].name {
    print("The first room name is \(firstRoomName).")
} else {
    print("Unable to retrieve the first room name.")
}
// Prints "The first room name is Living Room."
```

### [访问可选类型的下标](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/optionalchaining#Accessing-Subscripts-of-Optional-Type)

如果一个下标返回一个可选类型的值——例如 Swift 的 `Dictionary` 类型的键下标——在下标的关闭括号后放置一个问号，以链式调用其可选返回值：

```swift
var testScores = ["Dave": [86, 82, 84], "Bev": [79, 94, 81]]
testScores["Dave"]?[0] = 91
testScores["Bev"]?[0] += 1
testScores["Brian"]?[0] = 72
// the "Dave" array is now [91, 82, 84] and the "Bev" array is now [80, 94, 81]
```

上面的例子定义了一个名为 `testScores` 的字典，其中包含两个键值对，将 `String` 键映射到一个 `Int` 值的数组。该例子使用可选链来将 `"Dave"` 数组中的第一个项目设置为 `91` ；将 `"Bev"` 数组中的第一个项目增加 `1` ；以及尝试为 `"Brian"` 的键设置数组中的第一个项目。前两个调用成功，因为 `testScores` 字典包含 `"Dave"` 和 `"Bev"` 的键。第三个调用失败，因为 `testScores` 字典不包含 `"Brian"` 的键。

## [链接多个级别的可选链](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/optionalchaining#Linking-Multiple-Levels-of-Chaining)

您可以将多个级别的可选链链接在一起，以深入到模型中的属性、方法和下标。然而，多个级别的可选链并不会为返回值增加更多的可选性。

换句话说：

- 如果您尝试检索的类型不是可选的，它将因为可选链而变为可选。
- 如果您尝试检索的类型已经是可选的，那么由于链式调用，它不会变得更加可选。

因此：

- 如果您尝试通过可选链获取一个 `Int` 值，无论使用多少级链，始终返回一个 `Int?` 。
- 同样，如果您尝试通过可选链检索 `Int?` 值，则始终返回 `Int?` ，无论使用了多少级链式调用。

下面的示例尝试访问 `john` 的 `residence` 属性的 `address` 属性的 `street` 属性。这里使用了两个级别的可选链，以链式访问 `residence` 和 `address` 属性，这两个属性都是可选类型：

```swift
if let johnsStreet = john.residence?.address?.street {
    print("John's street name is \(johnsStreet).")
} else {
    print("Unable to retrieve the address.")
}
// Prints "Unable to retrieve the address."
```

`john.residence` 的值当前包含一个有效的 `Residence` 实例。然而， `john.residence.address` 的值当前是 `nil` 。因此，对 `john.residence?.address?.street` 的调用失败。

注意在上述示例中，您正尝试检索 `street` 属性的值。该属性的类型为 `String?` 。因此， `john.residence?.address?.street` 的返回值也是 `String?` ，尽管在应用了两个级别的可选链的同时，还考虑了该属性的基础可选类型。

如果您将实际的 `Address` 实例设置为 `john.residence.address` 的值，并为地址的 `street` 属性设置实际值，则可以通过多级可选链访问 `street` 属性的值：

```swift
let johnsAddress = Address()
johnsAddress.buildingName = "The Larches"
johnsAddress.street = "Laurel Street"
john.residence?.address = johnsAddress


if let johnsStreet = john.residence?.address?.street {
    print("John's street name is \(johnsStreet).")
} else {
    print("Unable to retrieve the address.")
}
// Prints "John's street name is Laurel Street."
```

在这个例子中，尝试设置 `john.residence` 的 `address` 属性将成功，因为 `john.residence` 的值当前包含一个有效的 `Residence` 实例。

## [可选返回值的方法链调用](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/optionalchaining#Chaining-on-Methods-with-Optional-Return-Values)

之前的示例展示了如何通过可选链检索可选类型属性的值。您还可以使用可选链调用返回可选类型值的方法，并在需要时对该方法的返回值进行链式调用。

下面的示例通过可选链调用了 `Address` 类的 `buildingIdentifier()` 方法。该方法返回类型为 `String?` 的值。如上所述，该方法调用在可选链之后的最终返回类型也是 `String?` :

```swift
if let buildingIdentifier = john.residence?.address?.buildingIdentifier() {
    print("John's building identifier is \(buildingIdentifier).")
}
// Prints "John's building identifier is The Larches."
```

如果您想在此方法的返回值上执行进一步的可选链操作，请将可选链问号放在方法的括号后面：

```swift
if let beginsWithThe =
    john.residence?.address?.buildingIdentifier()?.hasPrefix("The") {
    if beginsWithThe {
        print("John's building identifier begins with \"The\".")
    } else {
        print("John's building identifier doesn't begin with \"The\".")
    }
}
// Prints "John's building identifier begins with "The"."
```

> 注意
>
> 在上面的例子中，您将可选链操作符问号放在括号后面，因为您正在链接的可选值是 `buildingIdentifier()` 方法的返回值，而不是 `buildingIdentifier()` 方法本身。
