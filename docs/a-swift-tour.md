# Swift 入门之旅

> 探索 Swift 的特性和语法。

传统上，用一种新语言写的第一个程序应该在屏幕上打印出“Hello, world!”这句话。在 Swift 中，您只需一行代码即可完成：

```swift
print("Hello, world!")
// Prints "Hello, world!"
```

如果您了解其他编程语言，这种语法看起来应该很熟悉——在 Swift 中，这一行代码就是一个完整的程序。您无需为了输出文本或处理字符串而单独导入某个库。在全局作用域下编写的代码会作为程序的入口点，因此您不需要 `main()` 函数。您也不用在每个语句末尾写分号。
本教程将通过展示如何完成各种编程任务，帮助您获得开始用 Swift 编写代码所需的基础知识。如果有哪里不明白也不必担心——本书余下部分会详细解释本教程中介绍的所有内容。

## 简单值

使用 `let` 来创建常量，使用 `var` 来创建变量。常量的值不需要在编译时就已知，但您必须只为其赋值一次。这意味着您可以用常量为某个只确定一次但会在多个地方使用的值命名。

```swift
var myVariable = 42
myVariable = 50
let myConstant = 42
```

常量或变量的类型必须与要赋给它的值的类型相同。不过，您并不总是需要显式地写出类型。在创建常量或变量时提供一个值，可以让编译器推断其类型。在上面的例子中，编译器推断 myVariable 是一个整数，因为它的初始值是整数。
如果初始值无法提供足够的信息（或者没有初始值），可以在变量后面加上冒号并写出类型来指定类型。

```swift
let implicitInteger = 70
let implicitDouble = 70.0
let explicitDouble: Double = 70
```

> 试一试
> 创建一个显式类型为 `Float` 且值为 4 的常量。

数值永远不会被隐式转换为其他类型。如果您需要将一个值转换为不同的类型，请显式地创建所需类型的实例。

```swift
let label = "The width is "
let width = 94
let widthLabel = label + String(width)
```

> 实验
> 尝试移除最后一行中的 String 类型转换。您会遇到什么错误？

