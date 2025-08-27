# 错误处理

> 响应并从错误中恢复。

错误处理是响应和从程序中的错误条件中恢复的过程。Swift 提供了对抛出、捕获、传播和操作可恢复错误的第一类支持。

某些操作并不保证总是完成执行或产生有用的输出。可选值用于表示缺少值，但当操作失败时，了解导致失败的原因通常是有用的，以便您的代码可以相应地做出响应。

作为一个例子，考虑从磁盘上的文件读取和处理数据的任务。这个任务可能会以多种方式失败，包括指定路径下不存在文件、文件没有读取权限，或者文件没有以兼容格式编码。区分这些不同的情况使程序能够解决一些错误，并向用户传达它无法解决的任何错误。

> 注意
>
> 在 Swift 中的错误处理与使用 `NSError` 类的 Cocoa 和 Objective-C 中的错误处理模式相互作用。有关此类的更多信息，请参阅在 Swift 中处理 Cocoa 错误。

## [表示和抛出错误](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/errorhandling#Representing-and-Throwing-Errors)

在 Swift 中，错误由符合 `Error` 协议的类型的值表示。这个空协议表示一个类型可以用于错误处理。

Swift 枚举特别适合于建模一组相关的错误条件，通过关联值允许传达关于错误性质的附加信息。例如，这里是如何表示游戏中操作自动售货机的错误条件：

```swift
enum VendingMachineError: Error {
    case invalidSelection
    case insufficientFunds(coinsNeeded: Int)
    case outOfStock
}
```

抛出一个错误让您表示发生了一些意想不到的事情，正常的执行流程无法继续。您使用 `throw` 语句来抛出一个错误。例如，下面的代码抛出一个错误，表示自动售货机需要额外的五个硬币：

```swift
throw VendingMachineError.insufficientFunds(coinsNeeded: 5)
```

## [处理错误](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/errorhandling#Handling-Errors)

当抛出错误时，某些周围的代码必须负责处理该错误——例如，通过纠正问题、尝试替代方法或通知用户失败。

在 Swift 中处理错误有四种方法。您可以将错误从函数传播到调用该函数的代码，使用 `do` - `catch` 语句处理错误，将错误作为可选值处理，或断言错误不会发生。每种方法在下面的部分中都有描述。

当函数抛出错误时，它会改变程序的流程，因此您可以快速识别代码中可能抛出错误的地方是很重要的。要识别代码中的这些地方，请在调用可能抛出错误的函数、方法或初始化器的代码前写上 `try` 关键字——或 `try?` 或 `try!` 变体。这些关键字在下面的部分中有描述。

> 注意
>
> 在 Swift 中，错误处理类似于其他语言中的异常处理，使用 `try` 、 `catch` 和 `throw` 关键字。与许多语言（包括 Objective-C）中的异常处理不同，Swift 中的错误处理不涉及展开调用栈，这是一个可能消耗大量计算资源的过程。因此， `throw` 语句的性能特征与 `return` 语句相当。

### [使用抛出函数传播错误](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/errorhandling#Propagating-Errors-Using-Throwing-Functions)

要指示一个函数、方法或初始化器可以抛出错误，可以在函数的声明中参数后写入 `throws` 关键字。用 `throws` 标记的函数称为抛出函数。如果函数指定了返回类型，则在返回箭头（ `->` ）之前写入 `throws` 关键字。

```swift
func canThrowErrors() throws -> String


func cannotThrowErrors() -> String
```

抛出函数将内部抛出的错误传播到调用它的作用域。

> 注意
>
> 只有抛出函数才能传播错误。任何在非抛出函数内部抛出的错误必须在函数内部处理。

在下面的示例中， `VendingMachine` 类有一个 `vend(itemNamed:)` 方法，如果请求的项目不可用、缺货或费用超过当前存入的金额，则抛出适当的 `VendingMachineError` ：

