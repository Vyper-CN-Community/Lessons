# Vyper 函数：使用 @view 读取 vs 修改状态

在智能合约开发中，函数通常需要返回信息或更改合约的数据。Vyper 提供了特定的方式来处理函数，具体取决于它们是仅读取数据还是实际修改区块链的状态。理解这种区别对于效率、安全性和 Gas 成本至关重要。

## 从函数返回值

Vyper 函数可以向调用者返回值。要做到这一点，你在函数签名后使用箭头 `->` 指定返回类型，后跟数据类型。在函数内部，`return` 关键字将值发送回去。

```vyper
@external
def my_func() -> uint256:
    return 123
```

## 状态变量和公共 Getters

在合约级别（任何函数之外）声明的变量称为状态变量或存储变量。它们在区块链上持久存​​储数据。

```vyper
my_favorite_number: public(uint256)
```

在这里，`my_favorite_number` 是一个状态变量。`public` 关键字很特殊：它自动创建一个同名的“Getter”函数（在本例中为 `my_favorite_number()`）。此 Getter 允许合约外的任何人读取变量的当前值。

重要的是，当你从区块链外部（例如，使用像 Remix 这样的工具或 Web 应用程序）调用此自动生成的 Getter 函数时，它被视为一次**调用 (Call)**。调用是**只读**操作，不会花费调用者 Gas，因为它们不修改区块链状态。Remix 通常用**蓝色按钮**表示这些可调用函数。

## @external 装饰器与默认行为

`@external` 装饰器将函数标记为可从合约外部调用。考虑这个修改状态的函数：

```vyper
@external
def store(new_number: uint256):
    self.my_favorite_number = new_number
```

因为这个 `store` 函数改变了 `my_favorite_number` 的值，与它交互需要一次**交易 (Transaction)**。交易修改区块链状态，必须被挖掘，消耗 Gas，并产生交易哈希。Remix 通常用**橙色按钮**显示像这样假定会修改状态的函数。

现在，让我们看一个通过设计仅用于读取状态的函数：

```vyper
@external
def retrieve() -> uint256:
    return self.my_favorite_number
```

此函数读取 `self.my_favorite_number` 并返回它。然而，因为它只被标记为 `@external` 而没有明确声明为只读，Vyper 和相关工具通常**默认将其视为潜在的交易**。如果从 Remix 等外部源调用，点击其按钮（可能是橙色）将启动交易并向调用者收取不必要的 Gas，即使没有状态被改变。

## 使用 @view 装饰器显式读取状态

为了正确地表明函数仅读取状态且不应修改它，请在 `@external` 之外添加 `@view` 装饰器。

```vyper
@external
@view
def retrieve() -> uint256:
    return self.my_favorite_number
```

添加 `@view` 明确告诉 Vyper 编译器和外部工具，此函数**承诺不更改任何区块链状态**。它可以读取状态变量并调用其他 `@view` 或 `@pure` 函数，但不能写入状态。

**效果**：当从区块链外部（链下）调用时，`@view` 函数被视为**调用 (Call)**。像公共 Getter 一样，它对调用者是**免费**的（无 Gas 成本），因为它只从用户连接的区块链节点读取现有数据。Remix 通常用**蓝色按钮**表示这些函数。

## 交易 vs 调用：总结

### 交易 (Transactions) - Remix 中的橙色按钮
*   修改区块链状态（例如，写入存储变量）。
*   需要 Gas。
*   必须被挖掘并包含在区块中。
*   拥有交易哈希。

### 调用 (Calls) - Remix 中的蓝色按钮
*   读取区块链状态而不修改它。
*   当链下发起时（例如，通过用户界面）：
    *   不花费调用者 Gas。
    *   不需要被挖掘。
    *   读取本身不关联交易哈希。
*   适用于公共 Getters 和 `@view` 函数。

## Gas 成本：@view 的细微差别

虽虽然链下调用 `@view` 函数对调用者是免费的，但这并不总是如此。

1.  **链下调用 (Off-Chain Calls)**：直接从区块链外部向 `@view` 函数或公共 Getter 发起的调用（例如，用户点击 DApp 中的按钮）对用户是免费的。
2.  **链上调用 (On-Chain Calls)**：如果一个交易（例如，我们的 `store` 函数）在执行期间调用了一个 `@view` 函数，该调用**确实消耗 Gas**。以太坊虚拟机 (EVM) 仍然需要执行 `@view` 函数中定义的计算和读取操作，这些操作会促成发起调用的交易的总 Gas 成本。

考虑这个例子：

```vyper
@external
def store(new_number: uint256):
    self.my_favorite_number = new_number
    # 在此交易期间调用 view 函数会消耗 Gas
    current_val: uint256 = self.retrieve() 
```

在这里执行 `store` 函数将比没有 `self.retrieve()` 调用的版本花费更多的 Gas，这表明当作为链上交易的一部分执行时，`@view` 函数会消耗 Gas。

## 最佳实践

*   始终为仅用于读取区块链状态而不修改它的 `@external` 函数添加 `@view` 装饰器。
*   这确保了它们在链下交互时表现为免费调用，为用户节省 Gas。
*   它还清楚地向其他开发者和审计员传达了函数的意图。
*   记住区别：`@view` 函数**仅在**链下调用时对调用者免费。
