# 下标

> 访问集合的元素。

类、结构和枚举可以定义下标，这是访问集合、列表或序列的成员元素的快捷方式。您可以使用下标通过索引设置和检索值，而无需单独的方法来设置和检索。例如，您可以将 `Array` 实例中的元素访问为 `someArray[index]` ，将 `Dictionary` 实例中的元素访问为 `someDictionary[key]` 。

您可以为单个类型定义多个下标，并根据您传递给下标的索引值的类型选择适当的下标重载。下标不限于单个维度，您可以定义具有多个输入参数的下标，以满足自定义类型的需求。

## [下标语法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/subscripts#Subscript-Syntax)

下标使您能够通过在实例名称后用方括号写入一个或多个值来查询类型的实例。它们的语法类似于实例方法语法和计算属性语法。您使用 `subscript` 关键字编写下标定义，并以与实例方法相同的方式指定一个或多个输入参数和返回类型。与实例方法不同，下标可以是可读写或只读。这种行为通过 `getter` 和 `setter` 以与计算属性相同的方式进行传达：

```swift
subscript(index: Int) -> Int {
    get {
        // 在此返回一个适当的值。
    }
    set(newValue) {
        // 在此执行适当的设置操作。
    }
}
```

`newValue` 的类型与下标的返回值相同。与计算属性一样，您可以选择不指定 `setter` 的 `(newValue)` 参数。如果您自己不提供，您的 setter 会提供一个名为 `newValue` 的默认参数。

与只读计算属性一样，您可以通过删除 `get` 关键字及其大括号来简化只读下标的声明：

```swift
subscript(index: Int) -> Int {
    // 在此返回一个适当的值。
}
```

这是只读下标实现的一个示例，它定义了一个 `TimesTable` 结构来表示整数的 n 倍表：

```swift
struct TimesTable {
    let multiplier: Int
    subscript(index: Int) -> Int {
        return multiplier * index
    }
}
let threeTimesTable = TimesTable(multiplier: 3)
print("6乘3等于 \(threeTimesTable[6])")
// 打印 "6乘3等于 18"
```

在这个示例中，创建了一个 `TimesTable` 的新实例来表示三倍表。这是通过将 `3` 的值传递给结构的 `initializer` 作为实例的 `multiplier` 参数的值来指示的。

您可以通过调用其下标来查询 `threeTimesTable` 实例，如对 `threeTimesTable[6]` 的调用所示。这请求三倍表中的第六个条目，返回的值为 `18` ，或 `3` 乘以 `6` 。

> **注意**
>
> n 次表是基于一个固定的数学规则。将 `threeTimesTable[someIndex]` 设置为新值是不合适的，因此 `TimesTable` 的下标被定义为只读下标。

## [下标用法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/subscripts#Subscript-Usage)

“下标”的确切含义取决于其使用的上下文。**下标通常用作访问集合、列表或序列中的成员元素的快捷方式。**&#x60A8;可以根据特定类或结构的功能以最合适的方式实现下标。

例如，Swift 的 `Dictionary` 类型实现了一个下标，用于设置和检索存储在 `Dictionary` 实例中的值。您可以通过在下标括号内提供字典的键类型的键，并将字典的值类型的值分配给下标，从而在字典中设置一个值：

```swift
var numberOfLegs = ["spider": 8, "ant": 6, "cat": 4]
numberOfLegs["bird"] = 2
```

上面的示例定义了一个名为 `numberOfLegs` 的变量，并用包含三个键值对的字典字面量初始化它。 `numberOfLegs` 字典的类型被推断为 `[String: Int]` 。在创建字典后，此示例使用下标赋值将 `String` 键的 `"bird"` 和 `Int` 值的 `2` 添加到字典中。

