# 属性

> 访问属于实例或类型的存储值和计算值。

属性将值与特定的类、结构或枚举相关联。存储属性将常量和变量值作为实例的一部分进行存储，而计算属性则计算（而不是存储）一个值。计算属性由类、结构和枚举提供。存储属性仅由类和结构提供。

存储属性和计算属性通常与特定类型的实例相关联。然而，属性也可以与类型本身相关联。这种属性称为类型属性。

此外，您可以定义属性观察者来监视属性值的变化，您可以通过自定义操作对此做出响应。属性观察者可以添加到您自己定义的存储属性，也可以添加到子类从其超类继承的属性。

您还可以使用属性包装器在多个属性的 getter 和 setter 中重用代码。

## [存储属性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties#Stored-Properties)

在最简单的形式中，存储属性是作为特定类或结构的实例的一部分存储的常量或变量。存储属性可以是变量存储属性（由 `var` 关键字引入）或常量存储属性（由 `let` 关键字引入）。

您可以在存储属性的定义中提供默认值，如默认属性值中所述。您还可以在初始化期间设置和修改存储属性的初始值。即使对于常量存储属性，这也是正确的，如在初始化期间分配常量属性中所述。

下面的示例定义了一个名为 `FixedLengthRange` 的结构，它描述了一个整数范围，其范围长度在创建后不能更改：

```swift
struct FixedLengthRange {
    var firstValue: Int
    let length: Int
}
var rangeOfThreeItems = FixedLengthRange(firstValue: 0, length: 3)
// the range represents integer values 0, 1, and 2
rangeOfThreeItems.firstValue = 6
// the range now represents integer values 6, 7, and 8
```

`FixedLengthRange` 的实例具有一个名为 `firstValue` 的变量存储属性和一个名为 `length` 的常量存储属性。在上面的示例中，当创建新的范围时， `length` 被初始化，并且此后不能更改，因为它是一个常量属性。

### [常量结构实例的存储属性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties#Stored-Properties-of-Constant-Structure-Instances)

如果您创建一个结构的实例并将该实例赋值给一个常量，您不能修改该实例的属性，即使它们被声明为变量属性：

```swift
let rangeOfFourItems = FixedLengthRange(firstValue: 0, length: 4)
// this range represents integer values 0, 1, 2, and 3
rangeOfFourItems.firstValue = 6
// this will report an error, even though firstValue is a variable property
```

因为 `rangeOfFourItems` 被声明为常量（使用 `let` 关键词），所以无法改变它的 `firstValue` 属性，即使 `firstValue` 是一个变量属性。

这种行为是由于结构体是值类型。当值类型的实例被标记为常量时，它的所有属性也都是常量。

对类来说并非如此，类是引用类型。如果将引用类型的实例分配给常量，您仍然可以更改该实例的变量属性。

### [延迟存储属性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties#Lazy-Stored-Properties)

延迟存储属性是指其初始值在第一次使用之前不会被计算的属性。您可以通过在声明之前写上 `lazy` 修饰符来指示延迟存储属性。

> 注意
>
> 您必须始终将懒属性声明为变量（使用 `var` 关键字），因为它的初始值可能在实例初始化完成后才会被获取。常量属性必须在初始化完成之前始终具有一个值，因此不能声明为懒属性。

当属性的初始值依赖于外部因素，而这些因素的值在实例初始化完成之前是未知的时，延迟属性非常有用。当属性的初始值需要复杂或计算昂贵的设置，而这设置不应在不需要时进行时，延迟属性也很有用。

下面的示例使用懒存储属性来避免复杂类的不必要初始化。此示例定义了两个类，分别称为 `DataImporter` 和 `DataManager` ，但这两个类的完整内容未显示：

```swift
class DataImporter {
    /*
    DataImporter is a class to import data from an external file.
    The class is assumed to take a nontrivial amount of time to initialize.
    */
    var filename = "data.txt"
    // the DataImporter class would provide data importing functionality here
}


class DataManager {
    lazy var importer = DataImporter()
    var data: [String] = []
    // the DataManager class would provide data management functionality here
}


let manager = DataManager()
manager.data.append("Some data")
manager.data.append("Some more data")
// the DataImporter instance for the importer property hasn't yet been created
```

`DataManager` 类有一个名为 `data` 的存储属性，该属性使用一个新的空数组初始化，数组中包含 `String` 值。尽管其余功能未显示，但这个 `DataManager` 类的目的是管理并提供对这个 `String` 数据数组的访问。

