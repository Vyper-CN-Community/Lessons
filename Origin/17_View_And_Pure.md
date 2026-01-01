# Vyper Functions: Reading vs. Modifying State with @view

In smart contract development, functions often need to return information or change the contract's data. Vyper provides specific ways to handle functions based on whether they only read data or actually modify the blockchain's state. Understanding this distinction is crucial for efficiency, security, and gas costs.

## Returning Values from Functions
Vyper functions can return values to the caller. To do this, you specify the return type after the function signature using an arrow `->` followed by the data type. Inside the function, the `return` keyword sends the value back.

```vyper
@external
def my_func() -> uint256:
    return 123
```

## State Variables and Public Getters
Variables declared at the contract level (outside any function) are called state variables or storage variables. They store data persistently on the blockchain.

```vyper
my_favorite_number: public(uint256)
```

Here, `my_favorite_number` is a state variable. The `public` keyword is special: it automatically creates a "getter" function with the same name (`my_favorite_number()` in this case). This getter allows anyone outside the contract to read the variable's current value.

Importantly, when you call this auto-generated getter function from outside the blockchain (e.g., using a tool like Remix or a web application), it's treated as a **call**. Calls are read-only operations that do not cost the caller gas because they don't modify the blockchain state. Remix often represents these callable functions with blue buttons.

## The @external Decorator and Default Behavior
The `@external` decorator marks a function as callable from outside the contract. Consider this function which modifies state:

```vyper
@external
def store(new_number: uint256):
    self.my_favorite_number = new_number
```

Because this `store` function changes the value of `my_favorite_number`, interacting with it requires a **transaction**. Transactions modify the blockchain state, must be mined, cost gas, and result in a transaction hash. Remix typically shows functions like this, which are assumed to modify state, with orange buttons.

Now, let's look at a function intended only to read state:

```vyper
@external
def retrieve() -> uint256:
    return self.my_favorite_number
```

This function reads `self.my_favorite_number` and returns it. However, because it's only marked `@external` and not explicitly declared as read-only, Vyper and associated tools often default to treating it as a potential transaction. If called from an external source like Remix, clicking its button (likely orange) would initiate a transaction and unnecessarily cost the caller gas, even though no state is being changed.

## Explicitly Reading State with the @view Decorator
To correctly signal that a function only reads state and should not modify it, use the `@view` decorator in addition to `@external`.

```vyper
@external
@view
def retrieve() -> uint256:
    return self.my_favorite_number
```

Adding `@view` explicitly tells the Vyper compiler and external tools that this function promises not to change any blockchain state. It can read state variables and call other `@view` or `@pure` functions, but it cannot write to state.

**Effect:** When called from outside the blockchain (off-chain), a `@view` function is treated as a **call**. Like the public getter, it is free for the caller (no gas cost) because it only reads existing data from the blockchain node the user is connected to. Remix typically represents these functions with blue buttons.

## Transactions vs. Calls: A Summary

### Transactions (Orange Buttons in Remix)
- Modify blockchain state (e.g., writing to storage variables).
- Require gas.
- Must be mined and included in a block.
- Have a transaction hash.

### Calls (Blue Buttons in Remix)
- Read blockchain state without modifying it.
- When initiated off-chain (e.g., by a user interface):
  - Do not cost the caller gas.
  - Do not need to be mined.
  - Do not have a transaction hash associated with the read itself.
- Valid for public getters and `@view` functions.

## Gas Costs: The Nuances of @view
While off-chain calls to `@view` functions are free for the caller, this isn't always the case.

1. **Off-Chain Calls:** Calls made directly to a `@view` function or public getter from outside the blockchain (e.g., a user clicking a button in a dapp) are free for the user.
2. **On-Chain Calls:** If a transaction (e.g., our `store` function) calls a `@view` function during its execution, that call **does consume gas**. The Ethereum Virtual Machine (EVM) still needs to perform the computations and read operations defined within the `@view` function, and these operations contribute to the overall gas cost of the transaction making the call.

Consider this example:

```vyper
@external
def store(new_number: uint256):
    self.my_favorite_number = new_number
    # Calling a view function internally costs gas during this transaction
    current_val: uint256 = self.retrieve() 
```

Executing the `store` function here will cost more gas than a version without the `self.retrieve()` call, demonstrating that `@view` functions consume gas when executed as part of an on-chain transaction.

## Best Practices
- Always add the `@view` decorator to `@external` functions that are only intended to read blockchain state and not modify it.
- This ensures they behave as free calls when interacted with off-chain, saving users gas.
- It also clearly communicates the function's intent to other developers and auditors.
- Remember the distinction: `@view` functions are free for the caller **only** when called off-chain.
