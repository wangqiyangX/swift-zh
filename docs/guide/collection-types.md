# 集合类型

> 使用数组、集合和字典组织数据。

Swift 提供三种主要的集合类型，称为数组、集合和字典，用于存储值的集合。数组是有序的值集合。集合是无序的唯一值集合。字典是无序的键值关联集合。

![CollectionTypes_intro~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/CollectionTypes_intro~dark@2x.png){.light-only}
![CollectionTypes_intro~dark~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/CollectionTypes_intro~dark~dark@2x.png){.dark-only}

Swift 中的数组、集合和字典始终明确它们可以存储的值和键的类型。这意味着您不能错误地将错误类型的值插入集合中。这也意味着您可以对从集合中检索到的值的类型充满信心。

> 注意
>
> Swift 的数组、集合和字典类型被实现为泛型集合。有关泛型类型和集合的更多信息，请参见 Generics。

## [集合的可变性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Mutability-of-Collections)

如果您创建一个数组、集合或字典，并将其赋值给一个变量，则创建的集合将是可变的。这意味着您可以在创建后通过添加、删除或更改集合中的项目来更改（或变更）集合。如果您将数组、集合或字典赋值给一个常量，则该集合是不可变的，其大小和内容无法更改。

> 注意
>
> 在所有不需要更改集合的情况下，创建不可变集合是一种良好的实践。这样可以让您更容易推理您的代码，并使 Swift 编译器能够优化您创建的集合的性能。

## [数组](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Arrays)

数组以有序列表存储相同类型的值。相同的值可以在数组中的不同位置出现多次。

> 注意
>
> Swift 的 `Array` 类型被桥接到 Foundation 的 `NSArray` 类。
>
> 有关使用 `Array` 与 Foundation 和 Cocoa 的更多信息，请参见在 Array 和 NSArray 之间的桥接。

### [数组类型简写语法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Array-Type-Shorthand-Syntax)

Swift 数组的类型完全写为 `Array<Element>` ，其中 `Element` 是数组允许存储的值的类型。您也可以将数组的类型以简写形式写为 `[Element]` 。虽然这两种形式在功能上是相同的，但是简写形式更受欢迎，并在本指南中用于指代数组的类型。

### [创建一个空数组](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Creating-an-Empty-Array)

您可以使用初始化语法创建某种类型的空数组：

```swift
var someInts: [Int] = []
print("someInts is of type [Int] with \(someInts.count) items.")
// Prints "someInts is of type [Int] with 0 items."
```

注意， `someInts` 变量的类型是从初始化器的类型推断为 `[Int]` 。

或者，如果上下文已经提供了类型信息，例如函数参数或已经有类型的变量或常量，您可以使用空数组字面量创建一个空数组，写作 `[]` （一对空方括号）：

```swift
someInts.append(3)
// someInts now contains 1 value of type Int
someInts = []
// someInts is now an empty array, but is still of type [Int]
```

### [创建具有默认值的数组](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Creating-an-Array-with-a-Default-Value)

Swift 的 `Array` 类型还提供了一个初始化器，用于创建大小为特定尺寸的数组，所有值均设置为相同的默认值。您需要向此初始化器传递适当类型的默认值（称为 `repeating` ）以及该值在新数组中重复的次数（称为 `count` ）：

```swift
var threeDoubles = Array(repeating: 0.0, count: 3)
// threeDoubles is of type [Double], and equals [0.0, 0.0, 0.0]
```

### [通过将两个数组相加来创建数组](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Creating-an-Array-by-Adding-Two-Arrays-Together)

您可以通过使用加法运算符 ( `+` ) 将两个具有兼容类型的现有数组相加来创建一个新数组。新数组的类型是从您相加的两个数组的类型推断得出的：

```swift
var anotherThreeDoubles = Array(repeating: 2.5, count: 3)
// anotherThreeDoubles is of type [Double], and equals [2.5, 2.5, 2.5]


var sixDoubles = threeDoubles + anotherThreeDoubles
// sixDoubles is inferred as [Double], and equals [0.0, 0.0, 0.0, 2.5, 2.5, 2.5]
```

