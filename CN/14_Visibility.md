# Vyper 状态变量：可见性与 Remix VM 部署

欢迎学习这节关于理解 Vyper 状态变量可见性以及将第一个简单合约部署到 Remix VM 的课程。我们将探索 Vyper 如何处理合约内的数据存储，以及如何利用 Remix IDE 的模拟环境进行动手实践来控制其可访问性。

## 理解状态变量

在 Vyper 中，在任何函数之外、合约顶层声明的变量称为**状态变量**（或存储变量）。这些变量代表智能合约的持久状态；它们的值直接存储在区块链上，并在交易之间保持不变。

在 Vyper 中声明状态变量使用的语法如果你使用过 Python 会看起来很熟悉：

```vyper
variable_name: type
```

例如，要声明一个旨在保存无符号 256 位整数的状态变量，你会写：

```vyper
my_favorite_number: uint256
```

这种语法与 Python 的类型提示完全相同，这在你学习 Vyper 时巩固了你的 Python 知识。

## 状态变量的默认初始化

一个重要的概念是，如果你在声明时或在构造函数（稍后介绍）中没有显式赋值，状态变量通过**默认值**自动初始化。对于像 `uint256` 这样的数字类型，默认值是 `0`。对于 `bool`，是 `False`；对于 `address`，是零地址 (`0x00...00`)。我们稍后会验证这个默认值。

```vyper
my_favorite_number: uint256
# 默认值: 0
```

## 控制访问：变量可见性

可见性关键字决定了状态变量可以从哪里被访问。Vyper 有不同的可见性级别，但对于状态变量，我们将重点关注隐式和显式使用的两个主要级别：

### 1. internal (内部 - 默认)

如果你声明一个没有特定可见性关键字的状态变量，比如上面的 `my_favorite_number: uint256` 示例，它默认为 **internal**。这意味着该变量只能从合约本身内部或继承它的合约中访问。外部账户或其他独立合约无法在没有专门为此创建的函数的情况下直接读取其值。

```vyper
# 默认为 Internal
my_favorite_number: uint256
```

### 2. public (公开)

要使状态变量可以从合约外部读取，你需要将其声明为 **public**。你可以通过用 `public()` 包裹变量类型来实现：

```vyper
my_favorite_number: public(uint256)
```

当你将状态变量标记为 public 时，Vyper 编译器会自动为其生成一个 **Getter 函数**。该函数与变量同名，允许任何人（外部账户、其他合约、用户界面）调用它并检索变量的当前值。

## 部署到 Remix VM

为了看到这些概念的实际效果，我们将使用 Remix IDE，这是一个用于开发智能合约的基于 Web 的环境。具体来说，我们将把合约部署到 **Remix VM (JavaScript VM)**。

Remix VM 是一个完全在浏览器 JavaScript 引擎中运行的模拟区块链环境。把它想象成一个完美的“假区块链”，用于快速测试和开发。它有几个学习优势：
*   无需设置或连接到真实区块链网络（如测试网或主网）。
*   它附带了预先注资的虚拟账户。
*   部署合约是瞬间完成的。
*   你可以轻松删除已部署的合约（不像在真实的不可变区块链上）。

## 实践演示：Internal 与 Public

### 第一步：编写初始合约 (Internal 可见性)

在 Remix 中创建一个名为 `favorites.vy` 的新文件，并添加以下代码：

```vyper
# favorites.vy
# version 0.4.0

my_favorite_number: uint256 # 默认为 Internal
```

### 第二步：编译合约

1.  转到 Remix 中的 "Vyper Compiler" 选项卡（插件可能需要激活）。
2.  确保选择了兼容的编译器版本（例如 0.4.0 或类似版本）。
3.  点击 "Compile favorites.vy"。

### 第三步：部署到 Remix VM (Internal 变量)

1.  转到 "Deploy & Run Transactions" 选项卡。
2.  在 "ENVIRONMENT" 下拉菜单中，选择其中一个 "Remix VM" 选项（例如 "Remix VM (Cancun)"）。
3.  确保在 "CONTRACT" 下拉菜单中选中了你的合约 (`favorites - favorites.vy`)。
4.  点击橙色的 "Deploy" 按钮。
5.  向下滚动到 "Deployed Contracts" 部分。你会看到你的 favorites 合约的一个实例。点击小箭头展开它。

**观察 (Internal)**：注意，在已部署的合约实例下，**没有按钮或直接方式**来读取 `my_favorite_number` 的值。你可能只能看到 "Low level interactions" 选项。这是因为变量默认为 internal，且没有自动创建 Getter 函数。

### 第四步：修改为 Public 可见性

现在，让我们更改可见性。修改 `favorites.vy` 代码：

```vyper
# favorites.vy
# version 0.4.0

my_favorite_number: public(uint256) # 现在是 public!
```

### 第五步：重新编译和重新部署

1.  回到 "Vyper Compiler" 选项卡并再次点击 "Compile favorites.vy"。
2.  返回 "Deploy & Run Transactions" 选项卡。
3.  **提示**：最好先删除旧的合约实例。点击 "Deployed Contracts" 下先前部署合约旁边的 'x' 按钮（这仅适用于 Remix VM）。
4.  再次点击橙色的 "Deploy" 按钮。
5.  展开新部署的合约实例。

**观察 (Public)**：这一次，你会在已部署合约下看到一个标记为 `my_favorite_number` 的**蓝色按钮**。这个按钮代表了自动生成的 Getter 函数，因为我们将变量声明为了 public。

### 第六步：读取值

点击蓝色的 `my_favorite_number` 按钮。Remix 将在按钮下方显示返回值，看起来像 `0: uint256: 0`。这证实了两件事：
*   我们 `uint256` 状态变量的默认值确实是 `0`。
*   `public` 关键字通过自动生成的 Getter 函数使变量的值可以从 Remix 界面轻松访问。

## 关键要点

*   **状态变量**：在区块链上存储持久数据，在合约顶层声明。
*   **Vyper 语法**：`variable_name: type` 镜像了 Python 类型提示。
*   **默认值**：状态变量有默认值（`uint256` 为 0）。
*   **默认可见性**：状态变量默认为 `internal`，仅在合约内部可访问。
*   **Public 可见性**：使用 `public(type)` 使状态变量外部可读。这通过自动创建 Getter 函数实现。
*   **Remix VM**：一个非常宝贵的工具，用于在模拟的浏览器环境中快速部署和测试合约。记住，部署在这里的合约很容易删除，不像在真实的不可变区块链上。
*   **Getter 函数**：提供读取公共变量状态的标准方式。虽然从技术上讲，公共区块链上的所有数据在底层都是潜在可读的（通过检查存储槽），但公共 Getter 提供了方便的、基于函数的访问。

你现在已经理解了如何声明状态变量，如何使用 internal（默认）和 public 控制其可见性，以及如何使用 Remix VM 部署简单的合约并与之交互以直接观察这些概念。
