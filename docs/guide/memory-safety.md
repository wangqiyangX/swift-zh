---
description: 构建您的代码以避免在访问内存时发生冲突。
---

# 内存安全

> 构建您的代码以避免在访问内存时发生冲突。

默认情况下，Swift 防止在您的代码中发生不安全的行为。例如，Swift 确保变量在使用之前已被初始化，内存在被释放后不再被访问，并且数组索引会检查越界错误。

Swift 还确保对同一内存区域的多次访问不会发生冲突，要求修改内存中某个位置的代码必须对该内存具有独占访问权限。由于 Swift 自动管理内存，大多数情况下您不必考虑访问内存。然而，了解潜在冲突可能发生的位置很重要，这样您就可以避免编写具有冲突内存访问的代码。如果您的代码确实包含冲突，您将会遇到编译时或运行时错误。

## [理解对内存的冲突访问](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/memorysafety#Understanding-Conflicting-Access-to-Memory)

当您在代码中执行诸如设置变量的值或将参数传递给函数等操作时，会发生对内存的访问。例如，以下代码同时包含读取访问和写入访问：

```swift
// A write access to the memory where one is stored.
var one = 1


// A read access from the memory where one is stored.
print("We're number \(one)!")
```

当代码的不同部分试图同时访问内存中的同一位置时，可能会发生对内存的冲突访问。对内存中同一位置的多次访问可能会产生不可预测或不一致的行为。在 Swift 中，有一些方法可以修改跨越多行代码的值，这使得在自身修改的过程中尝试访问一个值成为可能。

通过考虑如何更新写在纸上的预算，您可以看到一个类似的问题。更新预算是一个两步过程：首先您添加项目的名称和价格，然后您更改总金额以反映当前列表上的项目。在更新之前和之后，您可以从预算中读取任何信息并得到正确的答案，如下图所示。