`DataManager` 类的部分功能是能够从文件中导入数据。此功能由 `DataImporter` 类提供，假设初始化需要非平凡的时间。这可能是因为 `DataImporter` 实例在初始化 `DataImporter` 实例时需要打开文件并将其内容读入内存。

因为 `DataManager` 实例有可能在不从文件中导入数据的情况下管理其数据，所以 `DataManager` 在创建 `DataManager` 本身时并不会创建新的 `DataImporter` 实例。相反，只有在首次使用时创建 `DataImporter` 实例才更有意义。

因为它被标记为 `lazy` 修饰符， `importer` 属性的 `DataImporter` 实例仅在第一次访问 `importer` 属性时创建，例如当查询其 `filename` 属性时：

```swift
print(manager.importer.filename)
// the DataImporter instance for the importer property has now been created
// Prints "data.txt"
```

> 注意
>
> 如果一个带有 `lazy` 修饰符的属性被多个线程同时访问，并且该属性尚未初始化，则无法保证该属性只会初始化一次。

### [存储属性和实例变量](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties#Stored-Properties-and-Instance-Variables)

如果您有过使用 Objective-C 的经验，您可能知道它提供了两种方式来将值和引用存储为类实例的一部分。除了属性，您还可以使用实例变量作为存储在属性中的值的备份存储。

Swift 将这些概念统一为一个单一的属性声明。Swift 属性没有对应的实例变量，属性的后备存储不会被直接访问。这种方法避免了在不同上下文中对值访问方式的混淆，并将属性的声明简化为一个单一、明确的语句。关于属性的所有信息——包括其名称、类型和内存管理特性——都在类型定义的一个位置中定义。

## [计算属性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties#Computed-Properties)

除了存储属性，类、结构和枚举还可以定义计算属性，这些属性实际上并不存储一个值。相反，它们提供了一个 getter 和一个可选的 setter，以间接地获取和设置其他属性和值。

```swift
struct Point {
    var x = 0.0, y = 0.0
}
struct Size {
    var width = 0.0, height = 0.0
}
struct Rect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return Point(x: centerX, y: centerY)
        }
        set(newCenter) {
            origin.x = newCenter.x - (size.width / 2)
            origin.y = newCenter.y - (size.height / 2)
        }
    }
}
var square = Rect(origin: Point(x: 0.0, y: 0.0),
    size: Size(width: 10.0, height: 10.0))
let initialSquareCenter = square.center
// initialSquareCenter is at (5.0, 5.0)
square.center = Point(x: 15.0, y: 15.0)
print("square.origin is now at (\(square.origin.x), \(square.origin.y))")
// Prints "square.origin is now at (10.0, 10.0)"
```

这个例子定义了三个结构体用于处理几何形状：

- `Point` 封装了一个点的 x 和 y 坐标。
- `Size` 封装了一个 `width` 和一个 `height` 。
- `Rect` 通过一个原点和一个大小定义一个矩形。

`Rect` 结构还提供了一个名为 `center` 的计算属性。 `Rect` 的当前中心位置可以始终从其 `origin` 和 `size` 确定，因此您不需要将中心点作为显式 `Point` 值存储。相反， `Rect` 定义了一个名为 `center` 的计算变量的自定义 getter 和 setter，以便您可以像处理真实存储属性一样处理矩形的 `center` 。

上面的示例创建了一个新的 `Rect` 变量，名为 `square` 。 `square` 变量的初始点为 `(0, 0)` ，宽度和高度为 `10` 。这个正方形在下面的图示中由浅绿色的正方形表示。

`square` 变量的 `center` 属性通过点语法 ( `square.center` ) 被访问，这会调用 `center` 的 getter，以检索当前属性值。getter 实际上计算并返回一个新的 `Point` ，以表示正方形的中心，而不是返回现有值。如上所示，getter 正确返回了中心点 `(5, 5)` 。

然后将 `center` 属性设置为新的值 `(15, 15)` ，这将方块向上和向右移动，移动到下图中深绿色方块所示的新位置。设置 `center` 属性会调用 `center` 的设置器，这会修改存储的 `origin` 属性的 `x` 和 `y` 值，并将方块移动到其新位置。