```swift
struct Item {
    var price: Int
    var count: Int
}


class VendingMachine {
    var inventory = [
        "Candy Bar": Item(price: 12, count: 7),
        "Chips": Item(price: 10, count: 4),
        "Pretzels": Item(price: 7, count: 11)
    ]
    var coinsDeposited = 0


    func vend(itemNamed name: String) throws {
        guard let item = inventory[name] else {
            throw VendingMachineError.invalidSelection
        }


        guard item.count > 0 else {
            throw VendingMachineError.outOfStock
        }


        guard item.price <= coinsDeposited else {
            throw VendingMachineError.insufficientFunds(coinsNeeded: item.price - coinsDeposited)
        }


        coinsDeposited -= item.price


        var newItem = item
        newItem.count -= 1
        inventory[name] = newItem


        print("Dispensing \(name)")
    }
}
```

`vend(itemNamed:)` 方法的实现使用 `guard` 语句提前退出方法，并在未满足购买零食的任何要求时抛出适当的错误。因为 `throw` 语句会立即转移程序控制，只有在满足所有这些要求时，才会出售商品。

因为 `vend(itemNamed:)` 方法会传播它抛出的任何错误，因此调用此方法的任何代码必须处理这些错误——使用 `do` - `catch` 语句、 `try?` 或 `try!` ——或者继续传播它们。例如，下面示例中的 `buyFavoriteSnack(person:vendingMachine:)` 也是一个抛出函数， `vend(itemNamed:)` 方法抛出的任何错误将传播到 `buyFavoriteSnack(person:vendingMachine:)` 函数被调用的地方。

```swift
let favoriteSnacks = [
    "Alice": "Chips",
    "Bob": "Licorice",
    "Eve": "Pretzels",
]
func buyFavoriteSnack(person: String, vendingMachine: VendingMachine) throws {
    let snackName = favoriteSnacks[person] ?? "Candy Bar"
    try vendingMachine.vend(itemNamed: snackName)
}
```

在这个例子中， `buyFavoriteSnack(person: vendingMachine:)` 函数查找给定人的最爱零食，并通过调用 `vend(itemNamed:)` 方法尝试为他们购买。因为 `vend(itemNamed:)` 方法可能会抛出错误，所以在它前面加上了 `try` 关键字进行调用。

抛出初始化器可以像抛出函数一样传播错误。例如，下面列表中 `PurchasedSnack` 结构的初始化器在初始化过程中调用了一个抛出函数，并通过将遇到的任何错误传播给其调用者来处理这些错误。

```swift
struct PurchasedSnack {
    let name: String
    init(name: String, vendingMachine: VendingMachine) throws {
        try vendingMachine.vend(itemNamed: name)
        self.name = name
    }
}
```

### [使用 Do-Catch 处理错误](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/errorhandling#Handling-Errors-Using-Do-Catch)

您使用 `do` - `catch` 语句通过运行一段代码来处理错误。如果 `do` 子句中的代码抛出错误，它将与 `catch` 子句匹配以确定哪个子句可以处理该错误。

这里是一个 `do` - `catch` 语句的一般形式：

```swift
do {
    try <#expression#>
    <#statements#>
} catch <#pattern 1#> {
    <#statements#>
} catch <#pattern 2#> where <#condition#> {
    <#statements#>
} catch <#pattern 3#>, <#pattern 4#> where <#condition#> {
    <#statements#>
} catch {
    <#statements#>
}
```

在 `catch` 后写一个模式以指示该子句可以处理哪些错误。如果 `catch` 子句没有模式，则该子句匹配任何错误，并将错误绑定到名为 `error` 的局部常量。有关模式匹配的更多信息，请参见 Patterns。

例如，以下代码匹配 `VendingMachineError` 枚举的所有三种情况。

```swift
var vendingMachine = VendingMachine()
vendingMachine.coinsDeposited = 8
do {
    try buyFavoriteSnack(person: "Alice", vendingMachine: vendingMachine)
    print("Success! Yum.")
} catch VendingMachineError.invalidSelection {
    print("Invalid Selection.")
} catch VendingMachineError.outOfStock {
    print("Out of Stock.")
} catch VendingMachineError.insufficientFunds(let coinsNeeded) {
    print("Insufficient funds. Please insert an additional \(coinsNeeded) coins.")
} catch {
    print("Unexpected error: \(error).")
}
// Prints "Insufficient funds. Please insert an additional 2 coins."
```

