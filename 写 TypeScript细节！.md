# 写 TypeScript 时，很多人可能会犯的几个错误！

TypeScript 和 JavaScript 在过去几年中不断进步，我们在过去点时间中建立的一些实践可能已经过时。有些可能永远没有意义，下面我列出了很多=开发者可能会犯的几个错误！

# 1.没有使用严格模式

通过使用没有严格模式的 tsconfig.json。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI2W1Ocqs3jagYsMlay1t6iajrOgUvZFyT9ooXOY1sNNvYicjQaVkouh5og/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

使用严格模式后。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI2icjcuUyQ4vgiag1V5r4yibpsRrJFOxPbLUibEFzZpnMmfX6jdBib105Iiamw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

### 我们为什么要使用严格模式？

严格模式**可以消除语法里一些不合理，不严谨的地方，可以让TS往更合理、更安全、更严谨的方向去发展**: 通过将一些TS的静默错误更改为抛出错误，消除了TS的一些静默错误，能更加有效保障代码运行的安全；提高编译器效率，增加运行速度；禁止一些可能在ECMAScript未来版本中定义的语法。

# 2. 使用 || 确定默认值

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI2aTentLiaVuDE9unnicrBPibRicRicMTakhMjrHDklJEWmdG37a7or6Jqtsw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

### 那它应该是什么样子的呢？

使用最新的`??`运算符或者最好是在参数级别定义返回值。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI25nOtNa7LfJZyVnjgBJQFEpRjprcr0tKXIkia30H41N57NibicNJiamkV3A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

这`??`运算符去年才被引入，如果在长函数的中间使用值，可能很难将它们定义为参数默认值。

?? 与 || 不同，它只返回 null 或 undefined，而不是所有 falsy 值。

# 3.使用any作为类型

当我们不确定数据类型的时候，会使用any类型的数据。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI2dSicnUUSLjbibcGic7mjk4ycO1hHDs9ElJKHjpCuOVFPIibRou5CNgzsIw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

在所有我们不确定类型的情况下，我们都应该使用unknown。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI26ibnVfjyMibtcZAiafVQOfH9AvA7GnZibz68HL0LqCbcATb3ErwvxZzsBg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

### 为什么要这么做呢？

any 很简单，因为它从根本上禁用了所有类型检查。通常，即使在官方类型中也使用 any（例如，上面示例中的 response.json() 被 TypeScript 团队键入为 `Promise<any>`）。

### 为什么不能用any？

它从根本上禁用所有类型检查。通过 any 进入的所有值都将完全放弃任何类型检查。这可能会变得非常难以捕捉错误，因为只有当我们对类型结构的假设符合运行时代码时，代码才会失败。

# 4. val 作为 SomeType

### 强制告诉编译器它无法推断的类型。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI2f1oNRj9Pr489vqibtkrL1EaQUCKEK2xUzgu1sDpGqKDPsPhIq9ezGQQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

### 这就是类型守卫的用途。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI2QbTLIg01iaeYq7xpibkics26fcrre5wicppDSqS00gPf2OfibSLxE63E9dw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

当我们想要从 JavaScript 转换为 TypeScript 时，现有的代码库经常对 TypeScript 编译器无法自动得出的类型做出假设。在这些情况下，使用快速 as SomeOtherType 可以加快转换速度，而无需放松 tsconfig 中的设置。

即使现在可以保存断言，但当有人移动代码时，这可能会改变。类型保护将确保所有检查都是明确的。

# 5. any在测试用例中的表现

### 在编写测试时

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI2jZa5tuolPEqSUPoSBluh59VfTODesMdI3CyEBRN1KhUGib0VdlpQjPA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

如果需要为测试模拟数据，需要将模拟逻辑移动到我们模拟的旁边并使其可重用。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI2dKibrH2o50SmJiaJPCyrg4frDDlHjTD3DNgnu76kotAX8W5pwz35NqHw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

