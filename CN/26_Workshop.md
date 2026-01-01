# Vyper 合约交互挑战：实战演练 (Workshop)

本课将进行一个实际挑战，涉及与用 Vyper 语言编写的已部署智能合约进行交互。我们将使用开发环境（类似于 Remix IDE）来调用合约上的函数，修改其状态并验证结果。目标是理解状态更改是如何发生的，以及像映射和结构体这样的数据结构是如何在智能合约中被操作的。

## 理解 FAVORITES 合约

我们正在与一个名为 `FAVORITES` 的已部署 Vyper 合约进行交互。让我们检查其关键组件：

### 状态变量

该合约管理存储在区块链上的几条数据：

*   `my_favorite_number: public(uint256)`：存储一个无符号整数。
*   `list_of_numbers: public(mapping(uint256, uint256))`：一个将索引（键，`uint256`）与喜爱数字（值，`uint256`）关联的映射。把它想象成一个动态字典或哈希映射。
*   `list_of_people: public(mapping(uint256, Person))`：一个将索引（键，`uint256`）与 `Person` 结构体（值）关联的映射。
*   `index: public(uint256)`：一个用于跟踪下一个可用索引的整数，用于向 `list_of_numbers` 和 `list_of_people` 映射添加条目。

### Person 结构体

Vyper 允许定义自定义数据结构。此合约使用 `Person` 结构体：

```vyper
struct Person:
    favorite_number: uint256
    name: String[100]
```

此结构归组了一个人的喜爱数字和他们的名字。

### add_person 函数详解

我们将用于此挑战的主要函数是 `add_person`。它的工作原理如下：

1.  它接受 `name` 和 `favorite_number` 作为输入。
2.  它将 `favorite_number` 存储在 `list_of_numbers` 映射中 `self.index` 的当前值处。
3.  它使用提供的名字和喜爱数字创建一个新的 `Person` 结构体实例。
4.  它将此 `Person` 结构体存储在 `list_of_people` 映射中，同样在 `self.index` 的当前值处。
5.  至关重要的是，它将 `self.index` 增加 1，为下一次调用 `add_person` 做准备。

最初，我们观察到 `list_of_people[0]` 已经包含 `(7, "Mark")`，表明 `add_person` 可能在我们的挑战开始之前被调用过一次，`index` 变量可能持有值 1。

## 挑战：填充映射中的特定索引

**任务：** 你能否与已部署的 FAVORITES 合约交互，使得查询 **索引 3** 的 `list_of_people` 时返回特定的数据元组 `(8, "Wong")`？

目前，查询 `list_of_people[3]` 将返回默认值（可能是 `(0, "")`），因为还没有在该索引处显式存储任何内容。

## 解决挑战：使用 add_person 控制状态

解决此问题的关键在于理解 `add_person` 函数如何利用 `self.index` 状态变量。每次 `add_person` 成功执行（作为一次交易）时，它会执行与其挑战相关的两个操作：

1.  它在**当前**索引处填充 `list_of_people` 映射。
2.  它递增索引。

如果合约以 `index = 1` 开始（因为索引 0 已经用 "Mark" 填充），我们需要 `add_person` 函数在 **`index` 为 3 时**执行，以便将我们想要的数据存储在 `list_of_people[3]`。

为了实现这一目标，我们需要多次调用 `add_person`：

1.  **调用 1（已完成）**：在 `index = 0` 处添加 `(7, "Mark")`。索引变为 1。
2.  **调用 2**：输入任意数据（例如 "Alice", 5）。这在 `index = 1` 处添加数据。索引变为 2。
3.  **调用 3**：输入任意数据（例如 "Bob", 12）。这在 `index = 2` 处添加数据。索引变为 3。
4.  **调用 4**：输入目标数据（name: "Wong", favorite_number: 8）。这在 `index = 3` 处添加 `(8, "Wong")`。索引变为 4。

因此，解决方案涉及将 `add_person` 函数的输入字段设置为 `name: "Wong"` 和 `favorite_number: 8`，然后执行该函数**足够多次**（在这种情况下，从观察到的状态开始可能再执行三次），以确保它在 `index` 为 3 时运行。

## 验证结果

在以索引 3 为目标的调用中使用输入 "Wong" 和 8 执行了所需次数的 `add_person` 函数后，我们可以验证结果。

使用合约交互界面查询公共 `list_of_people` 映射。输入索引 `3`。界面应返回元组 `(uint256: 8, String: "Wong")`，确认状态已根据挑战要求成功修改。

## 关键概念回顾

本练习演示了智能合约开发和交互中的几个基本概念：

*   **状态修改**：更改区块链上存储的数据需要发送执行改变状态函数（如 `add_person`）的交易。
*   **映射 (Mappings)**：Vyper 映射提供了一种灵活的方式来存储键值数据。它们没有固定大小，但允许通过其键访问值。
*   **结构体 (Structs)**：自定义数据类型 (`Person`) 允许对相关信息进行分组。
*   **索引管理**：使用专用状态变量 (`index`) 来控制插入位置，模拟类似数组的顺序添加。
*   **Gas 成本**：实时区块链网络上的每一个改变状态的交易都会消耗 Gas。