在上述示例中， `buyFavoriteSnack(person:vendingMachine:)` 函数在 `try` 表达式中被调用，因为它可能会抛出错误。如果抛出错误，则执行立即转移到 `catch` 子句，这些子句决定是否允许传播继续。如果没有模式匹配，则错误会被最终的 `catch` 子句捕获，并绑定到一个本地 `error` 常量。如果没有抛出错误，则执行 `do` 语句中的其余语句。

`catch` 子句不必处理 `do` 子句中可能抛出的每一个错误。如果没有 `catch` 子句处理该错误，错误将传播到周围的作用域。然而，传播的错误必须由某个周围的作用域处理。在一个不抛出错误的函数中，封闭的 `do` - `catch` 语句必须处理该错误。在一个抛出错误的函数中，封闭的 `do` - `catch` 语句或调用者必须处理该错误。如果错误在未被处理的情况下传播到顶层作用域，您将会遇到运行时错误。

例如，上面的示例可以写成这样，以便任何不是 `VendingMachineError` 的错误都被调用函数捕获：

```swift
func nourish(with item: String) throws {
    do {
        try vendingMachine.vend(itemNamed: item)
    } catch is VendingMachineError {
        print("Couldn't buy that from the vending machine.")
    }
}


do {
    try nourish(with: "Beet-Flavored Chips")
} catch {
    print("Unexpected non-vending-machine-related error: \(error)")
}
// Prints "Couldn't buy that from the vending machine."
```

在 `nourish(with:)` 函数中，如果 `vend(itemNamed:)` 抛出一个属于 `VendingMachineError` 枚举的错误， `nourish(with:)` 通过打印一条消息来处理该错误。否则， `nourish(with:)` 将错误传播到其调用位置。该错误随后由通用 `catch` 子句捕获。

另一种捕获多个相关错误的方法是在 `catch` 后列出它们，用逗号分隔。例如：

```swift
func eat(item: String) throws {
    do {
        try vendingMachine.vend(itemNamed: item)
    } catch VendingMachineError.invalidSelection, VendingMachineError.insufficientFunds, VendingMachineError.outOfStock {
        print("Invalid selection, out of stock, or not enough money.")
    }
}
```

`eat(item:)` 函数列出要捕获的自动售货机错误，其错误文本对应于该列表中的项目。如果抛出列出的三个错误中的任何一个，这个 `catch` 子句通过打印消息来处理它们。任何其他错误都会传播到周围的作用域，包括可能稍后添加的任何自动售货机错误。

### [将错误转换为可选值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/errorhandling#Converting-Errors-to-Optional-Values)

您使用 `try?` 通过将错误转换为可选值来处理错误。如果在评估 `try?` 表达式时抛出错误，则表达式的值为 `nil` 。例如，在以下代码中 `x` 和 `y` 具有相同的值和行为：

```swift
func someThrowingFunction() throws -> Int {
    // ...
}


let x = try? someThrowingFunction()


let y: Int?
do {
    y = try someThrowingFunction()
} catch {
    y = nil
}
```

如果 `someThrowingFunction()` 抛出错误，则 `x` 和 `y` 的值为 `nil` 。否则， `x` 和 `y` 的值为函数返回的值。请注意 `x` 和 `y` 是可选的，类型为 `someThrowingFunction()` 返回的值。在这里，函数返回一个整数，因此 `x` 和 `y` 是可选的整数。

使用 `try?` 让您在想要以相同方式处理所有错误时写出简洁的错误处理代码。例如，以下代码使用几种方法来获取数据，或者在所有方法失败时返回 `nil` 。

```swift
func fetchData() -> Data? {
    if let data = try? fetchDataFromDisk() { return data }
    if let data = try? fetchDataFromServer() { return data }
    return nil
}
```

### [禁用错误传播](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/errorhandling#Disabling-Error-Propagation)

有时您知道一个抛出异常的函数或方法在运行时实际上不会抛出错误。在这些情况下，您可以在表达式之前写 `try!` 来禁用错误传播，并将调用包装在一个运行时断言中，断言不会抛出错误。如果实际上抛出了错误，您将会得到一个运行时错误。