虽然在尚未有很好的测试覆盖率的代码库中编写测试时，经常会出现复杂的大数据结构，但测试中的特定功能只需要其中的一部分。短期内无需担心其他属性更简单。

最近一次是当其中一个属性发生变化时，我们必须在所有测试中更改它而不是一个中心位置。此外，在某些情况下，被测代码依赖于我们之前认为不重要的属性，然后必须更新该功能的所有测试。

# 6. 可选属性

### 将属性定义为有时存在，有时不存在的可选属性。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI23q31sofUz7e6s2eshOvvVhRLJrcWJb05FCAMl4F4BPrffO9z6UVpBQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

### 清楚地表达，模型哪些组合存在，哪些不存在。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI2dvBK4ibT5tgxVQ8nhN5yzQDnKGW9kxvRhXTYTPibapNaCib3OJicwQKEFw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

将属性定义为可选而不是划分类型更容易并且生成的代码更少。它还需要对正在开发的产品有充分的了解，并且可以在对产品的假设发生变化时限制代码的使用。

类型系统的最大好处是它们可以用编译时检查代替运行时检查。通过更多的快速输入，可以在编译时检查可能被忽视的错误。

# 7. 使用一个字母作为泛型参数

### 用一个字母给作为名称，比如常用的T作为泛型名称！

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI2CMkW5gGmtAOvW3rNpX1Q5xG93gt85hw4zicapBH7cQmmiaw5Pmb4kzaw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

### 应该给出一个完整的描述性类型名称。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI2dNh10UR0uTdW9VCzTdH3l2yfFbDvPl6OfB3g8fGSZMQarhermbNib7w/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

我猜很多人有这种坏习惯，因为即使是官方文档也使用一个字母的名称。按 T 代替写全名可以更快地键入，并且不需要考虑太多。

泛型类型是变量，就像其他变量一样。当 IDE 开始向我们展示这些技术性时，我们已经放弃了在变量名称中描述变量技术性的想法。例如。我们通常只写 const name = ‘Daniel’ 而不是 const strName = ‘Daniel’。此外，一个字母的变量名通常会引起轰动，因为如果不看它们的声明，可能很难翻译它们的含义。

# 8.非布尔检查

### 通过将值直接传递给 if 语句来检查值是否已定义。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI2ouBaGgJH7PpZRianyIvGYD52ExRicL2icWYUfqRao1g7FzqmLP05Oicz5Q/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

### 我们可以明确检查我们关心的情况。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI2iaWZQBe3WtdGcOyGzlO8ZQO8YFpaD1Cj8JEKzRibO5WF7lnwUcLzSFvA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

用简短的方式编写检查看起来更简洁，并且可以让我们避免思考我们真正想要检查的内容。

也许我们应该考虑一下我们真正想要检查的内容。例如，上面给出的示例以不同的方式处理 countOfNewMessages 为 0 的情况。

# 9. ！！操作符

### 将非布尔值转换为布尔值。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI2JC1Z1ky1n3LrB2buP2b1XErSiamtcfguU28VIvvkD7FtOt4GT59ZmjA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

### 明确检查我们关心的状况。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/H8M5QJDxMHrmLEeZgIovticP5RcoalgI2CGUUz3oNgJo5BKxnJcCxX8HRub7ibFCic3KdPypibHyJeDhmYtiaNiba0yg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)image.png

对我们中的一些人来说，理解`！`很简单。它看起来简短而简洁，如果您已经熟悉它，那么您就会知道它是关于什么的。这是将任何值转换为布尔值的简便方法。尤其是在代码库中，假值（如 `null`、`undefined` 和`“”`）之间没有明确的语义分离。

使用 `!!`通过宣传内部知识来混淆代码的真正含义。这使得新开发人员更不容易访问代码库，无论是一般开发的新手，还是 JavaScript 的新手。引入细微的错误也很容易。来自“非布尔布尔检查”的 `countOfNewMessages` 为 `0` 的问题仍然存在 `!!`。