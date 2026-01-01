# 理解 Vyper 中的可见性：函数

智能合约编程中的**可见性**规定了合约的哪些部分（状态变量和函数）可以被访问以及从哪里被访问。Vyper，作为一种用于以太坊虚拟机 (EVM) 的 Python 风格语言，使用特定的关键字和装饰器来管理合约安全和设计这一至关重要的方面。理解可见性可以确保函数仅在预期时被调用，并且数据被适当地暴露。

让我们使用一个简单的示例合约 `favorites.vy` 来探索可见性：

```vyper
# favorites.vy
# version 0.4.0

my_favorite_number: public(uint256)

@external
def store(new_number: uint256):
    self.my_favorite_number = new_number
```

在这个初始状态下：

1.  `my_favorite_number: public(uint256)`：这声明了一个名为 `my_favorite_number` 的状态变量。`public` 关键字在这里是关键。它自动指示 Vyper 编译器生成一个同名的 Getter 函数 (`my_favorite_number()`)。这允许此合约之外的任何人（用户或其他合约）通过调用此自动生成的函数来读取 `my_favorite_number` 的当前值。该变量也可以从合约的函数内部访问和修改（使用 `self.my_favorite_number`）。

2.  `@external def store(...)`：这定义了一个名为 `store` 的函数。`@external` 装饰器指定了其可见性。标记为 `@external` 的函数只能从合约**外部**调用。这意味着外部拥有账户 (EOA) 或另一个智能合约可以触发此函数，但**不能**使用 `self` 关键字从同一合约内的另一个函数调用它（例如，`self.store(...)` 将是无效的）。

## 隐式可见性默认值

如果你省略显式的可见性标记，Vyper 有合理的默认值：

1.  **状态变量**：如果你从状态变量声明中删除了 `public` 关键字（例如，`my_favorite_number: uint256`），Vyper **不会**创建自动 Getter 函数。该变量对于合约逻辑（通过 `self`）仍然是内部可访问的，但不能直接通过外部世界读取。其可见性实际上变成了 **internal**。

2.  **函数**：如果你没有向函数定义添加可见性装饰器（`@external` 或 `@internal`），Vyper 隐式默认为 `@internal`。

## 内部可见性：@internal

让我们修改 `store` 函数以显式使用（或演示默认的）`@internal` 可见性。想象一下，为了这个特定场景，我们还从状态变量中删除了 `public`：

```vyper
# favorites.vy
# version 0.4.0

my_favorite_number: uint256 # 现在是 Internal

@internal
def store(new_number: uint256):
    self.my_favorite_number = new_number
```

`@internal` 装饰器表示此函数**只能**从同一个合约内部调用。`favorites.vy` 中定义的其他函数可以使用 `self.store(...)` 调用它。但是，它**从合约外部完全不可访问**。如果你部署此版本，你会发现：
*   没有外部接口（如 Remix 中的按钮）可以直接调用 `store` 函数。
*   没有外部接口读取 `my_favorite_number`（因为删除了 `public`）。

## 可见性类型之间的交互

理解具有不同可见性级别的函数如何交互至关重要：

### External 调用 Internal（允许）

一个 `@external` 函数可以使用 `self` 调用一个 `@internal` 函数。这是组织代码的一种常见模式，即外部入口点使用内部辅助函数。

```vyper
@internal
def _update_number(new_number: uint256): # 注意：通常以 _ 为前缀
    self.my_favorite_number = new_number

@external
def set_favorite_to_seven():
    # 有效：外部函数调用内部函数
    self._update_number(7)
```

### Internal 调用 External（通过 self 禁止）

一个 `@internal` 函数**不能**使用 `self` 调用一个 `@external` 函数。外部函数被设计为从合约外部进入的入口点，而不是通过 `self` 启动的内部控制流。尝试这样做会导致编译错误。

```vyper
@internal
def try_invalid_call():
    # 无效：不能通过 self 调用外部函数
    self.set_favorite_to_seven() # 假设 set_favorite_to_seven 是 @external

@external
def set_favorite_to_seven():
    self.my_favorite_number = 7
```

编译内部函数通过 `self` 调用外部函数的代码将导致 `CallViolation` 错误，明确指出：`CallViolation: Cannot call external functions via 'self' or via library`。这强制执行了外部接口和内部逻辑的预期分离。

## Vyper 可见性关键要点

*   **状态变量**：使用 `public` 自动创建外部 Getter 函数。没有 `public`，变量仅在内部可访问。
*   **@external 函数**：只能从合约**外部**调用。不能从合约内部使用 `self` 调用。
*   **@internal 函数**：只能从合约**内部**使用 `self` 调用。如果未指定装饰器，这是函数的默认可见性。
*   **self 关键字**：用于内部调用。它可以调用 `@internal` 函数，但不能调用 `@external` 函数。
*   **目的**：可见性控制访问，增强安全性并强制执行清晰的合约交互模式。选择正确的可见性对于健壮且安全的智能合约开发至关重要。
