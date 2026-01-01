# Mastering Conditional Logic in Vyper: Using if, elif, and else

Conditional statements are fundamental building blocks in programming, allowing your smart contracts to make decisions and execute different code paths based on specific criteria. In Vyper, the language used for writing secure and efficient Ethereum smart contracts, conditional logic is implemented using `if`, `elif`, and `else` statements, much like in Python. This lesson will guide you through understanding and implementing these crucial control flow structures.

## Setting Up the Contract

Before diving into conditionals, let's establish the basic structure of our Vyper contract file (e.g., `ifElse.vy`):

```vyper
# Pragma Directive
pragma version ^0.4.0
```

This line tells the compiler that the code is written for Vyper version 0.4.0 or any compatible later version within the 0.4.x series.

## Implementing Conditional Statements

We can implement a function `if_else` to demonstrate conditional logic.

```vyper
@external
@pure
def if_else(x: uint256) -> uint256:
    if x <= 10:
        return 1
    elif x <= 20:
        return 2
    else:
        return 0
```

### Breakdown of the Logic

1.  **The `if` Statement**:
    *   `if x <= 10:`
    *   The code checks if the input value `x` is less than or equal to 10.
    *   **Action**: If true, it returns `1`. The function stops here.

2.  **The `elif` Statement (Else If)**:
    *   `elif x <= 20:`
    *   If the first condition was false (meaning `x` is greater than 10), Vyper checks this condition.
    *   **Action**: If `x` is less than or equal to 20 (but > 10), it returns `2`.

3.  **The `else` Statement**:
    *   `else:`
    *   If both previous conditions were false (meaning `x` is greater than 20).
    *   **Action**: It executes the code in this block, returning `0`.

## Code Refinement: Adding the @pure Decorator

Observing our `if_else` function, we notice it doesn't read from or write to the contract's storage or interact with the blockchain environment (like checking block numbers or balances). It operates solely on its input arguments. In such cases, we can add the `@pure` decorator alongside `@external`.

*   `@pure`: This decorator signifies that the function promises not to read or modify any state. This is a stricter guarantee than `@view` (which allows reading state) and can enable optimizations and clearer understanding of the function's behavior.

## Testing in Remix IDE

To verify our logic, we can use the Remix IDE:

1.  **Compile**: Navigate to the "Vyper Compiler" tab, select `ifElse.vy`, and click "Compile".
2.  **Deploy**: Go to the "Deploy & Run Transactions" tab and deploy the contract.
3.  **Interact**:
    *   **Test 1**: Enter `1`. Call. Output should be `1`.
    *   **Test 2**: Enter `20`. Call. Output should be `2`.
    *   **Test 3**: Enter `100`. Call. Output should be `0`.

This confirms that the control flow is working as expected.
