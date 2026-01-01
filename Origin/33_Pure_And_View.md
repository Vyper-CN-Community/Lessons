# Vyper Function Decorators: Pure vs. View Explained

In Vyper, function decorators like `@pure` and `@view` provide important information about a function's behavior, specifically regarding its interaction with the blockchain state. Both decorators signal that a function is read-only, meaning it promises not to alter any data stored permanently on the blockchain. Understanding the difference between them is crucial for writing efficient and secure smart contracts.

## Core Concepts Recap

*   **Read-Only Functions**: Functions that do not modify the blockchain state (no changes to state variables).
*   **State Variables**: Variables declared within a contract whose values persist on the blockchain (e.g., `my_var: public(uint256)`).
*   **Global Variables**: Special variables provided by the EVM (e.g., `block.timestamp`, `msg.sender`).

## The @pure Decorator

Functions marked with `@pure` are the most restrictive type of read-only function.

*   **Restriction**: A pure function **cannot** read contract state variables nor can it read any global variables (like `block.timestamp`).
*   **Operation**: It can only operate on the input arguments passed directly to it and any local variables defined within its scope.
*   **Analogy**: Think of a pure mathematical function. Given the same inputs, it will always produce the same output, regardless of any external state.

### Example: A @pure Function

```vyper
@external
@pure
def add(x: uint256, y: uint256) -> uint256:
    return x + y
```

This function primarily depends solely on `x` and `y`.

## The @view Decorator

Functions marked with `@view` are also read-only, but they have fewer restrictions than pure functions.

*   **Permission**: A view function **can** read contract state variables and global variables.
*   **Restriction**: Crucially, it still **cannot modify** any state variables. It only "views" the state.
*   **Analogy**: Think of "viewing" or inspecting the current condition of the contract without making any changes.

### Example: A @view Function

```vyper
count: public(uint256)

@external
@view
def add_to_count(x: uint256) -> uint256:
    return x + self.count + block.timestamp
```

This function reads `self.count` and `block.timestamp`, so it cannot be `@pure`, but since it doesn't modify state, it is `@view`.

## Illustrating the Boundaries

1.  **State Modification**: If a function changes a state variable (`self.count += 1`), it is **not** read-only. It cannot be `@pure` or `@view`.
2.  **Reading State**: If a function returns `x + self.count`, it reads state. It must be `@view` (or unmarked), not `@pure`.
3.  **Reading Global**: If a function returns `x + block.timestamp`, it reads a global variable. It must be `@view` (or unmarked), not `@pure`.

## Summary: When to Use Which

*   **Use `@pure`**: When your function performs calculations based **only** on its arguments and local variables. No state or global usage.
*   **Use `@view`**: When your function needs to **read** state variables or global variables but guarantees **not to change** any state.
*   **Use neither**: If your function needs to **modify** the contract's state variables (e.g., updating a balance).
