# Organizing Complex Data with Vyper Structs and Strings

In previous lessons, we explored basic Vyper functionality, such as storing a favorite number or a simple list of numbers. Let's revisit a contract that stores a list of favorite numbers. While this contract successfully stores a list of `uint256` values in the `list_of_numbers` array, it has a limitation: we only store the numbers themselves. We don't know who these favorite numbers belong to.

How can we associate a name with each number? Vyper provides powerful tools for creating more complex, custom data structures: **structs** and **Strings**.

## Introducing Structs: Creating Custom Data Types

To associate a name with a favorite number, we need a way to bundle these two pieces of data together. Vyper allows us to define our own custom data types using **structs**.

A struct (short for structure) is a way to group several related variables (called members) under a single name. Think of it as creating a blueprint for a new type.

### Syntax

You define a struct using the `struct` keyword, followed by the name you want to give your new type (typically using CapitalCase convention), and then an indented block listing the members and their types.

```vyper
struct Person:
    favorite_number: uint256
    name: String[100]
```

In this example, we've created a new type named `Person`. Any variable declared with the type `Person` will hold both a `uint256` value for `favorite_number` and a `String[100]` value for `name`. Struct definitions are typically placed near the top of your contract file.

Once defined, you can use `Person` just like any other built-in Vyper type (e.g., `uint256`, `address`) for state variables, function parameters, or local variables.

## Working with Text: The String Type

Our `Person` struct includes a `name` member. To store text data like names, Vyper uses the **String** type.

A key characteristic of Strings in Vyper is that they have a fixed maximum size. You must specify this maximum size when declaring a string variable.

### Syntax

`String[MAX_LENGTH]`

Here, `MAX_LENGTH` is an integer representing the maximum number of bytes the string can occupy. While not a direct character count (due to potential multi-byte characters), it gives you an upper bound on the string's length. For instance, `String[100]` declares a string variable that can hold up to 100 bytes of text data.

### Example

Let's temporarily add a public string state variable to see how it works:

```vyper
my_name: public(String[100])
```

If you compile and deploy this modified contract, you can call the public getter `my_name` and it will return the string value (if one was assigned). String values are assigned using double quotes (`"Patrick"`).

## Storing Collections: Arrays of Structs

Now that we have a custom `Person` type which includes a name, how do we store a list of these Person objects? We use an array, just like we did for `uint256`, but this time the array's elements will be of type `Person`.

### Syntax

`variableName: public(StructName[ARRAY_SIZE])`

To store a list of up to 5 people, we can declare a state variable like this:

```vyper
list_of_people: public(Person[5])
```

This creates a public, fixed-size array named `list_of_people`. It can hold 5 elements, and each element must be a `Person` struct. Conceptually, you can think of this array holding data like: `[(7, "Patrick"), (16, "Chelsea"), (42, "Mark"), ...]`.

## Putting It All Together: Adding People to the List

Let's modify our `add_number` function to store `Person` structs instead of just numbers. We'll rename the function to `add_person` and update its logic.

### Modified Function

```vyper
@external
def add_person(name: String[100], favorite_number: uint256):
    # Create a new Person struct instance
    new_person: Person = Person(favorite_number=favorite_number, name=name)
    
    # Store it in the array at the current index
    self.list_of_people[self.index] = new_person
    
    # Increment the index
    self.index = self.index + 1
```

### Explanation

1.  **Function Signature**: The function now accepts two arguments: `name` (of type `String[100]`) and `favorite_number` (of type `uint256`).
2.  **Creating a Struct Instance**: Inside the function, we declare a local variable `new_person` of type `Person`. We initialize it using the `Person(...)` syntax, providing values for its members based on the function inputs. Using keyword arguments (`favorite_number=...`, `name=...`) makes the code clearer.
3.  **Storing the Struct in the Array**: We then assign this `new_person` struct instance to an element in our state variable array `list_of_people`. Crucially, we must use `self.list_of_people` to refer to the state variable array. Forgetting `self.` would result in a compile-time error.
4.  **Incrementing the Index**: Finally, we increment `self.index` so that the next call to `add_person` stores the new data in the next available slot in the array.

Now, if you call `add_person` with "Mark" and 7, the `Person` struct `(7, "Mark")` will be created and stored at `self.list_of_people[0]`. Calling the public getter `list_of_people` with index 0 will return this data as a tuple.

## Conclusion

By combining structs, Strings, and arrays, we can build much more sophisticated and organized data structures within our Vyper smart contracts.

*   **Structs** allow us to define custom types by grouping related data.
*   **Strings** (`String[MAX_LENGTH]`) allow us to store fixed-size text data.
*   **Arrays of Structs** (`StructName[ARRAY_SIZE]`) allow us to store lists or collections of our custom data types.

This combination is fundamental for representing real-world entities and relationships on the blockchain.
