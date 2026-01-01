# 在 Vyper 中使用固定大小列表 (Arrays/Lists)

本课探讨如何在你的智能合约中使用固定大小数组，Vyper 将其称为**列表 (Lists)**。我们将涵盖声明、初始化、读取和写入这些列表，重点强调像索引和边界检查这样的重要注意事项。我们将建立在一个基本的 `favorites.vy` 合约之上。

想象你有一个像这样的简单合约：

```vyper
# favorites.vy
# ... 基本设置 ...
```

现在，让我们增强这个合约，使用固定大小列表来存储不仅仅是一个，而是一个喜爱数字的**集合**。

## 声明和初始化固定大小列表

要存储多个数字，我们可以将状态变量声明为列表。语法指定元素类型，后跟方括号中的固定大小。

让我们添加一个设计用于容纳 5 个无符号 256 位整数 (`uint256`) 的列表：

```vyper
list_of_numbers: public(uint256[5])
```

当你像这样声明一个状态变量时，Vyper 自动将其所有元素初始化为其类型的默认值。对于 `uint256`，默认值是 0。因此，在部署之后，`list_of_numbers` 概念上持有 `[0, 0, 0, 0, 0]`。

## 公共访问和读取列表元素

为了允许外部用户或合约从我们的列表中读取值，我们将其标记为 `public`。Vyper 将自动为其生成一个 Getter 函数。

在编译和部署此更新后的合约（例如，使用 Remix IDE）后，你会注意到对应于 `list_of_numbers` 的新接口元素。这个公共 Getter 函数需要一个参数：你想要检索的元素的**索引**。

### 从零开始的索引

至关重要的是要记住，Vyper 列表，像许多编程语言中的数组一样，使用**从零开始的索引 (Zero-Based Indexing)**。这意味着：
*   第一个元素在索引 0。
*   第二个元素在索引 1。
*   ...
*   对于我们大小为 5 的列表，最后一个元素在索引 4。

如果你用索引 0 调用生成的 `list_of_numbers` Getter，它将返回第一个元素（初始为 0）。用 4 调用它将返回第五个元素（也是初始为 0）。

### 越界读取

如果你尝试访问有效范围之外的索引会发生什么？对于我们大小为 5 的列表，有效索引是 0, 1, 2, 3, 和 4。如果你尝试读取索引 5（或任何大于 4 或小于 0 的索引），交易将失败并**回退 (revert)**。这是 Vyper 内置的安全机制，用于防止无效内存访问。

## 写入列表元素

自动生成的公共 Getter 仅允许读取。要修改列表，我们需要创建自己的函数。让我们设计一个 `add_number` 函数来向列表中存储新数字。

由于此函数将修改合约的状态（`list_of_numbers`），它必须标记为 `@external`（允许从外部调用）且不能标记为 `@view`。

### 访问状态变量

记住，函数需要使用 `self.` 前缀来访问状态变量。

我们的第一次尝试可能看起来像这样，试图将新数字放在列表的开头：

```vyper
@external
def add_number(favorite_number: uint256):
    self.list_of_numbers[0] = favorite_number
```

如果你部署这个并调用 `add_number(777)`，列表将变为 `[777, 0, 0, 0, 0]`。然而，随后对 `add_number` 的每一次调用都只会覆盖索引 0 处的值。

## 顺序添加数字

为了将数字一个接一个地添加到可用槽位中，我们需要跟踪下一个写入的索引。我们可以为此引入另一个状态变量：

```vyper
index: public(uint256)
```

当合约部署时，我们应该在 `__init__`（构造函数）中将此索引跟踪器初始化为 0：

```vyper
@deploy
def __init__():
    self.index = 0
```

现在，我们修改 `add_number` 以使用 `self.index` 确定新数字的位置，然后递增 `self.index`，以便下一次调用使用后续位置：

```vyper
@external
def add_number(favorite_number: uint256):
    self.list_of_numbers[self.index] = favorite_number
    self.index = self.index + 1
```

### 让我们追踪执行过程：
1.  **部署**：`list_of_numbers` 是 `[0, 0, 0, 0, 0]`，`index` 是 0。
2.  **调用** `add_number(7)`：
    *   `self.list_of_numbers[0]` 变为 7。（`list_of_numbers` 现在是 `[7, 0, 0, 0, 0]`）
    *   `self.index` 变为 1。
3.  **调用** `add_number(12)`：
    *   `self.list_of_numbers[1]` 变为 12。（`list_of_numbers` 现在是 `[7, 12, 0, 0, 0]`）
    *   `self.index` 变为 2。
4.  **(调用继续...)**
5.  **5 次调用后**：`list_of_numbers` 可能是 `[7, 12, 5, 99, 42]`，`self.index` 将是 5。列表已满。

### 越界写入

如果我们第六次调用 `add_number` 会发生什么？
*   函数尝试执行 `self.list_of_numbers[self.index] = favorite_number`。
*   此时，`self.index` 是 5。
*   代码试图写入 `self.list_of_numbers[5]`。
*   由于大小为 5 的列表的最大有效索引是 4，索引 5 是**越界**的。
*   就像越界读取一样，这会导致交易**回退 (revert)**。在该失败交易中尝试的任何状态更改（如递增索引，虽然这里发生在回退点之后）都将被撤消。