例如，以下代码使用一个 `loadImage(atPath:)` 函数，它在给定路径加载图像资源，或者如果无法加载图像则抛出错误。在这种情况下，由于图像与应用程序一起打包，因此在运行时不会抛出错误，因此禁用错误传播是合适的。

```swift
let photo = try! loadImage(atPath: "./Resources/John Appleseed.jpg")
```

## [指定错误类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/errorhandling#Specifying-the-Error-Type)

上述所有示例使用了最常见的错误处理方式，在这种方式中，您的代码抛出的错误可以是符合 `Error` 协议的任何类型的值。这种方法符合现实情况，因为您无法事先知道在代码运行时可能发生的每个错误，特别是在传播其他地方抛出的错误时。它还反映了错误可能随时间变化的事实。库的新版本 — 包括您的依赖项使用的库 — 可能会抛出新错误，真实用户配置的丰富复杂性可能会暴露在开发或测试过程中未能看出的故障模式。上述示例中的错误处理代码始终包括一个默认情况，以处理没有特定 `catch` 子句的错误。

大多数 Swift 代码并未指定它抛出的错误的类型。然而，您可能会在以下特殊情况下限制代码只抛出一种特定类型的错误：

- 在运行代码于不支持动态内存分配的嵌入式系统时。抛出一个 `any Error` 实例或另一个封装协议类型需要在运行时分配内存来存储错误。相比之下，抛出特定类型的错误允许 Swift 避免为错误分配堆内存。
- 当错误是某个代码单元（如库）的实现细节时，并且不是该代码的接口的一部分。因为错误仅来自库，而不是其他依赖项或库的客户端，所以您可以列出所有可能的失败。而且因为这些错误是库的实现细节，因此它们总是在该库内处理。
- 在仅传播由泛型参数描述的错误的代码中，例如一个接受闭包参数并传播该闭包中的任何错误的函数。关于传播特定错误类型与使用 `rethrows` 的比较，请参见重新抛出函数和方法。

例如，考虑总结评级并使用以下错误类型的代码：

```swift
enum StatisticsError: Error {
    case noRatings
    case invalidRating(Int)
}
```

要指定一个函数仅抛出 `StatisticsError` 值作为其错误，您在声明该函数时写 `throws(StatisticsError)` 而不是仅仅 `throws` 。这种语法也称为有类型的抛出，因为您在声明中在 \{{3 \}} 之后写错误类型。例如，下面的函数将 `StatisticsError` 值作为其错误抛出。

```swift
func summarize(_ ratings: [Int]) throws(StatisticsError) {
    guard !ratings.isEmpty else { throw .noRatings }


    var counts = [1: 0, 2: 0, 3: 0]
    for rating in ratings {
        guard rating > 0 && rating <= 3 else { throw .invalidRating(rating) }
        counts[rating]! += 1
    }


    print("*", counts[1]!, "-- **", counts[2]!, "-- ***", counts[3]!)
}
```

在上面的代码中， `summarize(_:)` 函数总结了一个在 1 到 3 的范围内表达的评分列表。如果输入无效，此函数会抛出一个 `StatisticsError` 的实例。上面代码中抛出错误的两个地方省略了错误的类型，因为函数的错误类型已经定义。抛出错误时，您可以使用短形式 `throw .noRatings` ，而不是写 `throw StatisticsError.noRatings` 。

当您在函数开始时写一个具体的错误类型时，Swift 会检查您不会抛出任何其他错误。例如，如果您尝试在上面的 `summarize(_:)` 函数中使用本章前面示例中的 `VendingMachineError` ，那么那段代码将在编译时产生错误。

您可以从常规抛出函数中调用一个使用类型抛出函数：

```swift
func someThrowingFunction() -> throws {
    let ratings = [1, 2, 3, 2, 2, 1]
    try summarize(ratings)
}
```

上面的代码没有为 `someThrowingFunction()` 指定错误类型，因此抛出了 `any Error` 。您也可以将错误类型显式写为 `throws(any Error)` ；下面的代码与上面的代码等效：

```swift
func someThrowingFunction() -> throws(any Error) {
    let ratings = [1, 2, 3, 2, 2, 1]
    try summarize(ratings)
}
```

