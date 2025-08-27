---
description: 在另一个类型的范围内定义类型。
---

# 嵌套类型

> 在另一个类型的范围内定义类型。

枚举通常是为了支持特定类或结构的功能而创建的。同样，仅为了在更复杂类型的上下文中使用而定义实用结构也是方便的，以及通常与特定类型一起使用的协议。为此，Swift 允许您定义嵌套类型，您可以在所支持类型的定义中嵌套支持性类型，如枚举、结构和协议。

要在另一种类型中嵌套一种类型，请在其支持的类型的外部大括号内编写其定义。类型可以嵌套到所需的任意级别。

## [嵌套类型的实际应用](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/nestedtypes#Nested-Types-in-Action)

下面的示例定义了一个名为 `BlackjackCard` 的结构，它模拟了在二十一点游戏中使用的扑克牌。 `BlackjackCard` 结构包含两个嵌套的枚举类型，分别称为 `Suit` 和 `Rank` 。

在二十一点游戏中，A 牌的值可以是 1 或 11。这个特性由一个名为 `Values` 的结构表示，该结构嵌套在 `Rank` 枚举中：

```swift
struct BlackjackCard {


    // nested Suit enumeration
    enum Suit: Character {
        case spades = "♠", hearts = "♡", diamonds = "♢", clubs = "♣"
    }


    // nested Rank enumeration
    enum Rank: Int {
        case two = 2, three, four, five, six, seven, eight, nine, ten
        case jack, queen, king, ace
        struct Values {
            let first: Int, second: Int?
        }
        var values: Values {
            switch self {
            case .ace:
                return Values(first: 1, second: 11)
            case .jack, .queen, .king:
                return Values(first: 10, second: nil)
            default:
                return Values(first: self.rawValue, second: nil)
            }
        }
    }


    // BlackjackCard properties and methods
    let rank: Rank, suit: Suit
    var description: String {
        var output = "suit is \(suit.rawValue),"
        output += " value is \(rank.values.first)"
        if let second = rank.values.second {
            output += " or \(second)"
        }
        return output
    }
}
```

`Suit` 枚举描述了四种常见的扑克牌花色，以及一个原始 `Character` 值来表示它们的符号。

`Rank` 枚举描述了十三个可能的扑克牌等级，以及一个原始 `Int` 值来表示它们的面值。（这个原始 `Int` 值对于杰克、皇后、国王和王牌是未使用的。）

如上所述， `Rank` 枚举定义了其自身的进一步嵌套结构，称为 `Values` 。这个结构封装了大多数牌有一个值，但王牌有两个值的事实。 `Values` 结构定义了两个属性来表示这一点：

- `first` ，类型为 `Int`
- `second` ，类型为 `Int?` ，或“可选 `Int` ”

`Rank` 还定义了一个计算属性 `values` ，它返回 `Values` 结构的一个实例。这个计算属性考虑了卡片的等级，并根据其等级初始化一个新的 `Values` 实例，使用适当的值。它为 `jack` 、 `queen` 、 `king` 和 `ace` 使用了特殊值。对于数字卡片，它使用等级的原始 `Int` 值。

`BlackjackCard` 结构本身有两个属性—— `rank` 和 `suit` 。它还定义了一个名为 `description` 的计算属性，该属性使用存储在 `rank` 和 `suit` 中的值来构建卡片名称和值的描述。 `description` 属性使用可选绑定来检查是否有第二个值可显示，如果有，则为该第二个值插入额外的描述细节。

因为 `BlackjackCard` 是一个没有自定义初始化器的结构，所以它有一个隐式的成员初始化器，如《结构类型的成员初始化器》中所述。您可以使用此初始化器来初始化一个名为 `theAceOfSpades` 的新常量：

```swift
let theAceOfSpades = BlackjackCard(rank: .ace, suit: .spades)
print("theAceOfSpades: \(theAceOfSpades.description)")
// Prints "theAceOfSpades: suit is ♠, value is 1 or 11"
```

即使 `Rank` 和 `Suit` 嵌套在 `BlackjackCard` 中，它们的类型也可以从上下文中推断，因此该实例的初始化能够仅通过其案例名称 ( `.ace` 和 `.spades` ) 引用枚举案例。在上面的示例中， `description` 属性正确报告黑桃 A 的值为 `1` 或 `11` 。

## [引用嵌套类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/nestedtypes#Referring-to-Nested-Types)

要在其定义上下文之外使用嵌套类型，请在其名称前加上它嵌套的类型的名称：

```swift
let heartsSymbol = BlackjackCard.Suit.hearts.rawValue
// heartsSymbol is "♡"
```

对于上面的示例，这使得 `Suit` 、 `Rank` 和 `Values` 的名称可以保持简短，因为它们的名称自然由其定义的上下文限定。
