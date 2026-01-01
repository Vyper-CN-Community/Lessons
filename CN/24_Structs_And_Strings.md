# 使用 Vyper 结构体和字符串组织复杂数据

在之前的课程中，我们探索了基本的 Vyper 功能，如存储一个喜爱数字或一个简单的数字列表。让我们重温一个存储喜爱数字列表的合约。虽然这个合约成功地将 `uint256` 值存储在 `list_of_numbers` 数组中，但它有一个局限性：我们只存储了数字本身。我们不知道这些喜爱数字属于谁。

我们如何将名字与每个数字关联起来？Vyper 提供了强大的工具来创建更复杂、自定义的数据结构：**结构体 (Structs)** 和 **字符串 (Strings)**。

## 介绍结构体：创建自定义数据类型

为了将名字与喜爱数字关联，我们需要一种方法将这两条数据捆绑在一起。Vyper 允许我们使用**结构体**定义我们自己的自定义数据类型。

结构体 (Struct，structure 的缩写) 是一种将多个相关变量（称为成员）归组在一个名称下的方式。把它想象成创建一个新类型的蓝图。

### 语法

你使用 `struct` 关键字定义结构体，后跟你想给新类型起的名字（通常使用大写驼峰命名法 `CapitalCase`），然后是一个缩进块，列出成员及其类型。

```vyper
struct Person:
    favorite_number: uint256
    name: String[100]
```

在这个例子中，我们创建了一个名为 `Person` 的新类型。任何声明为 `Person` 类型的变量都将同时持有一个 `uint256` 值的 `favorite_number` 和一个 `String[100]` 值的 `name`。结构体定义通常放置在合约文件的顶部附近。

一旦定义，你就可以像任何其他内置 Vyper 类型（如 `uint256`, `address`）一样，为状态变量、函数参数或局部变量使用 `Person`。

## 处理文本：String 类型

我们的 `Person` 结构体包含一个 `name` 成员。为了存储像名字这样的文本数据，Vyper 使用 **String** 类型。

Vyper 中 String 的一个关键特征是它们具有固定的最大大小。在声明字符串变量时，必须指定此最大大小。

### 语法

`String[MAX_LENGTH]`

在这里，`MAX_LENGTH` 是一个整数，表示字符串可以占用的最大字节数。虽然不是直接的字符计数（由于潜在的多字节字符），但它为你提供了字符串长度的上限。例如，`String[100]` 声明了一个最多可容纳 100 字节文本数据的字符串变量。

### 示例

让我们临时添加一个公共字符串状态变量来看看它是如何工作的：

```vyper
my_name: public(String[100])
```

如果你编译并部署这个修改后的合约，你可以调用公共 Getter `my_name`，它将返回字符串值（如果已赋值）。字符串值使用双引号 (`"Patrick"`) 赋值。

## 存储集合：结构体数组

现在我们有了一个包含名字的自定义 `Person` 类型，我们如何存储这些 Person 对象的列表？我们使用数组，就像我们对 `uint256` 所做的那样，但这次数组的元素将是 `Person` 类型。

### 语法

`variableName: public(StructName[ARRAY_SIZE])`

要存储最多 5 个人的列表，我们可以声明这样一个状态变量：

```vyper
list_of_people: public(Person[5])
```

这创建了一个名为 `list_of_people` 的公共、固定大小数组。它可以容纳 5 个元素，每个元素必须是一个 `Person` 结构体。概念上，你可以想象这个数组持有的数据像：`[(7, "Patrick"), (16, "Chelsea"), (42, "Mark"), ...]`。

## 融会贯通：向列表添加人员

让我们修改 `add_number` 函数以存储 `Person` 结构体，而不仅仅是数字。我们将函数重命名为 `add_person` 并更新其逻辑。

### 修改后的函数

```vyper
@external
def add_person(name: String[100], favorite_number: uint256):
    # 创建一个新的 Person 结构体实例
    new_person: Person = Person(favorite_number=favorite_number, name=name)
    
    # 将其存储在当前索引的数组中
    self.list_of_people[self.index] = new_person
    
    # 递增索引
    self.index = self.index + 1
```

### 解释

1.  **函数签名**：函数现在接受两个参数：`name` (类型 `String[100]`) 和 `favorite_number` (类型 `uint256`)。
2.  **创建结构体实例**：在函数内部，我们声明一个类型为 `Person` 的局部变量 `new_person`。我们使用 `Person(...)` 语法初始化它，根据函数输入为其成员提供值。使用关键字参数 (`favorite_number=...`, `name=...`) 使代码更清晰。
3.  **将结构体存储在数组中**：然后我们将这个 `new_person` 结构体实例赋值给我们的状态变量数组 `list_of_people` 中的一个元素。至关重要的是，我们必须使用 `self.list_of_people` 来引用状态变量数组。忘记 `self.` 会导致编译时错误。
4.  **递增索引**：最后，我们递增 `self.index`，以便下一次调用 `add_person` 时将新数据存储在数组中的下一个可用槽位。

现在，如果你用 "Mark" 和 7 调用 `add_person`，`Person` 结构体 `(7, "Mark")` 将被创建并存储在 `self.list_of_people[0]`。用索引 0 调用公共 Getter `list_of_people` 将以元组形式返回此数据。

## 结论

通过结合结构体、字符串和数组，我们可以在 Vyper 智能合约中构建更复杂和有组织的数据结构。

*   **结构体 (Structs)** 允许我们通过对相关数据进行分组来定义自定义类型。
*   **字符串 (Strings)** (`String[MAX_LENGTH]`) 允许我们存储固定大小的文本数据。
*   **结构体数组** (`StructName[ARRAY_SIZE]`) 允许我们存储自定义数据类型的列表或集合。

这种组合对于在区块链上表示现实世界的实体和关系是基础性的。
