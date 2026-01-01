# Recap: Understanding Your First Vyper Smart Contract - favorites.vy

Congratulations on writing your first Vyper smart contract! This lesson recaps the fundamental concepts we covered while building the `favorites.vy` contract using the Remix Ethereum IDE. Understanding these basics is crucial before moving on to more complex topics.

Let's revisit the key elements of our contract:

### Vyper Version Pragma
The very first line in our contract specifies the compiler version:
```vyper
@version ^0.4.0
```
This is essential to ensure your code behaves as expected with future compiler updates.

### Structs: Defining Custom Data Types
We used structs to group related information:
```vyper
struct Person:
    favorite_number: uint256
    name: String[100]
```
Structs help organize data logically.

### State Variables: Storing Data on the Blockchain
Variables declared outside of any function are state variables. They represent persistent data.
*   `uint256`: Whole non-negative numbers.
*   `String[100]`: Text limited to 100 bytes.
*   `uint256[5]`: Fixed-size list of numbers.
*   `HashMap[...]`: Key-value mapping.

### Visibility: Controlling Access with public
Using `public(...)` makes a variable readable from outside and automatically creates a getter function unless you want to write a custom one.

### The Constructor: Initializing Your Contract
The `__init__` function (marked with `@deploy`) runs only once during deployment to set up the initial state.
```vyper
@deploy
def __init__():
    self.my_favorite_number = 7
```

### Functions: Defining Contract Behavior
Functions define the logic.
*   `@external`: Callable from outside.
*   `@view`: Read-only, no state modification, generally no gas cost to call.
*   `self`: The keyword used to access state variables inside functions.

### Key Takeaways
*   **State Modification** (like `store`, `add_person`) requires transactions and costs gas.
*   **State Reading** (like `retrieve`) can be done via view functions generally without execution gas costs.
*   **Remix IDE** is a powerful tool for learning and testing.

You've successfully navigated the core components of a Vyper smart contract! Now is a perfect time to take a break and let this information sink in. Well done!