还有一种更简单的方法可以在字符串中包含数值：将数值写在括号中，并在括号前加上反斜杠（`\`）。例如：

```swift
let apples = 3
let oranges = 5
let appleSummary = "I have \(apples) apples."
let fruitSummary = "I have \(apples + oranges) pieces of fruit."
```

> 试一试
> 使用 `\()` 将浮点数计算结果包含在字符串中，并在问候语中包含某人的名字。

对于占据多行的字符串，使用三个双引号（`"""`）。每一行开头的缩进会被移除，只要它与结束引号的缩进一致。例如：

```swift
let quotation = """
Even though there's whitespace to the left,
the actual lines aren't indented.
Except for this line.
Double quotes (") can appear without being escaped.

        I still have \(apples + oranges) pieces of fruit.
        """

```

使用方括号（`[]`）来创建数组和字典，并通过在方括号中写入索引或键来访问它们的元素。在最后一个元素后面允许加逗号。

```swift
var fruits = ["strawberries", "limes", "tangerines"]
fruits[1] = "grapes"


var occupations = [
    "Malcolm": "Captain",
    "Kaylee": "Mechanic",
 ]
occupations["Jayne"] = "Public Relations"
```

随着您添加元素，数组会自动增长。

```swift
fruits.append("blueberries")
print(fruits)
// Prints "["strawberries", "grapes", "tangerines", "blueberries"]"
```

您还可以使用方括号来表示一个空数组或空字典。对于数组，写作 `[]`；对于字典，写作 `[:]`。

```swift
fruits = []
occupations = [:]
```

如果您要将一个空数组或字典赋值给一个新变量，或者赋值给其他没有类型信息的地方，您需要指定类型。

```swift
let emptyArray: [String] = []
let emptyDictionary: [String: Float] = [:]
```

## 控制流

使用 `if` 和 `switch` 来进行条件判断，使用 `for-in` 、 `while` 和 `repeat-while` 来进行循环。条件或循环变量周围的括号是可选的。代码体周围的大括号是必需的。

```swift
let individualScores = [75, 43, 103, 87, 12]
var teamScore = 0
for score in individualScores {
    if score > 50 {
        teamScore += 3
    } else {
        teamScore += 1
    }
}
print(teamScore)
// Prints "11"
```

在 `if` 语句中，条件必须是布尔表达式——这意味着像 `if score { ... }` 这样的代码是错误的，并不会被隐式地与零进行比较。
您可以在赋值的等号（ `=` ）后面或在 `return` 后面写 `if` 或 `switch` ，以根据条件选择一个值。

```swift
let scoreDecoration = if teamScore > 10 {
    "🎉"
} else {
    ""
}
print("Score:", teamScore, scoreDecoration)
// Prints "Score: 11 🎉"
```

您可以同时使用 `if` 和 `let` 来处理可能缺失的值。这些值被表示为可选项。可选值要么包含一个值，要么包含 `nil` ，以表示值缺失。在值的类型后面写一个问号（`?`），以标记该值为可选。

```swift
var optionalString: String? = "Hello"
print(optionalString == nil)
// Prints "false"


var optionalName: String? = "John Appleseed"
var greeting = "Hello!"
if let name = optionalName {
    greeting = "Hello, \(name)"
}
```

> 实验
> 将 `optionalName` 改为 `nil` 。您会得到什么问候语？添加一个 `else` 子句，当 `optionalName` 为 `nil` 时设置不同的问候语。

如果可选值是 `nil` ，条件为 `false` ，大括号中的代码将被跳过。否则，可选值会被解包并赋值给 `let` 后的常量，这样在代码块内部就可以使用解包后的值。
处理可选值的另一种方法是使用 `??` 运算符提供一个默认值。如果可选值不存在，则会使用默认值。

```swift
let nickname: String? = nil
let fullName: String = "John Appleseed"
let informalGreeting = "Hi \(nickname ?? fullName)"
```

您可以使用更简短的写法来解包一个值，并且对解包后的值使用相同的名称。

```swift
if let nickname {
    print("Hey, \(nickname)")
}
// Doesn't print anything, because nickname is nil.
```

Switch 语句支持任何类型的数据和多种比较操作——它们不仅限于整数和等值判断。

```swift
let vegetable = "red pepper"
switch vegetable {
case "celery":
    print("Add some raisins and make ants on a log.")
case "cucumber", "watercress":
    print("That would make a good tea sandwich.")
case let x where x.hasSuffix("pepper"):
    print("Is it a spicy \(x)?")
default:
    print("Everything tastes good in soup.")
}
// Prints "Is it a spicy red pepper?"
```

> 实验
> 尝试移除 default 分支。您会遇到什么错误？

请注意， `let` 可以在模式中使用，将与模式匹配的值赋给一个常量。
在执行完匹配的 `switch` 分支中的代码后，程序会退出 `switch` 语句。执行不会继续到下一个分支，因此您不需要在每个分支代码的结尾显式地使用 `break` 语句。
您可以使用 `for-in` 来遍历字典中的条目，为每个键值对提供一对名称。字典是一种无序集合，因此它们的键和值会以任意顺序进行遍历。

```swift
let interestingNumbers = [
    "Prime": [2, 3, 5, 7, 11, 13],
    "Fibonacci": [1, 1, 2, 3, 5, 8],
    "Square": [1, 4, 9, 16, 25],
]
var largest = 0
for (_, numbers) in interestingNumbers {
    for number in numbers {
        if number > largest {
            largest = number
        }
    }
}
print(largest)
// Prints "25"
```

> 实验
> 用一个变量名替换 `_` ，并记录哪种类型的数字最大。

使用 `while` 来重复执行一段代码，直到条件发生变化。循环的条件也可以放在末尾，这样可以确保循环至少执行一次。

```swift
var n = 2
while n < 100 {
    n *= 2
}
print(n)
// Prints "128"


var m = 2
repeat {
    m *= 2
} while m < 100
print(m)
// Prints "128"
```

> 实验
> 将条件从 m < 100 改为 m < 0 ，观察当循环条件已经为假时， while 和 repeat-while 的行为有何不同。

您可以使用 `..<` 创建一个索引范围，从而在循环中保留一个索引。

```swift
var total = 0
for i in 0..<4 {
    total += i
}
print(total)
// Prints "6"
```

使用 `..<` 来创建一个不包含上限值的区间，使用 `...` 来创建一个包含两个值的区间。

## 函数与闭包

使用 `func` 来声明一个函数。通过在函数名后面加上括号中的参数列表来调用函数。使用 `->` 将参数名和类型与函数的返回类型分隔开。

```swift
func greet(person: String, day: String) -> String {
    return "Hello \(person), today is \(day)."
}
greet(person: "Bob", day: "Tuesday")
```

> 实验
> 移除 `day` 参数。添加一个参数，在问候语中包含今天的午餐特价。

默认情况下，函数使用其参数名作为参数标签。可以在参数名前自定义参数标签，或者写 `_` 表示不使用参数标签。

```swift
func greet(_ person: String, on day: String) -> String {
    return "Hello \(person), today is \(day)."
}
greet("John", on: "Wednesday")
```

使用元组可以创建复合值——例如，从函数中返回多个值。元组的元素可以通过名称或数字来引用。

```swift
func calculateStatistics(scores: [Int]) -> (min: Int, max: Int, sum: Int) {
    var min = scores[0]
    var max = scores[0]
    var sum = 0


    for score in scores {
        if score > max {
            max = score
        } else if score < min {
            min = score
        }
        sum += score
    }


    return (min, max, sum)
}
let statistics = calculateStatistics(scores: [5, 3, 100, 3, 9])
print(statistics.sum)
// Prints "120"
print(statistics.2)
// Prints "120"
```

函数可以嵌套。嵌套函数可以访问外部函数中声明的变量。您可以使用嵌套函数来组织较长或复杂函数中的代码。

```swift
func returnFifteen() -> Int {
    var y = 10
    func add() {
        y += 5
    }
    add()
    return y
}
returnFifteen()
```

函数是一等类型。这意味着函数可以作为值返回另一个函数。

```swift
func makeIncrementer() -> ((Int) -> Int) {
    func addOne(number: Int) -> Int {
        return 1 + number
    }
    return addOne
}
var increment = makeIncrementer()
increment(7)
```

一个函数可以将另一个函数作为其参数之一。

```swift
func hasAnyMatches(list: [Int], condition: (Int) -> Bool) -> Bool {
    for item in list {
        if condition(item) {
            return true
        }
    }
    return false
}
func lessThanTen(number: Int) -> Bool {
    return number < 10
}
var numbers = [20, 19, 7, 12]
hasAnyMatches(list: numbers, condition: lessThanTen)
```

函数实际上是闭包的一种特殊情况：闭包是可以稍后调用的代码块。闭包中的代码可以访问在创建闭包时所在作用域中可用的变量和函数，即使闭包在执行时处于不同的作用域——您已经在嵌套函数的例子中见过这种情况。您可以通过用大括号（`{ }`）包裹代码来编写一个没有名称的闭包。使用 `in` 来将参数和返回类型与闭包体分隔开。

```swift
numbers.map({ (number: Int) -> Int in
    let result = 3 * number
    return result
})
```

> 实验
> 重写闭包，使其对所有奇数都返回零。

您有多种方法可以更简洁地编写闭包。当闭包的类型已经确定时，比如作为委托的回调，您可以省略参数的类型、返回类型，或者两者都省略。只有一条语句的闭包会隐式返回该语句的值。

```swift
let mappedNumbers = numbers.map({ number in 3 _ number })
print(mappedNumbers)
// Prints "[60, 57, 21, 36]"
```

您可以通过编号而不是名称来引用参数——这种方法在非常简短的闭包中尤其有用。作为函数最后一个参数传递的闭包可以直接出现在括号后面。当闭包是函数的唯一参数时，您甚至可以完全省略括号。

```swift
let sortedNumbers = numbers.sorted { $0 > $1 }
print(sortedNumbers)
// Prints "[20, 19, 12, 7]"
```

## 对象和类

使用 `class` 后跟类名来创建一个类。在类中的属性声明方式与常量或变量声明相同，只是它处于类的上下文中。同样，方法和函数的声明方式也是一样的。

```swift
class Shape {
    var numberOfSides = 0
    func simpleDescription() -> String {
        return "A shape with \(numberOfSides) sides."
    }
}
```

> 实验
> 使用 let 添加一个常量属性，并添加另一个带有参数的方法。

通过在类名后加上括号来创建类的实例。使用点语法访问该实例的属性和方法。

```swift
var shape = Shape()
shape.numberOfSides = 7
var shapeDescription = shape.simpleDescription()
```

这个 `Shape` 类的版本缺少了一项重要内容：用于在创建实例时设置类的初始化器。使用 `init` 来创建一个初始化器。

```swift
class NamedShape {
    var numberOfSides: Int = 0
    var name: String


    init(name: String) {
       self.name = name
    }


    func simpleDescription() -> String {
       return "A shape with \(numberOfSides) sides."
    }
}
```

请注意， `self` 用于区分初始化器中的 `name` 属性和 `name` 参数。在创建类的实例时，初始化器的参数像函数调用一样传递。每个属性都需要被赋值——要么在声明时赋值（如 `numberOfSides` ），要么在初始化器中赋值（如 `name` ）。

如果您需要在对象被释放前执行一些清理操作，可以使用 `deinit` 创建一个析构器。

子类在其类名后面包含其父类名，两者之间用冒号分隔。类没有必须继承任何标准根类的要求，因此您可以根据需要包含或省略父类。

子类中重写父类实现的方法需要用 `override` 标记——如果意外重写了某个方法但没有使用 `override` ，编译器会检测到并报错。编译器还会检测带有 `override` 标记但实际上并未重写父类中任何方法的方法。

```swift
class Square: NamedShape {
    var sideLength: Double


    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 4
    }


    func area() -> Double {
        return sideLength * sideLength
    }


    override func simpleDescription() -> String {
        return "A square with sides of length \(sideLength)."
    }
}
let test = Square(sideLength: 5.2, name: "my test square")
test.area()
test.simpleDescription()
```

> 实验
> 再创建一个名为 Circle 的 `NamedShape` 子类，在其初始化方法中接收半径和名称作为参数。在 Circle 类中实现一个 `area()` 方法和一个 `simpleDescription()` 方法。

除了简单的存储属性外，属性还可以拥有 `getter` 和 `setter。`

```swift
class EquilateralTriangle: NamedShape {
    var sideLength: Double = 0.0


    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 3
    }


    var perimeter: Double {
        get {
             return 3.0 * sideLength
        }
        set {
            sideLength = newValue / 3.0
        }
    }


    override func simpleDescription() -> String {
        return "An equilateral triangle with sides of length \(sideLength)."
    }
}
var triangle = EquilateralTriangle(sideLength: 3.1, name: "a triangle")
print(triangle.perimeter)
// Prints "9.3"
triangle.perimeter = 9.9
print(triangle.sideLength)
// Prints "3.3000000000000003"
```

在 `perimeter` 的 `setter` 中，新值有一个隐式名称 `newValue` 。您可以在 `set` 后面的括号中提供一个显式名称。
请注意， `EquilateralTriangle` 类的初始化方法有三个不同的步骤：

1. 设置子类声明的属性的值。
2. 调用父类的初始化方法。
3. 更改由父类定义的属性的值。任何使用方法、getter 或 setter 的额外设置工作也可以在此时完成。

如果您不需要计算属性，但仍然需要在设置新值之前和之后运行代码，可以使用 `willSet` 和 `didSet` 。您提供的代码会在初始化器之外每当值发生变化时运行。例如，下面的类确保其三角形的边长始终与其正方形的边长相同。

```swift
class TriangleAndSquare {
    var triangle: EquilateralTriangle {
        willSet {
            square.sideLength = newValue.sideLength
        }
    }
    var square: Square {
        willSet {
            triangle.sideLength = newValue.sideLength
        }
    }
    init(size: Double, name: String) {
        square = Square(sideLength: size, name: name)
        triangle = EquilateralTriangle(sideLength: size, name: name)
    }
}
var triangleAndSquare = TriangleAndSquare(size: 10, name: "another test shape")
print(triangleAndSquare.square.sideLength)
// Prints "10.0"
print(triangleAndSquare.triangle.sideLength)
// Prints "10.0"
triangleAndSquare.square = Square(sideLength: 50, name: "larger square")
print(triangleAndSquare.triangle.sideLength)
// Prints "50.0"
```

在处理可选值时，您可以在方法、属性和下标等操作前写上 `?` 。如果 `?` 前的值是 `nil` ，那么 `?` 之后的所有内容都会被忽略，整个表达式的值为 `nil` 。否则，可选值会被解包， `?` 之后的所有内容都作用于解包后的值。在这两种情况下，整个表达式的值都是一个可选值。

```swift
let optionalSquare: Square? = Square(sideLength: 2.5, name: "optional square")
let sideLength = optionalSquare?.sideLength
```

## 枚举和结构体

使用 `enum` 来创建一个枚举。与类和所有其他具名类型一样，枚举也可以关联方法。

```swift
enum Rank: Int {
    case ace = 1
    case two, three, four, five, six, seven, eight, nine, ten
    case jack, queen, king


    func simpleDescription() -> String {
        switch self {
        case .ace:
            return "ace"
        case .jack:
            return "jack"
        case .queen:
            return "queen"
        case .king:
            return "king"
        default:
            return String(self.rawValue)
        }
    }
}
let ace = Rank.ace
let aceRawValue = ace.rawValue
```

> 实验
> 编写一个函数，通过比较原始值来比较两个 Rank 的值。

默认情况下，Swift 会从零开始为原始值赋值，并每次递增一，但您可以通过显式指定值来更改此行为。在上面的示例中， Ace 被显式赋予了原始值 1 ，其余的原始值则按顺序分配。您也可以使用字符串或浮点数作为枚举的原始类型。使用 rawValue 属性可以访问枚举成员的原始值。
使用 `init?(rawValue:)` 初始化器可以通过原始值创建枚举的实例。它会返回与原始值匹配的枚举成员，或者如果没有匹配的 Rank ，则返回 `nil` 。

```swift
if let convertedRank = Rank(rawValue: 3) {
let threeDescription = convertedRank.simpleDescription()
}
```

枚举的 `case` 值是真正的值，而不仅仅是它们原始值的另一种写法。实际上，在没有有意义的原始值时，您可以不提供原始值。

```swift
enum Suit {
    case spades, hearts, diamonds, clubs


    func simpleDescription() -> String {
        switch self {
        case .spades:
            return "spades"
        case .hearts:
            return "hearts"
        case .diamonds:
            return "diamonds"
        case .clubs:
            return "clubs"
        }
    }
}
let hearts = Suit.hearts
let heartsDescription = hearts.simpleDescription()
```

> 实验
> 为 Suit 添加一个 color() 方法，对于黑桃和梅花返回“black”，对于红桃和方块返回“red”。

请注意上述两种引用枚举中 hearts 的方式： 在为红心常量赋值时，枚举例 Suit.hearts 使用的是全名，因为常量没有指定明确的类型。在开关内部，枚举用缩写形式 .hearts 来表示，因为 self 的值已经知道是一种花色。在任何已知值类型的情况下，都可以使用缩写形式。

如果枚举有原始值，这些值会在声明时确定，这意味着特定枚举成员的每个实例总是具有相同的原始值。另一种选择是为枚举成员关联值——这些值在创建实例时确定，并且对于枚举成员的每个实例都可以不同。您可以将关联值看作是枚举成员实例的存储属性。例如，考虑从服务器请求日出和日落时间的情况。服务器要么返回请求的信息，要么返回出错的描述。

```swift
enum ServerResponse {
    case result(String, String)
    case failure(String)
}


let success = ServerResponse.result("6:00 am", "8:09 pm")
let failure = ServerResponse.failure("Out of cheese.")


switch success {
case let .result(sunrise, sunset):
    print("Sunrise is at \(sunrise) and sunset is at \(sunset).")
case let .failure(message):
    print("Failure...  \(message)")
}
// Prints "Sunrise is at 6:00 am and sunset is at 8:09 pm."

```

> 实验
> 为 ServerResponse 和 switch 添加第三个分支。

请注意，日出和日落时间是作为将 ServerResponse 的值与 `switch` 分支匹配的一部分被提取出来的。

使用 `struct` 创建一个结构体。结构体支持许多与类相同的行为，包括方法和初始化器。结构体和类之间最重要的区别之一是，结构体在代码中传递时总是被复制的，而类是通过引用传递的。

```swift
struct Card {
    var rank: Rank
    var suit: Suit
    func simpleDescription() -> String {
        return "The \(rank.simpleDescription()) of \(suit.simpleDescription())"
    }
}
let threeOfSpades = Card(rank: .three, suit: .spades)
let threeOfSpadesDescription = threeOfSpades.simpleDescription()
```

> 实验
> 编写一个函数，返回一个包含完整扑克牌组的数组，每种花色和点数的组合各有一张。

## 并发

使用 `async` 标记一个异步运行的函数。

```swift
func fetchUserID(from server: String) async -> Int {
    if server == "primary" {
        return 97
    }
    return 501
}
```

您可以在异步函数调用前加上 `await` 来标记它。

```swift
func fetchUsername(from server: String) async -> String {
    let userID = await fetchUserID(from: server)
    if userID == 501 {
        return "John Appleseed"
    }
    return "Guest"
}
```

使用 `async let` 来调用异步函数，使其能够与其他异步代码并行运行。当您使用它返回的值时，写上 `await` 。

```swift
func connectUser(to server: String) async {
    async let userID = fetchUserID(from: server)
    async let username = fetchUsername(from: server)
    let greeting = await "Hello \(username), user ID \(userID)"
    print(greeting)
}
```

使用 `Task` 可以从同步代码中调用异步函数，而无需等待它们返回。

```swift
Task {
    await connectUser(to: "primary")
}
// Prints "Hello Guest, user ID 97"
```

使用任务组来组织并发代码。

```swift
let userIDs = await withTaskGroup(of: Int.self) { group in
    for server in ["primary", "secondary", "development"] {
        group.addTask {
            return await fetchUserID(from: server)
        }
    }


    var results: [Int] = []
    for await result in group {
        results.append(result)
    }
    return results
}
```

`Actor` 类似于类，不同之处在于它们确保不同的异步函数可以安全地同时与同一个 `actor` 实例交互。

```swift
actor ServerConnection {
    var server: String = "primary"
    private var activeUsers: [Int] = []
    func connect() async -> Int {
        let userID = await fetchUserID(from: server)
        // ... communicate with server ...
        activeUsers.append(userID)
        return userID
    }
}
```

当您在 `actor` 上调用方法或访问其属性时，您需要用 `await` 标记该代码，以表明它可能需要等待 `actor` 上已经在运行的其他代码完成。

```swift
let server = ServerConnection()
let userID = await server.connect()
```

## 协议与扩展

使用 `protocol` 来声明一个协议。

```swift
protocol ExampleProtocol {
     var simpleDescription: String { get }
     mutating func adjust()
}
```

类、枚举和结构体都可以遵循协议。

```swift
class SimpleClass: ExampleProtocol {
     var simpleDescription: String = "A very simple class."
     var anotherProperty: Int = 69105
     func adjust() {
          simpleDescription += "  Now 100% adjusted."
     }
}
var a = SimpleClass()
a.adjust()
let aDescription = a.simpleDescription


struct SimpleStructure: ExampleProtocol {
     var simpleDescription: String = "A simple structure"
     mutating func adjust() {
          simpleDescription += " (adjusted)"
     }
}
var b = SimpleStructure()
b.adjust()
let bDescription = b.simpleDescription
```

> 实验
> 为 ExampleProtocol 添加另一个要求。您需要对 SimpleClass 和 SimpleStructure 做出哪些更改，以便它们仍然符合该协议？

注意在 `SimpleStructure` 的声明中使用 `mutating` 关键字来标记会修改结构体的方法。而 `SimpleClass` 的声明则不需要将其任何方法标记为 `mutating`，因为类的方法总是可以修改类本身。
使用 `extension` 为已有类型添加功能，比如新增方法和计算属性。您可以使用扩展为在其他地方声明的类型添加协议遵循，甚至可以为您从库或框架中导入的类型添加协议遵循。

```swift
extension Int: ExampleProtocol {
    var simpleDescription: String {
        return "The number \(self)"
    }
    mutating func adjust() {
        self += 42
    }
 }
print(7.simpleDescription)
// Prints "The number 7"
```

> 实验
> 为 `Double` 类型编写一个扩展，添加一个 `absoluteValue` 属性。

您可以像使用其他命名类型一样使用协议名——例如，创建一个包含不同类型但都遵循同一协议的对象集合。当您处理类型为封装协议类型的值时，协议定义之外的方法是不可用的。

```swift
let protocolValue: any ExampleProtocol = a
print(protocolValue.simpleDescription)
// Prints "A very simple class. Now 100% adjusted."
// print(protocolValue.anotherProperty) // Uncomment to see the error
```

即使变量 `protocolValue` 的运行时类型是 `SimpleClass` ，编译器仍将其视为给定的 `ExampleProtocol` 类型。这意味着您无法意外访问该类在遵循协议之外实现的方法或属性。

## 错误处理

您可以使用任何遵循 `Error` 协议的类型来表示错误。

```swift
enum PrinterError: Error {
    case outOfPaper
    case noToner
    case onFire
}
```

使用 `throw` 来抛出错误，使用 `throws` 来标记一个可能抛出错误的函数。如果您在函数中抛出错误，该函数会立即返回，并且调用该函数的代码会处理这个错误。

```swift
func send(job: Int, toPrinter printerName: String) throws -> String {
    if printerName == "Never Has Toner" {
        throw PrinterError.noToner
    }
    return "Job sent"
}
```

有几种方法可以处理错误。一种方法是使用 `do-catc`h 。在 `do` 块中，您可以在可能抛出错误的代码前加上 `try` 来标记它。在 `catch` 块中，错误会自动被命名为 `error` ，除非您为它指定了其他名称。

```swift
do {
    let printerResponse = try send(job: 1040, toPrinter: "Bi Sheng")
    print(printerResponse)
} catch {
    print(error)
}
// Prints "Job sent"
```

> 实验
> 将打印机名称更改为 "Never Has Toner" ，这样 `send(job:toPrinter:)` 函数就会抛出错误。

您可以提供多个 `catch` 块来处理特定的错误。在 `catch` 后面写一个模式，就像在 `switch` 的 `case` 后面写模式一样。

```swift
do {
    let printerResponse = try send(job: 1440, toPrinter: "Gutenberg")
    print(printerResponse)
} catch PrinterError.onFire {
    print("I'll just put this over here, with the rest of the fire.")
} catch let printerError as PrinterError {
    print("Printer error: \(printerError).")
} catch {
    print(error)
}
// Prints "Job sent"
```

> 实验
> 在 `do` 块中添加抛出错误的代码。您需要抛出哪种类型的错误，才能让第一个 `catch` 块处理该错误？第二个和第三个块又如何？

另一种处理错误的方法是使用 `try?` 将结果转换为可选值。如果函数抛出错误，具体的错误会被丢弃，结果为 `nil` 。否则，结果是一个包含函数返回值的可选值。

```swift
let printerSuccess = try? send(job: 1884, toPrinter: "Mergenthaler")
let printerFailure = try? send(job: 1885, toPrinter: "Never Has Toner")
```

使用 `defer` 编写一段代码块，在函数中的所有其他代码执行完毕后、函数返回之前执行。无论函数是否抛出错误，这段代码都会被执行。您可以使用 defer 将设置和清理代码写在一起，尽管它们需要在不同的时间执行。

```swift
var fridgeIsOpen = false
let fridgeContent = ["milk", "eggs", "leftovers"]


func fridgeContains(_ food: String) -> Bool {
    fridgeIsOpen = true
    defer {
        fridgeIsOpen = false
    }


    let result = fridgeContent.contains(food)
    return result
}
if fridgeContains("banana") {
    print("Found a banana")
}
print(fridgeIsOpen)
// Prints "false"
```

## 泛型

在尖括号内写一个名称，以创建泛型函数或类型。

```swift
func makeArray<Item>(repeating item: Item, numberOfTimes: Int) -> [Item] {
    var result: [Item] = []
    for _ in 0..<numberOfTimes {
         result.append(item)
    }
    return result
}
makeArray(repeating: "knock", numberOfTimes: 4)

```

您可以为函数和方法，以及类、枚举和结构体创建泛型形式。

```swift
// Reimplement the Swift standard library's optional type
enum OptionalValue<Wrapped> {
    case none
    case some(Wrapped)
}
var possibleInteger: OptionalValue<Int> = .none
possibleInteger = .some(100)
```

在主体之前使用 `where` 来指定一组约束条件——例如，要求类型实现某个协议，要求两个类型相同，或要求某个类具有特定的父类。

```swift
func anyCommonElements<T: Sequence, U: Sequence>(_ lhs: T, _ rhs: U) -> Bool
    where T.Element: Equatable, T.Element == U.Element
{
    for lhsItem in lhs {
        for rhsItem in rhs {
            if lhsItem == rhsItem {
                return true
            }
        }
    }
   return false
}
anyCommonElements([1, 2, 3], [3])
```

> 实验
> 修改 `anyCommonElements(_:_:)` 函数，编写一个返回任意两个序列共有元素数组的函数。

编写 `<T: Equatable>` 与编写 `<T> ... where T: Equatable` 是一样的。

> 测试版软件
>
> 本文档包含关于正在开发中的 API 或技术的初步信息。这些信息可能会发生变化，根据本文档实现的软件应在最终操作系统软件上进行测试。
> 了解更多关于使用[Apple 测试版软件](https://developer.apple.com/support/beta-software/)的信息。
