# 关于语言参考

> 阅读正式语法使用的符号说明。

本书的这一部分描述了 Swift 编程语言的正式语法。这里描述的语法旨在帮助您更详细地理解该语言，而不是让您直接实现解析器或编译器。

Swift 语言相对较小，因为许多在 Swift 代码中几乎随处可见的常见类型、函数和运算符实际上是在 Swift 标准库中定义的。尽管这些类型、函数和运算符并不是 Swift 语言本身的一部分，但它们在本书这一部分的讨论和代码示例中被广泛使用。

## [如何阅读语法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/aboutthelanguagereference#How-to-Read-the-Grammar)

用于描述 Swift 编程语言的正式语法的符号遵循一些约定：

- 箭头（→）用于标记语法生成，并可以理解为“可以由……组成。”
- 语法类别用斜体文本表示，并出现在语法产生式规则的两侧。
- 字面单词和标点符号用 `boldface constant width` 文本表示，仅出现在语法产生式规则的右侧。
- 替代语法产生式由竖线 (|) 分隔。当替代产生式太长而难以阅读时，会被拆分成多个新行的语法产生式规则。
- 在少数情况下，使用常规字体文本来描述语法产生式规则的右侧。
- 可选的语法类别和文字由后缀问号标记，?。

作为示例，getter-setter 块的语法定义如下：

getter-setter 块的语法

_getter-setter-block_ → **`{`** _getter-clause_ _setter-clause?_ **`}`** | **`{`** _setter-clause_ _getter-clause_ **`}`**

此定义表明，getter-setter 块可以由一个 getter 子句后跟一个可选的 setter 子句（用大括号括起来）组成，或者由一个 setter 子句后跟一个 getter 子句（用大括号括起来）组成。上述语法生成与以下两个生成等效，其中替代项被明确列出：

getter-setter 块的语法

_getter-setter-block_ → **`{`** _getter-clause_ _setter-clause?_ **`}`**\
&#xNAN;_&#x67;etter-setter-block_ → **`{`** _setter-clause_ _getter-clause_ **`}`**
