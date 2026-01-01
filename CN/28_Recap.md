# 回顾：构建 Favorites Vyper 智能合约

在我们要采取激动人心的步骤——将我们的 `favorites.vy` 智能合约部署到实时区块链网络之前，让我们回顾一下目前为止我们实现的 Vyper 基本概念和功能。该合约演示了在链上存储和管理数据的基本构建块。

## favorites.vy 的目标

`favorites.vy` 合约的主要目标有两点：
1.  存储合约部署者的喜爱数字。
2.  存储其他人的喜爱数字，将他们的名字与其选择的数字关联起来。

为了实现这一目标，我们利用了几个核心 Vyper 功能：

## 已实现的核心概念

### 结构体：定义自定义数据类型

Vyper 允许我们使用 `struct` 关键字创建自定义数据结构。这有助于逻辑地组织相关数据。在我们的合约中，我们定义了一个 `Person` 结构体：

```vyper
struct Person:
    favorite_number: uint256
    name: String[100]
```

这创建了一个名为 `Person` 的新类型，它捆绑了一个无符号 256 位整数 (`favorite_number`) 和一个最大长度为 100 个字符的字符串 (`name`)。

### 状态变量：持久的链上存储

状态变量保存持久存在于区块链上的数据，定义了合约的当前状态。我们声明了几个状态变量，使用 `public()` 可见性包装器，它会自动生成用于轻松检索数据的 Getter 函数：

```vyper
my_favorite_number: public(uint256)
list_of_numbers: public(uint256[5])
list_of_people: public(Person[5])
name_to_favorite_number: public(HashMap[String[100], uint256])
index: public(uint256)
```

这些变量存储部署者的详细信息、数字列表和 `Person` 结构体列表、从名字到喜爱数字的映射，以及一个在向数组添加元素时跟踪当前位置的索引变量。

### 构造函数：初始化合约状态

构造函数是一个标记为 `@deploy` 装饰器并命名为 `__init__` 的特殊函数。它仅在合约首次部署到区块链时执行一次。其主要作用是设置状态变量的初始值。

```vyper
@deploy
def __init__():
    self.my_favorite_number = 7
    self.index = 0
```

### 函数：定义合约交互

函数定义了用户和其他合约如何与我们的智能合约交互。我们使用 `@external` 装饰器使函数可从合约外部调用。

*   **简单的状态修改 (`store`)**：此函数允许更新部署者的喜爱数字。它修改合约的状态。

    ```vyper
    @external
    def store(new_number: uint256):
        self.my_favorite_number = new_number
    ```

*   **只读数据检索 (`retrieve`)**：此函数返回部署者的喜爱数字。至关重要的是，它使用了 `@view` 装饰器，表示它不修改合约的状态。

    ```vyper
    @external
    @view
    def retrieve() -> uint256:
        return self.my_favorite_number
    ```

*   **复杂逻辑 (`add_person`)**：此函数演示了更复杂的交互，涉及多个状态变量。它接受名字和喜爱数字作为输入并执行多个操作：

    ```vyper
    @external
    def add_person(name: String[100], favorite_number: uint256):
        # 1. 将数字存储在简单列表中
        self.list_of_numbers[self.index] = favorite_number
        
        # 2. 在内存中创建一个 Person 结构体实例
        new_person: Person = Person(favorite_number=favorite_number, name=name)
        
        # 3. 将 Person 结构体存储在 people 列表中
        self.list_of_people[self.index] = new_person
        
        # 4. 存储 name-to-number 映射
        self.name_to_favorite_number[name] = favorite_number
        
        # 5. 为下一次添加递增索引
        self.index = self.index + 1
    ```

## 关键关系

注意这些概念是如何相互联系的：
*   `Person` 结构体是存储在 `list_of_people` 状态变量数组中的数据类型。
*   `index` 状态变量管理向 `list_of_numbers` 和 `list_of_people` 的插入。
*   `add_person` 函数编排跨数组和映射的更新，利用 `Person` 结构体并修改 `index`。
*   `__init__` 构造函数为其他函数使用的变量提供初始基线状态。

有了这个坚实的基础回顾，我们现在准备将 `favorites.vy` 合约部署到真实的区块链网络上。