### [使用数组字面量创建数组](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Creating-an-Array-with-an-Array-Literal)

您还可以使用数组字面量初始化数组，这是一种将一个或多个值作为数组集合的简写方式。数组字面量写作一个值的列表，值之间用逗号分隔，并用一对方括号包围：

```swift
[<#value 1#>, <#value 2#>, <#value 3#>]
```

下面的示例创建了一个名为 `shoppingList` 的数组，用于存储 `String` 值：

```swift
var shoppingList: [String] = ["Eggs", "Milk"]
// shoppingList has been initialized with two initial items
```

`shoppingList` 变量被声明为“字符串值的数组”，写作 `[String]` 。因为这个特定的数组指定了值类型为 `String` ，所以它只允许存储 `String` 值。在这里， `shoppingList` 数组使用两个 `String` 值 ( `"Eggs"` 和 `"Milk"` ) 初始化，写作数组字面量。

> 注意
>
> `shoppingList` 数组被声明为变量（使用 `var` 引入器），而不是常量（使用 `let` 引入器），因为在下面的示例中，有更多的项目被添加到购物清单中。

在这种情况下，数组字面量包含两个 `String` 值和其他内容。这与 `shoppingList` 变量的声明类型（只能包含 `String` 值的数组）相匹配，因此允许将数组字面量赋值作为用两个初始项初始化 `shoppingList` 的方式。

由于 Swift 的类型推断，如果您使用包含相同类型值的数组字面量初始化数组，则不必写出数组的类型。 `shoppingList` 的初始化可以用更简短的形式写成：

```swift
var shoppingList = ["Eggs", "Milk"]
```

因为数组字面量中的所有值都是相同类型，Swift 可以推断出 `[String]` 是用于 `shoppingList` 变量的正确类型。

### [访问和修改数组](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Accessing-and-Modifying-an-Array)

您可以通过其方法和属性访问和修改数组，或使用下标语法。

要查找数组中的项目数量，请检查其只读 `count` 属性：

```swift
print("The shopping list contains \(shoppingList.count) items.")
// Prints "The shopping list contains 2 items."
```

使用布尔 `isEmpty` 属性作为检查 `count` 属性是否等于 `0` 的快捷方式：

```swift
if shoppingList.isEmpty {
    print("The shopping list is empty.")
} else {
    print("The shopping list isn't empty.")
}
// Prints "The shopping list isn't empty."
```

您可以通过调用数组的 `append(_:)` 方法将新项添加到数组的末尾：

```swift
shoppingList.append("Flour")
// shoppingList now contains 3 items, and someone is making pancakes
```

另外，使用加法赋值运算符 ( `+=` ) 附加一个或多个兼容项的数组：

```swift
shoppingList += ["Baking Powder"]
// shoppingList now contains 4 items
shoppingList += ["Chocolate Spread", "Cheese", "Butter"]
// shoppingList now contains 7 items
```

通过使用下标语法从数组中检索值，在数组名称后面紧接着用方括号传递要检索的值的索引：

```swift
var firstItem = shoppingList[0]
// firstItem is equal to "Eggs"
```

> 注意
>
> 数组中的第一个元素的索引是 `0` ，而不是 `1` 。Swift 中的数组始终是从零开始索引的。

您可以使用下标语法在给定索引处更改现有值：

```swift
shoppingList[0] = "Six eggs"
// the first item in the list is now equal to "Six eggs" rather than "Eggs"
```

当您使用下标语法时，指定的索引需要是有效的。例如，写 `shoppingList[shoppingList.count] = "Salt"` 尝试将一个项目附加到数组的末尾会导致运行时错误。

您还可以使用下标语法一次更改一系列值，即使替换值集的长度与您要替换的范围不同。以下示例将 `"Chocolate Spread"` 、 `"Cheese"` 和 `"Butter"` 替换为 `"Bananas"` 和 `"Apples"` ：

```swift
shoppingList[4...6] = ["Bananas", "Apples"]
// shoppingList now contains 6 items
```

要在指定索引处向数组插入项，请调用数组的 `insert(_:at:)` 方法：

```swift
shoppingList.insert("Maple Syrup", at: 0)
// shoppingList now contains 7 items
// "Maple Syrup" is now the first item in the list
```

