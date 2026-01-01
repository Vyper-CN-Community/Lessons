# 掌握 Vyper HashMaps 以实现高效数据检索

在智能合约开发中，选择合适的数据结构对于效率和可用性至关重要。虽然列表（数组）对于有序集合很有用，但它们依赖数字索引进行数据访问。当你需要基于特定标识符（如名字）而不是其在序列中的位置来检索信息时，这会变得繁琐。让我们探索 Vyper 的 `HashMap` 如何为此类场景提供更直观的解决方案。

## 基于索引查找的挑战

想象一下存储有关人员的信息，包括他们的名字和喜爱数字。一种常见的初始方法可能涉及使用列表：一个用于名字，另一个用于对应的喜爱数字。

如果我们先添加 "Patrick" 然后添加 "Mariah"，访问 Mariah 的喜爱数字需要知道她的索引（在本例中为 1）。你需要类似 `self.list_of_numbers[1]` 的代码。这带来了一个问题：

1.  **不便**：用户或其他合约需要知道他们想要的特定条目的正确数字索引。
2.  **脆弱性**：如果元素的顺序发生变化（例如，移除了一个项目），索引会发生变化，可能导致数据检索错误。
3.  **类型限制**：列表严格要求整数索引。尝试使用非整数键（如 `self.list_of_people["Mariah"]`）访问数据将导致编译错误。

## 介绍 Vyper HashMaps (映射/Mappings)

Vyper 提供了一种强大的数据结构，称为 **HashMap**，在 Solidity 等其他智能合约语言中通常称为 **mapping (映射)**，或在传统编程中称为字典/哈希表。HashMap 将数据存储为**键值对 (Key-Value Pairs)**，允许你将一条数据（键）直接与另一条数据（值）关联。

### 核心概念

*   HashMap 将特定类型的键映射到另一特定类型的值。
*   声明 HashMap 的语法是：`HashMap[KeyType, ValueType]`。
*   这种结构支持使用键进行直接查找，绕过了对数字索引的需求。

Vyper 中 HashMap 的一个关键特征是它们如何处理尚未显式赋值的键的查找。它们不会报错，而是返回指定 `ValueType` 的**默认值**。常见的默认值包括：
*   数字类型（`uint256`, `int128` 等）为 `0`
*   `bool` 为 `False`
*   `address` 为零地址 (`0x00...00`)
*   `String` 或 `Bytes` 为空字符串或空字节

## 在 Vyper 中实现 HashMaps

让我们修改我们的合约，使用 HashMap 来存储喜爱数字，使用名字作为键。

### 1. 声明 HashMap 状态变量

我们引入一个新的状态变量来保存我们的映射。我们将名字（表示为限制为 100 字节的 `String`）映射到喜爱数字 (`uint256`)。将其设为 public 会自动创建一个 Getter 函数。

```vyper
name_to_favorite_number: public(HashMap[String[100], uint256])
```

### 2. 更新 HashMap

我们需要修改负责添加数据的函数（例如 `add_person`），以便在填充任何现有列表的同时填充这个新的 HashMap。当添加一个新人员时，我们使用他们的名字作为键存储他们的喜爱数字。

```vyper
@external
def add_person(name: String[100], favorite_number: uint256):
    # ... (之前的逻辑，例如添加到列表) ...
    
    # 将该人添加到 hashmap
    # self 指代合约的存储
    self.name_to_favorite_number[name] = favorite_number
```

在这里，`self.name_to_favorite_number[name]` 访问对应于提供的名字的映射槽，`= favorite_number` 分配关联的值。

## HashMap 实战：一个实际例子

让我们追踪与更新后的合约的交互：

1.  **部署**：部署后立即，`name_to_favorite_number` 映射（概念上）是空的。如果你用键 "Mariah" 查询它，它返回 `0`（默认值）。
2.  **添加数据**：用 `name = "Mariah"` 和 `favorite_number = 30` 调用 `add_person`。这执行 `self.name_to_favorite_number["Mariah"] = 30`。
3.  **检索数据**：如果你现在使用键 "Mariah" 查询映射，它将返回 `30`。

**好处**：我们现在可以直接使用 Mariah 的名字作为标识符来检索她的喜爱数字，而无需知道她在任何列表中的位置（索引）。

## 关键要点

*   Vyper 中的 **HashMap** 提供了一种键值存储机制，将唯一键映射到特定值。
*   它允许使用键进行**直接数据检索**，避免了列表中基于索引查找的限制。
*   使用语法 `HashMap[KeyType, ValueType]` 声明 HashMap。
*   使用赋值语法更新值：`self.map_variable[key] = value`。
*   HashMap 中**未设置的键**返回 ValueType 的默认值。
*   `HashMap` 和 `List` 是 Vyper 中的基本数据结构。在它们之间进行选择涉及到考虑访问模式（直接查找 vs 迭代）和 Gas 成本。
