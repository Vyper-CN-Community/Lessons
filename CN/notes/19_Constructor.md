# 初始化智能合约：理解 Vyper 构造函数 (__init__)

部署 Vyper 智能合约时，你经常需要设置其初始状态。这可能涉及定义所有权、设置配置参数或建立关键变量的初始值。这个关键的初始化步骤通过一个称为**构造函数 (constructor)** 的特殊函数来处理。

在 Vyper 中，构造函数遵循源自 Python 的特定约定。它是一个名为 `__init__`（双下划线 init 双下划线）的函数，必须使用 `@deploy` 装饰器进行装饰。此装饰器向 Vyper 编译器发出信号，表明 `__init__` 函数包​​含**仅在**合约部署到区块链上的那一刻执行一次的逻辑。部署后，构造函数无法再次被调用。

```vyper
@deploy
def __init__():
    # 初始化逻辑在这里
    pass
```

## 设置初始状态：所有者模式 (The Owner Pattern)

构造函数最常见和最关键的用途之一是确立合约的**所有者**。通常，所有者是发起部署交易的账户地址。Vyper 提供了一个全局变量 `msg.sender`，它保存了交易发起者的地址。在部署期间，`msg.sender` 是部署者的地址。

要实现此模式，你首先声明一个状态变量（通常为公共的以便于验证）来存储所有者的地址。然后，在 `__init__` 函数内部，使用 `self` 关键字（指代合约的存储）将 `msg.sender` 赋值给此状态变量。

```vyper
owner: public(address)

@deploy
def __init__():
    self.owner = msg.sender
```

当此合约被部署时，`__init__` 函数会自动执行，将 `owner` 变量设置为部署合约的地址。

## 带参数的构造函数用于自定义初始化

构造函数不仅限于使用像 `msg.sender` 这样的全局变量。它们还可以接受**参数**，允许你在部署过程中传入自定义值。这对于创建灵活和可配置的合约至关重要。

这些参数在 `__init__` 函数定义的括号内定义，就像任何普通函数一样。部署工具（如 Remix）通常会检测这些参数，并提供输入字段供你在部署合约时提供所需的值。

让我们扩展之前的示例，接受一个名称（作为字符串）和一个持续时间（作为整数）来计算过期时间戳。

```vyper
owner: public(address)
name: public(String[100])
expiresAt: public(uint256)

@deploy
def __init__(name: String[100], duration: uint256):
    self.owner = msg.sender
    self.name = name
    self.expiresAt = block.timestamp + duration
```

在这个例子中：
1.  `__init__` 函数现在接受两个参数：类型为 `String[100]` 的 `name` 和类型为 `uint256` 的 `duration`。
2.  部署时，你需要为 `name` 和 `duration` 提供值。
3.  在构造函数内部，`self.owner` 像之前一样设置。
4.  `self.name` 使用部署期间传入的名称值进行初始化。
5.  `self.expiresAt` 通过获取发生部署的区块的时间戳 (`block.timestamp`) 并加上部署期间提供的 `duration` 值来计算。

在使用例如 `name="Vyper"` 和 `duration=10` 部署此合约后，你可以查询公共状态变量并验证 `owner` 是部署者地址，`name` 是 "Vyper"，而 `expiresAt` 是部署 `block.timestamp` 加上 10 秒。

## 实际应用：初始化喜爱数字

即使对于简单的初始化任务，构造函数也是有用的。考虑一个旨在存储喜爱数字的合约，如果未显式设置，默认为 0。如果你希望此数字在部署后立即从 7 开始，你可以使用无参数构造函数。

```vyper
my_favorite_number: public(uint256)

@deploy
def __init__():
    self.my_favorite_number = 7
```

在添加构造函数之前，部署后立即查询 `my_favorite_number` 将返回 0。添加此 `__init__` 函数后，部署合约不仅确保 `my_favorite_number` 立即设置为 7，还演示了构造函数在覆盖默认零初始化中的作用。

## 关键要点

*   **目的**：构造函数 (`__init__`) 在合约部署时初始化其状态变量。
*   **执行**：它们自动运行，且在部署交易期间仅运行一次。
*   **语法**：定义为 `def __init__(...)` 且必须具有 `@deploy` 装饰器。
*   **状态访问**：使用 `self` 关键字访问和修改构造函数内的状态变量（例如，`self.owner = msg.sender`）。
*   **参数**：构造函数可以接受参数，以允许基于部署时提供的值进行自定义初始化。
*   **全局变量**：构造函数内部常用的全局变量包括 `msg.sender`（部署者地址）和 `block.timestamp`（部署时间）。
*   **默认值**：如果状态变量未在构造函数中显式设置，它将保留其默认的零值（0、空字符串、零地址等）。
