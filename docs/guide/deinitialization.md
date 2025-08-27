# 反初始化

> 释放需要自定义清理的资源。

在类实例被释放之前会立即调用反初始化器。您使用 `deinit` 关键字编写反初始化器，类似于使用 `init` 关键字编写初始化器。反初始化器仅在类类型上可用。

## [如何进行反初始化](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/deinitialization#How-Deinitialization-Works)

Swift 会在实例不再需要时自动释放它们，以释放资源。Swift 通过自动引用计数 (ARC) 处理实例的内存管理，如《自动引用计数》中所述。通常，当实例被释放时，您不需要执行手动清理。然而，当您处理自己的资源时，您可能需要自己执行一些额外的清理。例如，如果您创建一个自定义类来打开文件并向其中写入一些数据，您可能需要在类实例被释放之前关闭文件。

类定义每个类最多可以有一个反初始化器。反初始化器不接受任何参数，并且不带括号编写：

```swift
deinit {
    // perform the deinitialization
}
```

反初始化器会在实例释放之前自动调用。您不能自己调用反初始化器。超类的反初始化器会被其子类继承，并且超类的反初始化器会在子类反初始化器实现的末尾自动调用。超类的反初始化器总是会被调用，即使子类没有提供自己的反初始化器。

因为在调用析构函数之前，实例不会被释放，所以析构函数可以访问被调用实例的所有属性，并可以根据这些属性修改其行为（例如查找需要关闭的文件的名称）。

## [析构函数的实际应用](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/deinitialization#Deinitializers-in-Action)

这是一个析构函数实际应用的示例。这个示例定义了两种新类型， `Bank` 和 `Player` ，用于一个简单的游戏。 `Bank` 类管理一种虚构的货币，其流通量永远不会超过 10,000 硬币。游戏中只能有一个 `Bank` ，因此 `Bank` 被实现为一个具有类型属性和方法的类，用于存储和管理其当前状态：

```swift
class Bank {
    static var coinsInBank = 10_000
    static func distribute(coins numberOfCoinsRequested: Int) -> Int {
        let numberOfCoinsToVend = min(numberOfCoinsRequested, coinsInBank)
        coinsInBank -= numberOfCoinsToVend
        return numberOfCoinsToVend
    }
    static func receive(coins: Int) {
        coinsInBank += coins
    }
}
```

`Bank` 通过其 `coinsInBank` 属性跟踪它所持有的硬币当前数量。它还提供了两种方法 — `distribute(coins:)` 和 `receive(coins:)` — 来处理硬币的分配和收集。

`distribute(coins:)` 方法在分发硬币之前检查银行中是否有足够的硬币。如果没有足够的硬币， `Bank` 返回的数量会小于请求的数量（如果银行中没有剩余硬币，则返回零）。它返回一个整数值以指示实际提供的硬币数量。

`receive(coins:)` 方法简单地将接收到的硬币数量重新添加到银行的硬币存储中。

`Player` 类描述了游戏中的玩家。每个玩家在任何时候都有一定数量的硬币存储在他们的钱包中。这由玩家的 `coinsInPurse` 属性表示：

```swift
class Player {
    var coinsInPurse: Int
    init(coins: Int) {
        coinsInPurse = Bank.distribute(coins: coins)
    }
    func win(coins: Int) {
        coinsInPurse += Bank.distribute(coins: coins)
    }
    deinit {
        Bank.receive(coins: coinsInPurse)
    }
}
```

每个 `Player` 实例在初始化时都以指定数量的硬币从银行中初始化起始津贴，尽管如果没有足够的硬币可用， `Player` 实例可能会收到少于该数量的硬币。

The `Player` class defines a `win(coins:)` method, which retrieves a certain number of coins from the bank and adds them to the player’s purse. The `Player` class also implements a deinitializer, which is called just before a `Player` instance is deallocated. Here, the deinitializer simply returns all of the player’s coins to the bank: `Player` 类定义了一个 `win(coins:)` 方法，该方法从银行中检索一定数量的硬币并将其添加到玩家的钱包中。`Player` 类还实现了一个析构函数，该函数在 `Player` 实例被释放之前调用。在这里，析构函数简单地将所有玩家的硬币返回给银行：

```swift
var playerOne: Player? = Player(coins: 100)
print("A new player has joined the game with \(playerOne!.coinsInPurse) coins")
// Prints "A new player has joined the game with 100 coins"
print("There are now \(Bank.coinsInBank) coins left in the bank")
// Prints "There are now 9900 coins left in the bank"
```

A new `Player` instance is created, with a request for 100 coins if they’re available. This `Player` instance is stored in an optional `Player` variable called `playerOne` . An optional variable is used here, because players can leave the game at any point. The optional lets you track whether there’s currently a player in the game. 创建一个新的 `Player` 实例，如果有的话请求 100 个硬币。这个 `Player` 实例存储在一个名为 `playerOne` 的可选 `Player` 变量中。这里使用可选变量，因为玩家可以在任何时候离开游戏。可选变量让您跟踪当前游戏中是否有玩家。

Because `playerOne` is an optional, it’s qualified with an exclamation point ( `!` ) when its `coinsInPurse` property is accessed to print its default number of coins, and whenever its `win(coins:)` method is called: 因为 `playerOne` 是一个可选的，所以在访问其 `coinsInPurse` 属性以打印其默认硬币数量时，它用感叹号 ( `!` ) 进行限定，并且每当调用其 `win(coins:)` 方法时：

```swift
playerOne!.win(coins: 2_000)
print("PlayerOne won 2000 coins & now has \(playerOne!.coinsInPurse) coins")
// Prints "PlayerOne won 2000 coins & now has 2100 coins"
print("The bank now only has \(Bank.coinsInBank) coins left")
// Prints "The bank now only has 7900 coins left"
```

Here, the player has won 2,000 coins. The player’s purse now contains 2,100 coins, and the bank has only 7,900 coins left. 在这里，玩家赢得了 2,000 个硬币。玩家的钱包现在包含 2,100 个硬币，而银行只剩下 7,900 个硬币。

```swift
playerOne = nil
print("PlayerOne has left the game")
// Prints "PlayerOne has left the game"
print("The bank now has \(Bank.coinsInBank) coins")
// Prints "The bank now has 10000 coins"
```

玩家现在已离开游戏。这通过将可选的 `playerOne` 变量设置为 `nil` 来表示，意味着“没有 `Player` 实例。”在发生这一点时， `playerOne` 变量对 `Player` 实例的引用被打断。没有其他属性或变量仍在引用 `Player` 实例，因此它被释放以腾出内存。在此之前，它的反初始化器会自动调用，并将其金币返回给银行。