对 `insert(_:at:)` 方法的这个调用将在购物清单的最开始插入一个值为 `"Maple Syrup"` 的新项目，索引为 `0` 。

同样，您可以使用 `remove(at:)` 方法从数组中删除一个项目。该方法会删除指定索引的项目并返回被删除的项目（尽管如果不需要返回值可以忽略它）：

```swift
let mapleSyrup = shoppingList.remove(at: 0)
// the item that was at index 0 has just been removed
// shoppingList now contains 6 items, and no Maple Syrup
// the mapleSyrup constant is now equal to the removed "Maple Syrup" string
```

> 注意
>
> 如果您尝试访问或修改超出数组现有边界的索引的值，将会触发运行时错误。您可以通过将索引与数组的 `count` 属性进行比较，来检查索引是否有效。数组中最大的有效索引是 `count - 1` ，因为数组是从零开始索引的——然而，当 `count` 为 `0` （意味着数组为空）时，没有有效的索引。

当一个项目被移除时，数组中的任何空隙都会被填补，因此索引 `0` 处的值又一次等于 `"Six eggs"` ：

```swift
firstItem = shoppingList[0]
// firstItem is now equal to "Six eggs"
```

如果您想从数组中删除最后一个项目，请使用 `removeLast()` 方法，而不是 `remove(at:)` 方法，以避免查询数组的 `count` 属性。与 `remove(at:)` 方法一样， `removeLast()` 返回被删除的项目：

```swift
let apples = shoppingList.removeLast()
// the last item in the array has just been removed
// shoppingList now contains 5 items, and no apples
// the apples constant is now equal to the removed "Apples" string
```

### [遍历数组](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Iterating-Over-an-Array)

您可以使用 `for` - `in` 循环遍历数组中的所有值：

```swift
for item in shoppingList {
    print(item)
}
// Six eggs
// Milk
// Flour
// Baking Powder
// Bananas
```

如果您需要每个项目的整数索引以及它的值，请改用 `enumerated()` 方法来遍历数组。对于数组中的每个项目， `enumerated()` 方法返回一个由整数和项目组成的元组。整数从零开始，并为每个项目递增；如果您遍历整个数组，这些整数与项目的索引相匹配。您可以在迭代过程中将元组分解为临时常量或变量：

```swift
for (index, value) in shoppingList.enumerated() {
    print("Item \(index + 1): \(value)")
}
// Item 1: Six eggs
// Item 2: Milk
// Item 3: Flour
// Item 4: Baking Powder
// Item 5: Bananas
```

要了解更多关于 `for` - `in` 循环的信息，请参见 For-In 循环。

## [设置](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Sets)

集合在没有定义顺序的情况下存储相同类型的不同值。您可以在项目顺序不重要或需要确保项目仅出现一次时使用集合而不是数组。

> 注意
>
> Swift 的 `Set` 类型被桥接到 Foundation 的 `NSSet` 类。
>
> 有关在 Foundation 和 Cocoa 中使用 `Set` 的更多信息，请参见《在 Set 和 NSSet 之间桥接》。

### [集合类型的哈希值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Hash-Values-for-Set-Types)

类型必须是可哈希的，以便存储在集合中——也就是说，类型必须提供一种方法来计算自身的哈希值。哈希值是一种 `Int` 值，对于所有相等比较的对象都是相同的，因此，如果 `a == b` ，则 `a` 的哈希值等于 `b` 的哈希值。

Swift 的所有基本类型（例如 `String` ， `Int` ， `Double` 和 `Bool` ）默认都是可哈希的，可以用作集合值类型或字典键类型。没有关联值的枚举案例值（如枚举中所描述）默认也是可哈希的。

> 注意
>
> 您可以通过使您自己的自定义类型符合 Swift 标准库中的 `Hashable` 协议，将其用作集合值类型或字典键类型。有关实现所需 `hash(into:)` 方法的信息，请参见 `Hashable` 。有关遵循协议的信息，请参见 协议。

### [设置类型语法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Set-Type-Syntax)