在这段代码中， `someThrowingFunction()` 传播 `summarize(_:)` 抛出的任何错误。来自 `summarize(_:)` 的错误始终是 `StatisticsError` 值，这也是 `someThrowingFunction()` 可以抛出的有效错误。

就像您可以编写一个返回类型为 `Never` 的函数，该函数永远不返回一样，您可以编写一个使用 `throws(Never)` 的函数，该函数永远不抛出：

```swift
func nonThrowingFunction() throws(Never) {
  // ...
}
```

此函数不能抛出，因为不可能创建一个类型为 `Never` 的值来抛出。

除了指定函数的错误类型，您还可以为 `do` - `catch` 语句编写特定的错误类型。例如：

```swift
let ratings = []
do throws(StatisticsError) {
    try summarize(ratings)
} catch {
    switch error {
    case .noRatings:
        print("No ratings available")
    case .invalidRating(let rating):
        print("Invalid rating: \(rating)")
    }
}
// Prints "No ratings available"
```

在此代码中，写 `do throws(StatisticsError)` 表示 `do` - `catch` 语句抛出 `StatisticsError` 值作为其错误。与其他 `do` - `catch` 语句一样， `catch` 子句可以处理所有可能的错误，或将未处理的错误传播给周围的某个范围进行处理。此代码处理了所有错误，使用 `switch` 语句为每个枚举值提供一个案例。与其他没有模式的 `catch` 子句一样，该子句匹配任何错误并将错误绑定到名为 `error` 的局部常量上。因为 `do` - `catch` 语句抛出 `StatisticsError` 值， `error` 是类型为 `StatisticsError` 的值。

上面的 `catch` 子句使用 `switch` 语句来匹配和处理每个可能的错误。如果您尝试向 `StatisticsError` 添加一个新案例而不更新错误处理代码，Swift 会给您一个错误，因为 `switch` 语句将不再是穷举的。对于一个捕获所有自身错误的库，您可以使用这种方法来确保任何新错误都有相应的新代码来处理它们。

如果一个函数或 `do` 块只抛出单一类型的错误，Swift 会推断该代码正在使用类型化抛出。使用这种更简短的语法，您可以将上述的 `do` - `catch` 示例写成如下形式：

```swift
let ratings = []
do {
    try summarize(ratings)
} catch {
    switch error {
    case .noRatings:
        print("No ratings available")
    case .invalidRating(let rating):
        print("Invalid rating: \(rating)")
    }
}
// Prints "No ratings available"
```

尽管上面的 `do` - `catch` 块没有指定抛出什么类型的错误，但 Swift 推断它抛出 `StatisticsError` 。您可以明确地写 `throws(any Error)` 来避免让 Swift 推断类型的抛出。

## [指定清理操作](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/errorhandling#Specifying-Cleanup-Actions)

您使用 `defer` 语句在代码执行离开当前代码块之前执行一组语句。此语句允许您执行任何必要的清理操作，无论执行是如何离开当前代码块的 — 无论是因为抛出错误，还是因为像 `return` 或 `break` 这样的语句。例如，您可以使用 `defer` 语句确保文件描述符被关闭，手动分配的内存被释放。

`defer` 语句延迟执行，直到当前作用域退出。此语句由 `defer` 关键字和稍后要执行的语句组成。延迟的语句中不得包含任何会将控制转移出语句的代码，例如 `break` 或 `return` 语句，或通过抛出错误来实现。延迟操作按它们在源代码中的书写顺序的逆序执行。即，第一条 `defer` 语句中的代码最后执行，第二条 `defer` 语句中的代码倒数第二执行，依此类推。源代码顺序中的最后一条 `defer` 语句首先执行。

```swift
func processFile(filename: String) throws {
    if exists(filename) {
        let file = open(filename)
        defer {
            close(file)
        }
        while let line = try file.readline() {
            // Work with the file.
        }
        // close(file) is called here, at the end of the scope.
    }
}
```

上述示例使用了一个 `defer` 语句，以确保 `open(_:)` 函数有一个对应的调用到 `close(_:)` 。

您可以在没有错误处理代码的情况下使用 `defer` 语句。有关更多信息，请参见 Deferred Actions。