![computedProperties](https://docs.swift.org/swift-book/images/org.swift.tspl/computedProperties@2x.png){.light-only}
![computedProperties~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/computedProperties~dark@2x.png){.dark-only}

### [简写设置器声明](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties#Shorthand-Setter-Declaration)

如果计算属性的设置器没有为要设置的新值定义名称，则使用默认名称 `newValue` 。这是一个利用这种简写表示法的 `Rect` 结构的替代版本：

```swift
struct AlternativeRect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return Point(x: centerX, y: centerY)
        }
        set {
            origin.x = newValue.x - (size.width / 2)
            origin.y = newValue.y - (size.height / 2)
        }
    }
}
```

### [简写获取器声明](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties#Shorthand-Getter-Declaration)

如果 getter 的整个主体是单个表达式，getter 会隐式返回该表达式。这是另一个利用这种缩写语法和 setter 缩写语法的 `Rect` 结构的版本：

```swift
struct CompactRect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            Point(x: origin.x + (size.width / 2),
                  y: origin.y + (size.height / 2))
        }
        set {
            origin.x = newValue.x - (size.width / 2)
            origin.y = newValue.y - (size.height / 2)
        }
    }
}
```

从 getter 中省略 `return` 遵循与从函数中省略 `return` 相同的规则，如《具有隐式返回的函数》中所述。

### [只读计算属性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties#Read-Only-Computed-Properties)

具有 getter 但没有 setter 的计算属性称为只读计算属性。只读计算属性始终返回一个值，可以通过点语法访问，但不能设置为不同的值。

> 注意
>
> 您必须将计算属性（包括只读计算属性）声明为使用 `var` 关键字的变量属性，因为它们的值不是固定的。 `let` 关键字仅用于常量属性，以指示它们的值在实例初始化后无法更改。

您可以通过删除 `get` 关键字及其大括号来简化只读计算属性的声明：

```swift
struct Cuboid {
    var width = 0.0, height = 0.0, depth = 0.0
    var volume: Double {
        return width * height * depth
    }
}
let fourByFiveByTwo = Cuboid(width: 4.0, height: 5.0, depth: 2.0)
print("the volume of fourByFiveByTwo is \(fourByFiveByTwo.volume)")
// Prints "the volume of fourByFiveByTwo is 40.0"
```

此示例定义了一个名为 `Cuboid` 的新结构，它表示一个具有 `width` 、 `height` 和 `depth` 属性的 3D 矩形盒。该结构还有一个只读计算属性，名为 `volume` ，用于计算并返回长方体的当前体积。将 `volume` 设置为可设置是没有意义的，因为这会导致对于特定的 `volume` 值，应该使用哪些 `width` 、 `height` 和 `depth` 的值变得模糊。然而，对于 `Cuboid` 提供一个只读计算属性以使外部用户能够发现其当前计算的体积是很有用的。

## [属性观察者](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties#Property-Observers)

属性观察者观察并响应属性值的变化。每当属性的值被设置时，属性观察者都会被调用，即使新值与属性的当前值相同。

您可以在以下位置添加属性观察者：

- 您定义的存储属性
- 继承的存储属性
- 您继承的计算属性

对于继承的属性，您可以通过在子类中重写该属性来添加属性观察者。对于您定义的计算属性，请使用属性的设置器来观察和响应值的变化，而不是尝试创建观察者。重写属性的内容在重写中描述。

您可以选择在属性上定义这两个观察者中的一个或两个：

- `willSet` 在值被存储之前被调用。
- `didSet` 在新值存储后立即被调用。

如果您实现一个 `willSet` 观察者，它会将新的属性值作为常量参数传递给您。您可以在 `willSet` 实现中为此参数指定名称。如果您在实现中不写参数名称和括号，则该参数将以默认参数名称 `newValue` 可用。

同样，如果您实现一个 `didSet` 观察者，它会将包含旧属性值的常量参数传递给您。您可以命名参数或使用默认参数名称 `oldValue` 。如果您在自己的 `didSet` 观察者中为属性分配值，则您分配的新值将替代刚刚设置的值。

> 注意
>
> 当在子类的初始化器中设置属性时，超类属性的 `willSet` 和 `didSet` 观察者会被调用，这在超类初始化器被调用之后。它们不会在类设置自己的属性时被调用，即在超类初始化器被调用之前。

有关初始化委托的更多信息，请参阅值类型的初始化委托和类类型的初始化委托。

这是一个 `willSet` 和 `didSet` 实际应用的例子。下面的例子定义了一个名为 `StepCounter` 的新类，该类跟踪一个人在走路时所走的总步数。这个类可能与来自计步器或其他步数计的数据一起使用，以跟踪一个人在日常生活中的锻炼情况。

```swift
class StepCounter {
    var totalSteps: Int = 0 {
        willSet(newTotalSteps) {
            print("About to set totalSteps to \(newTotalSteps)")
        }
        didSet {
            if totalSteps > oldValue  {
                print("Added \(totalSteps - oldValue) steps")
            }
        }
    }
}
let stepCounter = StepCounter()
stepCounter.totalSteps = 200
// About to set totalSteps to 200
// Added 200 steps
stepCounter.totalSteps = 360
// About to set totalSteps to 360
// Added 160 steps
stepCounter.totalSteps = 896
// About to set totalSteps to 896
// Added 536 steps
```

`StepCounter` 类声明了一个 `totalSteps` 类型的 `Int` 属性。这是一个具有 `willSet` 和 `didSet` 观察者的存储属性。

每当属性被赋予新值时， `willSet` 和 `didSet` 观察者会被调用。这在新值与当前值相同时也成立。

此示例的 `willSet` 观察者为即将到来的新值使用自定义参数名称 `newTotalSteps` 。在这个示例中，它仅仅打印出即将被设置的值。

`didSet` 观察者在 `totalSteps` 的值被更新后被调用。它将 `totalSteps` 的新值与旧值进行比较。如果步骤总数增加，则会打印出一条消息，以指示新增了多少步。 `didSet` 观察者没有为旧值提供自定义参数名称，而是使用默认名称 `oldValue` 。

> 注意
>
> 如果您将具有观察者的属性作为输入输出参数传递给一个函数，则始终调用 `willSet` 和 `didSet` 观察者。这是因为输入输出参数的复制输入复制输出内存模型：值始终在函数结束时写回到属性中。有关输入输出参数行为的详细讨论，请参见输入输出参数。

## [属性包装器](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties#Property-Wrappers)

属性包装器在管理属性存储方式的代码和定义属性的代码之间添加了一层分离。例如，如果您有提供线程安全检查或将其底层数据存储在数据库中的属性，您必须在每个属性上编写那段代码。当您使用属性包装器时，您在定义包装器时只需编写一次管理代码，然后通过将其应用于多个属性来重复使用该管理代码。

要定义一个属性包装器，您需要创建一个结构体、枚举或类来定义一个 `wrappedValue` 属性。在下面的代码中， `TwelveOrLess` 结构体确保它包装的值始终包含一个小于或等于 12 的数字。如果您要求它存储一个更大的数字，它将存储 12。

```swift
@propertyWrapper
struct TwelveOrLess {
    private var number = 0
    var wrappedValue: Int {
        get { return number }
        set { number = min(newValue, 12) }
    }
}
```

setter 确保新值小于或等于 12，而 getter 返回存储的值。

> 注意
>
> 上面示例中 `number` 的声明将变量标记为 `private` ，这确保 `number` 仅在 `TwelveOrLess` 的实现中使用。在其他地方编写的代码通过 `wrappedValue` 的 getter 和 setter 访问该值，无法直接使用 `number` 。有关 `private` 的信息，请参见访问控制。

您通过在属性前写上包装器的名称作为属性来应用包装器。以下是一个结构体，存储一个矩形，使用 `TwelveOrLess` 属性包装器以确保其尺寸始终不超过 12：

```swift
struct SmallRectangle {
    @TwelveOrLess var height: Int
    @TwelveOrLess var width: Int
}


var rectangle = SmallRectangle()
print(rectangle.height)
// Prints "0"


rectangle.height = 10
print(rectangle.height)
// Prints "10"


rectangle.height = 24
print(rectangle.height)
// Prints "12"
```

`height` 和 `width` 属性的初始值来自 `TwelveOrLess` 的定义，该定义将 `TwelveOrLess.number` 设置为零。 `TwelveOrLess` 中的 setter 将 10 视为有效值，因此将数字 10 存储在 `rectangle.height` 中的操作按原样进行。然而，24 超出了 `TwelveOrLess` 允许的范围，因此尝试存储 24 最终将 `rectangle.height` 设置为 12，这是允许的最大值。

当您将包装器应用于一个属性时，编译器会合成代码，为包装器提供存储空间，并提供通过包装器访问属性的代码。（属性包装器负责存储被包装的值，因此没有合成代码用于此。）您可以编写代码使用属性包装器的行为，而不利用特殊的属性语法。例如，这里是前面代码清单中 `SmallRectangle` 的一个版本，它将其属性明确地封装在 `TwelveOrLess` 结构中，而不是将 `@TwelveOrLess` 写为一个属性：

```swift
struct SmallRectangle {
    private var _height = TwelveOrLess()
    private var _width = TwelveOrLess()
    var height: Int {
        get { return _height.wrappedValue }
        set { _height.wrappedValue = newValue }
    }
    var width: Int {
        get { return _width.wrappedValue }
        set { _width.wrappedValue = newValue }
    }
}
```

`_height` 和 `_width` 属性存储属性包装器 `TwelveOrLess` 的一个实例。 `height` 和 `width` 的 getter 和 setter 封装对 `wrappedValue` 属性的访问。

### [设置封装属性的初始值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties#Setting-Initial-Values-for-Wrapped-Properties)

上面示例中的代码通过在 `TwelveOrLess` 的定义中为 `number` 指定初始值来设置封装属性的初始值。使用此属性包装器的代码不能为由 `TwelveOrLess` 封装的属性指定不同的初始值——例如， `SmallRectangle` 的定义不能为 `height` 或 `width` 指定初始值。为了支持设置初始值或其他自定义，属性包装器需要添加一个初始化器。这里有一个扩展版本的 `TwelveOrLess` ，称为 `SmallNumber` ，它定义了设置封装值和最大值的初始化器：

```swift
@propertyWrapper
struct SmallNumber {
    private var maximum: Int
    private var number: Int


    var wrappedValue: Int {
        get { return number }
        set { number = min(newValue, maximum) }
    }


    init() {
        maximum = 12
        number = 0
    }
    init(wrappedValue: Int) {
        maximum = 12
        number = min(wrappedValue, maximum)
    }
    init(wrappedValue: Int, maximum: Int) {
        self.maximum = maximum
        number = min(wrappedValue, maximum)
    }
}
```

`SmallNumber` 的定义包括三个初始化器 — `init()` 、 `init(wrappedValue:)` 和 `init(wrappedValue:maximum:)` — 示例中使用这些初始化器来设置包装值和最大值。有关初始化和初始化器语法的信息，请参见初始化。

当您对一个属性应用包装器而不指定初始值时，Swift 使用 `init()` 初始化器来设置包装器。例如：

```swift
struct ZeroRectangle {
    @SmallNumber var height: Int
    @SmallNumber var width: Int
}


var zeroRectangle = ZeroRectangle()
print(zeroRectangle.height, zeroRectangle.width)
// Prints "0 0"
```

`SmallNumber` 包装的 `height` 和 `width` 的实例是通过调用 `SmallNumber()` 创建的。初始化器内部的代码设置了初始包装值和初始最大值，使用默认值零和 12。属性包装器仍然提供所有初始值，就像早期示例中在 `SmallRectangle` 中使用 `TwelveOrLess` 一样。与该示例不同， `SmallNumber` 还支持在声明属性时写入这些初始值。

当您为属性指定初始值时，Swift 使用 `init(wrappedValue:)` 初始化器来设置包装器。例如：

```swift
struct UnitRectangle {
    @SmallNumber var height: Int = 1
    @SmallNumber var width: Int = 1
}


var unitRectangle = UnitRectangle()
print(unitRectangle.height, unitRectangle.width)
// Prints "1 1"
```

当您在一个带有包装器的属性上写 `= 1` 时，会被转换为对 `init(wrappedValue:)` 初始化器的调用。包装 `height` 和 `width` 的 `SmallNumber` 实例是通过调用 `SmallNumber(wrappedValue: 1)` 创建的。初始化器使用这里指定的包装值，并使用默认的最大值 12。

当您在自定义属性后面的括号中写入参数时，Swift 会使用接受这些参数的初始化器来设置包装器。例如，如果您提供一个初始值和一个最大值，Swift 会使用 `init(wrappedValue:maximum:)` 初始化器：

```swift
struct NarrowRectangle {
    @SmallNumber(wrappedValue: 2, maximum: 5) var height: Int
    @SmallNumber(wrappedValue: 3, maximum: 4) var width: Int
}


var narrowRectangle = NarrowRectangle()
print(narrowRectangle.height, narrowRectangle.width)
// Prints "2 3"


narrowRectangle.height = 100
narrowRectangle.width = 100
print(narrowRectangle.height, narrowRectangle.width)
// Prints "5 4"
```

包裹 `height` 的 `SmallNumber` 实例是通过调用 `SmallNumber(wrappedValue: 2, maximum: 5)` 创建的，而包裹 `width` 的实例是通过调用 `SmallNumber(wrappedValue: 3, maximum: 4)` 创建的。

通过包含属性包装器的参数，您可以在包装器中设置初始状态或在创建时传递其他选项给包装器。这种语法是使用属性包装器的最通用方式。您可以提供所需的任何参数给属性，它们会传递给初始化器。

当您包含属性包装器参数时，您还可以使用赋值指定初始值。Swift 将赋值视为 `wrappedValue` 参数，并使用接受您包含的参数的初始化器。例如：

```swift
struct MixedRectangle {
    @SmallNumber var height: Int = 1
    @SmallNumber(maximum: 9) var width: Int = 2
}


var mixedRectangle = MixedRectangle()
print(mixedRectangle.height)
// Prints "1"


mixedRectangle.height = 20
print(mixedRectangle.height)
// Prints "12"
```

包装 `height` 的 `SmallNumber` 实例是通过调用 `SmallNumber(wrappedValue: 1)` 创建的，该调用使用默认的最大值 12。包装 `width` 的实例是通过调用 `SmallNumber(wrappedValue: 2, maximum: 9)` 创建的。

### [从属性包装器投影值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties#Projecting-a-Value-From-a-Property-Wrapper)

除了包装的值，属性包装器还可以通过定义一个投影值来暴露额外的功能——例如，一个管理数据库访问的属性包装器可以在其投影值上暴露一个 `flushDatabaseConnection()` 方法。投影值的名称与包装值相同，只是前面加了一个美元符号 ( `$` )。由于您的代码不能定义以 `$` 开头的属性，因此投影值从不干扰您定义的属性。

在上面的 `SmallNumber` 示例中，如果您尝试将属性设置为一个过大的数字，属性包装器会在存储之前调整这个数字。下面的代码向 `SmallNumber` 结构添加一个 `projectedValue` 属性，以跟踪属性包装器在存储新值之前是否调整了属性的新值。

```swift
@propertyWrapper
struct SmallNumber {
    private var number: Int
    private(set) var projectedValue: Bool


    var wrappedValue: Int {
        get { return number }
        set {
            if newValue > 12 {
                number = 12
                projectedValue = true
            } else {
                number = newValue
                projectedValue = false
            }
        }
    }


    init() {
        self.number = 0
        self.projectedValue = false
    }
}
struct SomeStructure {
    @SmallNumber var someNumber: Int
}
var someStructure = SomeStructure()


someStructure.someNumber = 4
print(someStructure.$someNumber)
// Prints "false"


someStructure.someNumber = 55
print(someStructure.$someNumber)
// Prints "true"
```

写入 `someStructure.$someNumber` 访问包装器的投影值。存储一个小数字如四后， `someStructure.$someNumber` 的值为 `false` 。但是，在尝试存储一个太大的数字，比如 55 后，投影值为 `true` 。

属性包装器可以返回任何类型的值作为其投影值。在这个例子中，属性包装器仅暴露一条信息——数字是否被调整——因此它将该布尔值作为其投影值。一个需要暴露更多信息的包装器可以返回某个其他类型的实例，或者它可以返回 `self` 以将包装器的实例作为其投影值。

当您从代码中访问投影值时，这段代码是类型的一部分，例如属性获取器或实例方法，您可以省略 `self.` 放在属性名称之前，就像访问其他属性一样。以下例子中的代码将包装器围绕 `height` 和 `width` 的投影值称为 `$height` 和 `$width` :

```swift
enum Size {
    case small, large
}


struct SizedRectangle {
    @SmallNumber var height: Int
    @SmallNumber var width: Int


    mutating func resize(to size: Size) -> Bool {
        switch size {
        case .small:
            height = 10
            width = 20
        case .large:
            height = 100
            width = 100
        }
        return $height || $width
    }
}
```

因为属性包装器语法只是一个带有 getter 和 setter 的属性的语法糖，访问 `height` 和 `width` 的行为与访问任何其他属性相同。例如， `resize(to:)` 中的代码使用它们的属性包装器访问 `height` 和 `width` 。如果您调用 `resize(to: .large)` ， `.large` 的 switch case 将矩形的高度和宽度设置为 100。包装器防止这些属性的值大于 12，并将投影值设置为 `true` ，以记录它调整了它们的值。在 `resize(to:)` 的末尾，返回语句检查 `$height` 和 `$width` 以确定属性包装器是否调整了 `height` 或 `width` 。

## [全局变量和局部变量](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties#Global-and-Local-Variables)

上述用于计算和观察属性的能力同样适用于全局变量和局部变量。全局变量是在任何函数、方法、闭包或类型上下文之外定义的变量。局部变量是在函数、方法或闭包上下文中定义的变量。

您在前面的章节中遇到的全局变量和局部变量都是存储变量。存储变量与存储属性一样，为某种类型的值提供存储，并允许设置和检索该值。

然而，您还可以定义计算变量并为存储变量定义观察者，无论是在全局范围还是局部范围。计算变量计算其值，而不是存储它，并且它们的写法与计算属性相同。

> 注意
>
> 全局常量和变量总是以懒惰的方式计算，这与懒惰存储属性类似。与懒惰存储属性不同，全局常量和变量不需要使用 `lazy` 修饰符标记。

局部常量和变量从不懒惰计算。

您可以将属性包装器应用于本地存储变量，但不能应用于全局变量或计算变量。例如，在下面的代码中， `myNumber` 将 `SmallNumber` 作为属性包装器使用。

```swift
func someFunction() {
    @SmallNumber var myNumber: Int = 0


    myNumber = 10
    // now myNumber is 10


    myNumber = 24
    // now myNumber is 12
}
```

就像当您将 `SmallNumber` 应用到一个属性时，将 `myNumber` 的值设置为 10 是有效的。因为属性包装器不允许值高于 12，所以它将 `myNumber` 设置为 12，而不是 24。

## [类型属性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties#Type-Properties)

实例属性是属于特定类型实例的属性。每次您创建该类型的新实例时，它都有自己的一组属性值，与任何其他实例分开。

您还可以定义属于类型本身的属性，而不是属于该类型的任何一个实例。这些属性只有一份副本，无论您创建多少个该类型的实例。这种类型的属性称为类型属性。

类型属性对于定义特定类型的所有实例的通用值非常有用，例如所有实例都可以使用的常量属性（如 C 中的静态常量），或者存储对该类型所有实例全局值的变量属性（如 C 中的静态变量）。

存储的类型属性可以是变量或常量。计算类型属性始终被声明为变量属性，这与计算实例属性的声明方式相同。

> 注意
>
> 与存储实例属性不同，您必须始终为存储类型属性提供默认值。这是因为类型本身没有初始化器可以在初始化时为存储类型属性分配值。

存储类型属性在首次访问时会被延迟初始化。即使被多个线程同时访问，它们也保证只会初始化一次，并且不需要标记为 `lazy` 修饰符。

### [类型 属性 语法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties#Type-Property-Syntax)

在 C 和 Objective-C 中，您将与类型相关的静态常量和变量定义为全局静态变量。然而，在 Swift 中，类型属性作为类型定义的一部分写在类型的外部大括号内，并且每个类型属性明确作用于它所支持的类型。

您使用 `static` 关键字定义类型属性。对于类类型的计算类型属性，您可以改用 `class` 关键字，以允许子类覆盖超类的实现。下面的示例显示了存储和计算类型属性的语法：

```swift
struct SomeStructure {
    static var storedTypeProperty = "Some value."
    static var computedTypeProperty: Int {
        return 1
    }
}
enum SomeEnumeration {
    static var storedTypeProperty = "Some value."
    static var computedTypeProperty: Int {
        return 6
    }
}
class SomeClass {
    static var storedTypeProperty = "Some value."
    static var computedTypeProperty: Int {
        return 27
    }
    class var overrideableComputedTypeProperty: Int {
        return 107
    }
}
```

> 注意
>
> 上面的计算类型属性示例是针对只读计算类型属性的，但您也可以使用与计算实例属性相同的语法定义读写计算类型属性。

### [查询和设置类型属性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties#Querying-and-Setting-Type-Properties)

类型属性使用点语法进行查询和设置，就像实例属性一样。然而，类型属性是在类型上进行查询和设置，而不是在该类型的实例上。例如：

```swift
print(SomeStructure.storedTypeProperty)
// Prints "Some value."
SomeStructure.storedTypeProperty = "Another value."
print(SomeStructure.storedTypeProperty)
// Prints "Another value."
print(SomeEnumeration.computedTypeProperty)
// Prints "6"
print(SomeClass.computedTypeProperty)
// Prints "27"
```

以下示例使用两个存储的类型属性作为一个结构的一部分，该结构模拟多个音频通道的音频电平计。每个通道的音频电平是一个介于 `0` 和 `10` 之间的整数，包括 `0` 和 `10`。

下图说明了如何将这两个音频通道组合在一起以模拟立体声音频电平计。当一个通道的音频电平为 `0` 时，该通道的灯光均未点亮。当音频电平为 `10` 时，该通道的所有灯光均点亮。在此图中，左通道的当前电平为 `9` ，右通道的当前电平为 `7` ：

![staticPropertiesVUMeter](https://docs.swift.org/swift-book/images/org.swift.tspl/staticPropertiesVUMeter@2x.png){.light-only}
![staticPropertiesVUMeter~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/staticPropertiesVUMeter~dark@2x.png){.dark-only}

上述描述的音频通道由 `AudioChannel` 结构的实例表示：

```swift
struct AudioChannel {
    static let thresholdLevel = 10
    static var maxInputLevelForAllChannels = 0
    var currentLevel: Int = 0 {
        didSet {
            if currentLevel > AudioChannel.thresholdLevel {
                // cap the new audio level to the threshold level
                currentLevel = AudioChannel.thresholdLevel
            }
            if currentLevel > AudioChannel.maxInputLevelForAllChannels {
                // store this as the new overall maximum input level
                AudioChannel.maxInputLevelForAllChannels = currentLevel
            }
        }
    }
}
```

`AudioChannel` 结构定义了两个存储类型属性以支持其功能。第一个， `thresholdLevel` ，定义了音频级别可以达到的最大阈值。对于所有 `AudioChannel` 实例，这是一个常量值 `10` 。如果音频信号的值高于 `10` ，它将被限制在这个阈值（如下所述）。

第二种类型属性是一个名为 `maxInputLevelForAllChannels` 的变量存储属性。它跟踪任何 `AudioChannel` 实例接收到的最大输入值。它的初始值为 `0` 。

`AudioChannel` 结构还定义了一个名为 `currentLevel` 的存储实例属性，表示通道当前的音频级别，范围从 `0` 到 `10` 。

The `currentLevel` 属性有一个 `didSet` 属性观察者，用于检查每次设置时 `currentLevel` 的值。该观察者执行两个检查：

- 如果 `currentLevel` 的新值大于允许的 `thresholdLevel` ，则属性观察者将 `currentLevel` 限制为 `thresholdLevel` 。
- 如果 `currentLevel` 的新值（经过任何限制后）高于任何 `AudioChannel` 实例之前接收到的任何值，则属性观察者将新的 `currentLevel` 值存储在 `maxInputLevelForAllChannels` 类型的属性中。

> 注意
>
> 在这两个检查中的第一个中， `didSet` 观察者将 `currentLevel` 设置为一个不同的值。然而，这并不会导致观察者再次被调用。

您可以使用 `AudioChannel` 结构创建两个新音频通道，分别称为 `leftChannel` 和 `rightChannel` ，以表示立体声系统的音频级别：

```swift
var leftChannel = AudioChannel()
var rightChannel = AudioChannel()
```

如果将左声道的 `currentLevel` 设置为 `7` ，您可以看到 `maxInputLevelForAllChannels` 类型属性已更新为 `7` ：

```swift
leftChannel.currentLevel = 7
print(leftChannel.currentLevel)
// Prints "7"
print(AudioChannel.maxInputLevelForAllChannels)
// Prints "7"
```

如果您尝试将右通道的 `currentLevel` 设置为 `11` ，您会看到右通道的 `currentLevel` 属性被限制为最大值 `10` ，并且 `maxInputLevelForAllChannels` 类型属性被更新为 `10` ：

```swift
rightChannel.currentLevel = 11
print(rightChannel.currentLevel)
// Prints "10"
print(AudioChannel.maxInputLevelForAllChannels)
// Prints "10"
```