有关 `Dictionary` 下标的更多信息，请参阅[访问和修改字典](collection-types.md#accessing-and-modifying-a-dictionary)。

> **注意**
>
> Swift 的 `Dictionary` 类型将其键值下标实现为一个接受和返回**可选类型**的下标。对于上面的 `numberOfLegs` 字典，键值下标接受和返回类型为 `Int?` 的值，或称为“可选整数”。 `Dictionary` 类型使用可选下标类型来建模并非每个键都有值的事实，并提供了一种通过为该键分配 `nil` 值来删除该键的值的方法。

## [下标选项](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/subscripts#Subscript-Options)

下标可以接受任意数量的输入参数，这些输入参数可以是**任何类型**。下标还可以返回任何类型的值。

与函数一样，下标可以接受不同数量的参数，并为其参数提供默认值，如在[可变参数和默认参数值](functions.md#variadic-parameters)中讨论的那样。然而，与函数不同，**下标不能使用输入输出参数(in-out)**。

一个类或结构可以提供**任意数量**的下标实现，适当的下标将根据下标括号内包含的值或值的类型在使用下标时进行推断。多个下标的这种定义称为下标重载。

虽然下标通常只接受一个参数，但如果适合您的类型，您也可以定义一个具有多个参数的下标。以下示例定义了一个 `Matrix` 结构，表示一个二维矩阵的 `Double` 值。 `Matrix` 结构的下标接受两个整数参数：

```swift
struct Matrix {
    let rows: Int, columns: Int
    var grid: [Double]
    init(rows: Int, columns: Int) {
        self.rows = rows
        self.columns = columns
        grid = Array(repeating: 0.0, count: rows * columns)
    }
    func indexIsValid(row: Int, column: Int) -> Bool {
        return row >= 0 && row < rows && column >= 0 && column < columns
    }
    subscript(row: Int, column: Int) -> Double {
        get {
            assert(indexIsValid(row: row, column: column), "Index out of range")
            return grid[(row * columns) + column]
        }
        set {
            assert(indexIsValid(row: row, column: column), "Index out of range")
            grid[(row * columns) + column] = newValue
        }
    }
}
```

`Matrix` 提供了一个初始化器，它接受两个参数，分别称为 `rows` 和 `columns` ，并创建一个足够大的数组以存储 `rows * columns` 个类型为 `Double` 的值。矩阵中的每个位置都被赋予初始值 `0.0` 。为此，数组的大小和初始单元格值 `0.0` 会被传递给一个数组初始化器，该初始化器创建并初始化一个正确大小的新数组。这个初始化器在[创建具有默认值的数组](collection-types.md#创建具有默认值的数组)中有更详细的描述。

您可以通过将适当的行数和列数传递给其初始化器来构造一个新的 `Matrix` 实例：

```swift
var matrix = Matrix(rows: 2, columns: 2)
```

上面的示例创建了一个新的 `Matrix` 实例，具有两行两列。该 `grid` 数组对于这个 `Matrix` 实例实际上是矩阵的扁平化版本，从左上角读取到右下角：

![subscriptMatrix01](https://docs.swift.org/swift-book/images/org.swift.tspl/subscriptMatrix01@2x.png){.light-only}
![subscriptMatrix01~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/subscriptMatrix01~dark@2x.png){.dark-only}

矩阵中的值可以通过将行和列值传递到下标中来设置，值之间用逗号分隔：

```swift
matrix[0, 1] = 1.5
matrix[1, 0] = 3.2
```

这两个语句调用下标的设置器，将 `1.5` 的值设置在矩阵的右上角位置（其中 `row` 是 `0` ，而 `column` 是 `1` ），并在左下角位置设置 `3.2` （其中 `row` 是 `1` ，而 `column` 是 `0` ）：

![subscriptMatrix02](https://docs.swift.org/swift-book/images/org.swift.tspl/subscriptMatrix02@2x.png){.light-only}
![subscriptMatrix02~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/subscriptMatrix02~dark@2x.png){.dark-only}

`Matrix` 下标的获取器和设置器都包含一个断言，以检查下标的 `row` 和 `column` 值是否有效。为了辅助这些断言， `Matrix` 包含一个名为 `indexIsValid(row:column:)` 的便利方法，该方法检查请求的 `row` 和 `column` 是否在矩阵的边界内：

```swift
func indexIsValid(row: Int, column: Int) -> Bool {
    return row >= 0 && row < rows && column >= 0 && column < columns
}
```

如果您尝试访问超出矩阵边界的下标，将会触发断言：

```swift
let someValue = matrix[2, 2]
// This triggers an assert, because [2, 2] is outside of the matrix bounds.
```

## [类型下标](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/subscripts#Type-Subscripts)

实例下标，如上所述，是在特定类型的实例上调用的下标。您还可以定义在类型本身上调用的下标。这种下标称为类型下标。通过在 `static` 关键字之前编写 `subscript` 关键字来指示类型下标。类可以使用 `class` 关键字来允许子类覆盖超类对该下标的实现。下面的示例演示了如何定义和调用类型下标：

```swift
enum Planet: Int {
    case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune
    static subscript(n: Int) -> Planet {
        return Planet(rawValue: n)!
    }
}
let mars = Planet[4]
print(mars)
```