![memory_shopping](https://docs.swift.org/swift-book/images/org.swift.tspl/memory_shopping@2x.png){.light-only}
![memory_shopping~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/memory_shopping~dark@2x.png){.dark-only}

在您将项目添加到预算的过程中，它处于临时的无效状态，因为总金额尚未更新以反映新添加的项目。在添加项目的过程中读取总金额会给您不正确的信息。

此示例还展示了您在修复内存冲突访问时可能遇到的挑战：有时解决冲突的方式有多种，这会产生不同的答案，并且并不总是显而易见哪种答案是正确的。在这个例子中，根据您是希望获得原始总金额还是更新后的总金额，5 美元或 320 美元都可能是正确答案。在您能够修复冲突访问之前，您必须确定它打算执行什么。

> 注意
>
> 如果您编写过并发或多线程代码，内存的冲突访问可能是一个熟悉的问题。然而，这里讨论的冲突访问可以发生在单个线程上，并不涉及并发或多线程代码。

如果您在单个线程内对内存有冲突访问，Swift 保证您将在编译时或运行时遇到错误。对于多线程代码，请使用线程消毒器（Thread Sanitizer）来帮助检测线程之间的冲突访问。

### [内存访问的特征](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/memorysafety#Characteristics-of-Memory-Access)

在冲突访问的上下文中，有三种内存访问特征需要考虑：访问是读取还是写入、访问的持续时间以及正在访问的内存位置。具体来说，如果您拥有满足以下所有条件的两个访问，则会发生冲突：

- 访问不是同时读取，也不是同时原子操作。
- 它们访问内存中的同一位置。
- 它们的持续时间重叠。

读取和写入访问之间的区别通常是显而易见的：写入访问会改变内存中的位置，而读取访问则不会。内存中的位置指的是正在被访问的内容——例如，变量、常量或属性。内存访问的持续时间要么是瞬时的，要么是长期的。

如果对 `Atomic` 或 `AtomicLazyReference` 的调用是对原子操作的调用，或者它仅使用 C 原子操作，则访问是原子的；否则它是非原子的。有关 C 原子函数的列表，请参见 `stdatomic(3)` 手册页。

如果在访问开始后但在结束之前不可能有其他代码运行，则访问是瞬时的。从本质上讲，两个瞬时访问不能同时发生。大多数内存访问是瞬时的。例如，下面代码列表中的所有读写访问都是瞬时的：

```swift
func oneMore(than number: Int) -> Int {
    return number + 1
}


var myNumber = 1
myNumber = oneMore(than: myNumber)
print(myNumber)
// Prints "2"
```

然而，有几种访问内存的方法，称为长期访问，它们跨越其他代码的执行。瞬时访问和长期访问之间的区别在于，在长期访问开始后但在结束之前，其他代码可以运行，这称为重叠。长期访问可以与其他长期访问和瞬时访问重叠。

重叠访问主要出现在使用输入输出参数的函数和方法或结构的变异方法的代码中。使用长期访问的特定类型的 Swift 代码将在下面的部分中讨论。

## [对入参和出参的冲突访问](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/memorysafety#Conflicting-Access-to-In-Out-Parameters)

一个函数对其所有入参和出参具有长期写入访问权限。入参和出参的写入访问在所有非入参和出参被评估后开始，并且持续整个函数调用的时间。如果有多个入参和出参，写入访问的开始顺序与参数出现的顺序相同。

这种长期写入访问的一个结果是，即使范围规则和访问控制在其他情况下允许，也无法访问作为入参和出参传递的原始变量——对原始变量的任何访问都会造成冲突。例如：

```swift
var stepSize = 1


func increment(_ number: inout Int) {
    number += stepSize
}


increment(&stepSize)
// Error: conflicting accesses to stepSize
```

在上面的代码中， `stepSize` 是一个全局变量，并且通常可以从 `increment(_:)` 中访问。然而，对 `stepSize` 的读取访问与对 `number` 的写入访问重叠。如下面的图所示， `number` 和 `stepSize` 都指向内存中的同一个位置。读取和写入访问指向相同的内存并且它们重叠，产生了冲突。

![memory_increment](https://docs.swift.org/swift-book/images/org.swift.tspl/memory_increment@2x.png){.light-only}
![memory_increment~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/memory_increment~dark@2x.png){.dark-only}

解决此冲突的一种方法是显式地复制 `stepSize` :

```swift
// Make an explicit copy.
var copyOfStepSize = stepSize
increment(&copyOfStepSize)


// Update the original.
stepSize = copyOfStepSize
// stepSize is now 2
```

当您在调用 `increment(_:)` 之前复制 `stepSize` 时，很明显 `copyOfStepSize` 的值是由当前步长增加的。读取访问在写入访问开始之前结束，因此没有冲突。

长期对输入输出参数的写访问的另一个后果是，将单个变量作为同一函数多个输入输出参数的参数传递会产生冲突。例如：

```swift
func balance(_ x: inout Int, _ y: inout Int) {
    let sum = x + y
    x = sum / 2
    y = sum - x
}
var playerOneScore = 42
var playerTwoScore = 30
balance(&playerOneScore, &playerTwoScore)  // OK
balance(&playerOneScore, &playerOneScore)
// Error: conflicting accesses to playerOneScore
```

上面的 `balance(_:_:)` 函数修改它的两个参数，以便将总值均匀地分配给它们。用 `playerOneScore` 和 `playerTwoScore` 作为参数调用它不会产生冲突——有两个时间上重叠的写访问，但它们访问的是内存中的不同位置。相比之下，将 `playerOneScore` 作为两个参数的值传递会产生冲突，因为它试图同时对内存中的同一位置进行两次写访问。

> 注意
>
> 由于运算符是函数，它们也可以长期访问其输入输出参数。例如，如果 `balance(_:_:)` 是一个名为 `<^>` 的运算符函数，编写 `playerOneScore <^> playerOneScore` 将导致与 `balance(&playerOneScore, &playerOneScore)` 相同的冲突。

## [方法中对 self 的冲突访问](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/memorysafety#Conflicting-Access-to-self-in-Methods)

结构上的一个变更方法在方法调用期间对 `self` 具有写入访问权限。例如，考虑一个游戏，其中每个玩家都有一个健康值，当受到伤害时会减少，以及一个能量值，当使用特殊能力时会减少。

```swift
struct Player {
    var name: String
    var health: Int
    var energy: Int


    static let maxHealth = 10
    mutating func restoreHealth() {
        health = Player.maxHealth
    }
}
```

在上面的 `restoreHealth()` 方法中，对 `self` 的写访问从方法开始时开始，并持续到方法返回。在这种情况下， `restoreHealth()` 中没有其他代码可能会与 `Player` 实例的属性产生重叠访问。下面的 `shareHealth(with:)` 方法将另一个 `Player` 实例作为输入输出参数，从而创造了重叠访问的可能性。

```swift
extension Player {
    mutating func shareHealth(with teammate: inout Player) {
        balance(&teammate.health, &health)
    }
}


var oscar = Player(name: "Oscar", health: 10, energy: 10)
var maria = Player(name: "Maria", health: 5, energy: 10)
oscar.shareHealth(with: &maria)  // OK
```

在上面的示例中，调用 `shareHealth(with:)` 方法让 Oscar 的玩家与 Maria 的玩家共享健康不会引起冲突。在方法调用期间对 `oscar` 有写入访问，因为 `oscar` 是在变更方法中 `self` 的值，并且在相同的时间段内对 `maria` 也有写入访问，因为 `maria` 被作为一个输入输出参数传递。如下图所示，它们访问的是内存中的不同位置。尽管这两个写入访问在时间上重叠，但它们并不冲突。

![memory_share_health_maria](https://docs.swift.org/swift-book/images/org.swift.tspl/memory_share_health_maria@2x.png){.light-only}
![memory_share_health_maria~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/memory_share_health_maria~dark@2x.png){.dark-only}

但是，如果将 `oscar` 作为参数传递给 `shareHealth(with:)` ，那么就会发生冲突：

```swift
oscar.shareHealth(with: &oscar)
// Error: conflicting accesses to oscar
```

变更方法在方法的整个执行时间内需要对 `self` 进行写入访问，而输入输出参数在相同的时间段内需要对 `teammate` 进行写入访问。在方法中， `self` 和 `teammate` 都指向内存中的同一个位置——如下图所示。这两个写入访问指向相同的内存并且它们重叠，产生了冲突。

![memory_share_health_oscar](https://docs.swift.org/swift-book/images/org.swift.tspl/memory_share_health_oscar@2x.png){.light-only}
![memory_share_health_oscar~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/memory_share_health_oscar~dark@2x.png){.dark-only}

## [属性的冲突访问](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/memorysafety#Conflicting-Access-to-Properties)

类型如结构、元组和枚举由单个组成值构成，例如结构的属性或元组的元素。由于这些是值类型，修改值的任何部分都会修改整个值，这意味着对其中一个属性的读或写访问需要对整个值的读或写访问。例如，对元组元素的重叠写访问会产生冲突：

```swift
var playerInformation = (health: 10, energy: 20)
balance(&playerInformation.health, &playerInformation.energy)
// Error: conflicting access to properties of playerInformation
```

在上面的示例中，对元组元素调用 `balance(_:_:)` 会产生冲突，因为对 `playerInformation` 存在重叠的写访问。 `playerInformation.health` 和 `playerInformation.energy` 都作为输入输出参数传递，这意味着 `balance(_:_:)` 在函数调用期间需要对它们的写访问。在这两种情况下，对元组元素的写访问需要对整个元组的写访问。这意味着对 `playerInformation` 的两个写访问的持续时间重叠，导致冲突。

下面的代码表明，对于存储在全局变量中的结构体的重叠写访问，会出现相同的错误。

```swift
var holly = Player(name: "Holly", health: 10, energy: 10)
balance(&holly.health, &holly.energy)  // Error
```

在实践中，对结构体属性的大多数访问可以安全地重叠。例如，如果上面示例中的变量 `holly` 被更改为局部变量而不是全局变量，编译器可以证明对结构体存储属性的重叠访问是安全的：

```swift
func someFunction() {
    var oscar = Player(name: "Oscar", health: 10, energy: 10)
    balance(&oscar.health, &oscar.energy)  // OK
}
```

在上面的示例中，Oscar 的健康和能量作为两个输入输出参数传递给 `balance(_:_:)` 。编译器可以证明内存安全得以保持，因为这两个存储属性没有以任何方式相互作用。

对结构体属性的重叠访问的限制并不总是必要的，以保持内存安全。内存安全是期望的保证，但独占访问是比内存安全更严格的要求——这意味着某些代码保持内存安全，即使它违反了对内存的独占访问。如果编译器可以证明对内存的非独占访问仍然是安全的，Swift 允许这种内存安全的代码。具体来说，如果满足以下条件，它可以证明对结构体属性的重叠访问是安全的：

- 您仅访问实例的储存属性，而不是计算属性或类属性。
- 结构是局部变量的值，而不是全局变量的值。
- 结构要么没有被任何闭包捕获，要么仅被非逃逸闭包捕获。

如果编译器无法证明访问是安全的，它将不允许该访问。