Swift 集合的类型写作 `Set<Element>` ，其中 `Element` 是集合允许存储的类型。与数组不同，集合没有等效的简写形式。

### [创建和初始化一个空集合](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Creating-and-Initializing-an-Empty-Set)

您可以使用初始化语法创建某种类型的空集合：

```swift
var letters = Set<Character>()
print("letters is of type Set<Character> with \(letters.count) items.")
// Prints "letters is of type Set<Character> with 0 items."
```

> 注意
>
> `letters` 变量的类型被推断为 `Set<Character>` ，来自初始化器的类型。

另外，如果上下文已经提供了类型信息，例如函数参数或已经类型化的变量或常量，您可以使用空数组字面量创建一个空集合：

```swift
letters.insert("a")
// letters now contains 1 value of type Character
letters = []
// letters is now an empty set, but is still of type Set<Character>
```

### [使用数组字面量创建集合](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Creating-a-Set-with-an-Array-Literal)

您还可以使用数组文字初始化集合，作为以集合形式编写一个或多个值的简写方式。

下面的示例创建了一个名为 `favoriteGenres` 的集合来存储 `String` 值：

```swift
var favoriteGenres: Set<String> = ["Rock", "Classical", "Hip hop"]
// favoriteGenres has been initialized with three initial items
```

`favoriteGenres` 变量被声明为“ `String` 值的集合”，写作 `Set<String>` 。因为这个特定的集合指定了值类型为 `String` ，所以它只允许存储 `String` 值。在这里， `favoriteGenres` 集合使用三个 `String` 值（ `"Rock"` 、 `"Classical"` 和 `"Hip hop"` ）通过数组文字初始化。

> 注意
>
> `favoriteGenres` 集合被声明为变量（使用 `var` 引入符）而不是常量（使用 `let` 引入符），因为在下面的示例中会添加和移除项。

集合类型不能仅从数组字面量推断，因此必须显式声明类型 `Set` 。然而，由于 Swift 的类型推断，如果您使用仅包含一种类型值的数组字面量来初始化集合，则不必编写集合元素的类型。 `favoriteGenres` 的初始化可以用更简短的形式来编写：

```swift
var favoriteGenres: Set = ["Rock", "Classical", "Hip hop"]
```

因为数组字面量中的所有值都是相同类型，Swift 可以推断出 `Set<String>` 是用于 `favoriteGenres` 变量的正确类型。

### [访问和修改集合](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Accessing-and-Modifying-a-Set)

您可以通过其方法和属性访问和修改集合。

要找出集合中项目的数量，请检查其只读 `count` 属性：

```swift
print("I have \(favoriteGenres.count) favorite music genres.")
// Prints "I have 3 favorite music genres."
```

使用布尔 `isEmpty` 属性作为检查 `count` 属性是否等于 `0` 的快捷方式：

```swift
if favoriteGenres.isEmpty {
    print("As far as music goes, I'm not picky.")
} else {
    print("I have particular music preferences.")
}
// Prints "I have particular music preferences."
```

您可以通过调用集合的 `insert(_:)` 方法将新项目添加到集合中：

```swift
favoriteGenres.insert("Jazz")
// favoriteGenres now contains 4 items
```

您可以通过调用集合的 `remove(_:)` 方法从集合中移除一个项目，如果该项目是集合的成员，则会移除该项目并返回被移除的值，或者如果集合不包含它，则返回 `nil` 。另外，可以使用其 `removeAll()` 方法移除集合中的所有项目。

```swift
if let removedGenre = favoriteGenres.remove("Rock") {
    print("\(removedGenre)? I'm over it.")
} else {
    print("I never much cared for that.")
}
// Prints "Rock? I'm over it."
```

要检查一个集合是否包含特定项，请使用 `contains(_:)` 方法。

```swift
if favoriteGenres.contains("Funk") {
    print("I get up on the good foot.")
} else {
    print("It's too funky in here.")
}
// Prints "It's too funky in here."
```

### [遍历集合](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Iterating-Over-a-Set)

您可以通过 `for` - `in` 循环遍历集合中的值。

```swift
for genre in favoriteGenres {
    print("\(genre)")
}
// Classical
// Jazz
// Hip hop
```

要了解更多关于 `for` - `in` 循环的信息，请参见 For-In 循环。

Swift 的 `Set` 类型没有定义的顺序。要以特定顺序遍历集合的值，请使用 `sorted()` 方法，该方法返回使用 `<` 运算符排序的集合元素数组。

```swift
for genre in favoriteGenres.sorted() {
    print("\(genre)")
}
// Classical
// Hip hop
// Jazz
```

## [执行集合操作](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Performing-Set-Operations)

您可以高效地执行基本的集合操作，例如将两个集合合并在一起，确定两个集合之间的共同值，或确定两个集合是否包含所有、部分或没有相同的值。

### [基本集合运算](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Fundamental-Set-Operations)

下面的插图描绘了两个集合 — `a` 和 `b` — 其结果通过阴影区域表示。

![setVennDiagram](https://docs.swift.org/swift-book/images/org.swift.tspl/setVennDiagram@2x.png){.light-only}
![setVennDiagram~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/setVennDiagram~dark@2x.png){.dark-only}

- 使用 `intersection(_:)` 方法创建一个新集合，仅包含两个集合中共同的值。
- 使用 `symmetricDifference(_:)` 方法创建一个新集合，包含任一集合中的值，但不包括两个集合中的值。
- 使用 `union(_:)` 方法创建一个新集合，包含两个集合中的所有值。
- 使用 `subtracting(_:)` 方法创建一个新集合，该集合的值不在指定集合中。

```swift
let oddDigits: Set = [1, 3, 5, 7, 9]
let evenDigits: Set = [0, 2, 4, 6, 8]
let singleDigitPrimeNumbers: Set = [2, 3, 5, 7]


oddDigits.union(evenDigits).sorted()
// [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
oddDigits.intersection(evenDigits).sorted()
// []
oddDigits.subtracting(singleDigitPrimeNumbers).sorted()
// [1, 9]
oddDigits.symmetricDifference(singleDigitPrimeNumbers).sorted()
// [1, 2, 9]
```

### [集合成员资格和相等性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Set-Membership-and-Equality)

下面的插图描绘了三个集合 — `a` , `b` 和 `c` — 其中重叠区域表示集合之间共享的元素。集合 `a` 是集合 `b` 的超集，因为 `a` 包含了 `b` 中的所有元素。相反，集合 `b` 是集合 `a` 的子集，因为 `b` 中的所有元素也被 `a` 包含。集合 `b` 和集合 `c` 彼此不相交，因为它们没有共同的元素。

![setEulerDiagram](https://docs.swift.org/swift-book/images/org.swift.tspl/setEulerDiagram@2x.png){.light-only}
![setEulerDiagram~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/setEulerDiagram~dark@2x.png){.dark-only}

- 使用“相等”运算符 ( `==` ) 来确定两个集合是否包含所有相同的值。
- 使用 `isSubset(of:)` 方法来确定一个集合的所有值是否都包含在指定的集合中。
- 使用 `isSuperset(of:)` 方法来确定一个集合是否包含指定集合中的所有值。
- 使用 `isStrictSubset(of:)` 或 `isStrictSuperset(of:)` 方法来确定一个集合是否是指定集合的子集或超集，但不等于该集合。
- 使用 `isDisjoint(with:)` 方法来确定两个集合是否没有共同的值。

```swift
let houseAnimals: Set = ["🐶", "🐱"]
let farmAnimals: Set = ["🐮", "🐔", "🐑", "🐶", "🐱"]
let cityAnimals: Set = ["🐦", "🐭"]


houseAnimals.isSubset(of: farmAnimals)
// true
farmAnimals.isSuperset(of: houseAnimals)
// true
farmAnimals.isDisjoint(with: cityAnimals)
// true
```

## [字典](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Dictionaries)

字典存储同一类型的键与同一类型的值之间的关联，集合中没有定义的顺序。每个值与一个唯一的键关联，该键作为字典中该值的标识符。与数组中的项不同，字典中的项没有指定的顺序。当您需要根据标识符查找值时，可以使用字典，这与现实世界字典用于查找特定单词的定义的方式非常相似。

> 注意
>
> Swift 的 `Dictionary` 类型被桥接到 Foundation 的 `NSDictionary` 类。
>
> 有关使用 `Dictionary` 与 Foundation 和 Cocoa 的更多信息，请参见在字典和 NSDictionary 之间的桥接。

### [字典类型简写语法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Dictionary-Type-Shorthand-Syntax)

Swift 字典的类型完整写作 `Dictionary<Key, Value>` ，其中 `Key` 是可以作为字典键的值的类型，而 `Value` 是字典为这些键存储的值的类型。

> 注意
>
> 字典 `Key` 类型必须符合 `Hashable` 协议，像集合的值类型一样。

您还可以以简写形式将字典的类型写为 `[Key: Value]` 。虽然这两种形式在功能上是相同的，但简写形式更受欢迎，并且在本指南中提到字典类型时将使用简写形式。

### [创建一个空字典](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Creating-an-Empty-Dictionary)

与数组一样，您可以使用初始化语法创建某种类型的空 `Dictionary` ：

```swift
var namesOfIntegers: [Int: String] = [:]
// namesOfIntegers is an empty [Int: String] dictionary
```

此示例创建一个空字典，类型为 `[Int: String]` ，用于存储整数值的人类可读名称。它的键类型为 `Int` ，值类型为 `String` 。

如果上下文已经提供类型信息，您可以使用空字典字面量创建一个空字典，写作 `[:]` （在一对方括号内用一个冒号表示）：

```swift
namesOfIntegers[16] = "sixteen"
// namesOfIntegers now contains 1 key-value pair
namesOfIntegers = [:]
// namesOfIntegers is once again an empty dictionary of type [Int: String]
```

### [使用字典字面量创建字典](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Creating-a-Dictionary-with-a-Dictionary-Literal)

您也可以使用字典字面量初始化字典，其语法与前面看到的数组字面量类似。字典字面量是一种简写形式，可以将一个或多个键值对作为一个 `Dictionary` 集合书写。

键值对是键和值的组合。在字典字面量中，每个键值对中的键和值通过冒号分隔。键值对作为列表书写，以逗号分隔，并用一对方括号包围：

```swift
[<#key 1#>: <#value 1#>, <#key 2#>: <#value 2#>, <#key 3#>: <#value 3#>]
```

下面的示例创建一个字典来存储国际机场的名称。在此字典中，键是三字母国际航空运输协会代码，值是机场名称：

```swift
var airports: [String: String] = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]
```

该 `airports` 字典声明为类型 `[String: String]` ，这意味着“一个 `Dictionary` ，其键的类型为 `String` ，值的类型也为 `String` ”。

> 注意
>
> `airports` 字典被声明为变量（使用 `var` 引入），而不是常量（使用 `let` 引入），因为在下面的示例中会向字典中添加更多机场。

该 `airports` 字典使用包含两个键值对的字典字面量进行初始化。第一个键值对的键为 `"YYZ"` ，值为 `"Toronto Pearson"` 。第二个键值对的键为 `"DUB"` ，值为 `"Dublin"` 。

这个字典字面量包含两个 `String: String` 对。这种键值类型与 `airports` 变量声明的类型相匹配（一个只有 `String` 键和只有 `String` 值的字典），因此字典字面量的赋值被允许作为用两个初始项初始化 `airports` 字典的一种方式。

与数组一样，如果您使用键和值具有一致类型的字典字面量进行初始化，则不必写出字典的类型。 `airports` 的初始化可以用一种更简短的形式编写：

```swift
var airports = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]
```

因为字面量中的所有键都是相同类型的，所有值也是相同类型的，Swift 可以推断出 `[String: String]` 是 `airports` 字典中使用的正确类型。

### [访问和修改字典](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Accessing-and-Modifying-a-Dictionary)

您可以通过其方法和属性访问和修改字典，或使用下标语法。

与数组一样，您可以通过检查其只读 `count` 属性来找出 `Dictionary` 中的项目数量：

```swift
print("The airports dictionary contains \(airports.count) items.")
// Prints "The airports dictionary contains 2 items."
```

使用布尔 `isEmpty` 属性作为检查 `count` 属性是否等于 `0` 的快捷方式：

```swift
if airports.isEmpty {
    print("The airports dictionary is empty.")
} else {
    print("The airports dictionary isn't empty.")
}
// Prints "The airports dictionary isn't empty."
```

您可以使用下标语法向字典添加新项。使用适当类型的新键作为下标索引，并分配适当类型的新值：

```swift
airports["LHR"] = "London"
// the airports dictionary now contains 3 items
```

您还可以使用下标语法来更改与特定键关联的值：

```swift
airports["LHR"] = "London Heathrow"
// the value for "LHR" has been changed to "London Heathrow"
```

作为下标的替代方案，使用字典的 `updateValue(_:forKey:)` 方法来设置或更新特定键的值。与上面的下标示例一样， `updateValue(_:forKey:)` 方法在没有值的情况下为键设置一个值，或者在该键已经存在的情况下更新值。然而，与下标不同， `updateValue(_:forKey:)` 方法在执行更新后返回旧值。这使您能够检查是否进行了更新。

`updateValue(_:forKey:)` 方法返回字典值类型的可选值。例如，对于存储 `String` 值的字典，该方法返回类型为 `String?` 的值，或“可选 `String` ”。如果在更新之前该键存在旧值，则此可选值包含该旧值；如果没有值，则为 `nil` ：

```swift
if let oldValue = airports.updateValue("Dublin Airport", forKey: "DUB") {
    print("The old value for DUB was \(oldValue).")
}
// Prints "The old value for DUB was Dublin."
```

您还可以使用下标语法从字典中检索特定键的值。因为可以请求一个没有值的键，所以字典的下标返回字典值类型的可选值。如果字典包含请求键的值，则下标返回一个包含该键现有值的可选值。否则，下标返回 `nil` :

```swift
if let airportName = airports["DUB"] {
    print("The name of the airport is \(airportName).")
} else {
    print("That airport isn't in the airports dictionary.")
}
// Prints "The name of the airport is Dublin Airport."
```

您可以使用下标语法通过为该键赋值 `nil` 从字典中删除一个键值对：

```swift
airports["APL"] = "Apple International"
// "Apple International" isn't the real airport for APL, so delete it
airports["APL"] = nil
// APL has now been removed from the dictionary
```

另外，使用 `removeValue(forKey:)` 方法从字典中移除一个键值对。如果该键值对存在，则该方法会移除它并返回被移除的值；如果不存在值，则返回 `nil` 。

```swift
if let removedValue = airports.removeValue(forKey: "DUB") {
    print("The removed airport's name is \(removedValue).")
} else {
    print("The airports dictionary doesn't contain a value for DUB.")
}
// Prints "The removed airport's name is Dublin Airport."
```

### [遍历字典](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Iterating-Over-a-Dictionary)

您可以使用 `for` - `in` 循环遍历字典中的键值对。字典中的每个项作为 `(key, value)` 元组返回，您可以在迭代过程中将元组的成员解构为临时常量或变量：

```swift
for (airportCode, airportName) in airports {
    print("\(airportCode): \(airportName)")
}
// LHR: London Heathrow
// YYZ: Toronto Pearson
```

要了解更多关于 `for` - `in` 循环的信息，请参见 For-In 循环。

您还可以通过访问字典的 `keys` 和 `values` 属性来检索字典键或值的可迭代集合：

```swift
for airportCode in airports.keys {
    print("Airport code: \(airportCode)")
}
// Airport code: LHR
// Airport code: YYZ


for airportName in airports.values {
    print("Airport name: \(airportName)")
}
// Airport name: London Heathrow
// Airport name: Toronto Pearson
```

如果您需要使用字典的键或值与一个接受 `Array` 实例的 API，使用 `keys` 或 `values` 属性初始化一个新数组：

```swift
let airportCodes = [String](airports.keys)
// airportCodes is ["LHR", "YYZ"]


let airportNames = [String](airports.values)
// airportNames is ["London Heathrow", "Toronto Pearson"]
```

Swift 的 `Dictionary` 类型没有定义的顺序。要以特定顺序迭代字典的键或值，请在其 `keys` 或 `values` 属性上使用 `sorted()` 方法。
