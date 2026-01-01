# Welcome to the Workshop: Reinforce Your Vyper Skills

Congratulations on completing Section 1: Welcome to Remix - Favorite's List! To help you solidify the concepts you've just learned, we're introducing a recurring feature called the "Workshop".

The primary goal of these workshops is simple: **practice**. By working through targeted exercises, you'll actively apply the principles covered in the preceding lessons.

## Recommended Workshop Workflow: Practice, Pause, and Proceed

To get the most out of these workshops:

1.  **Attempt Independently First**: Dedicate focused time (e.g., 25 minutes) to solving the workshop prompts on your own using what you've learned.
2.  **Know When to Pause**: If the timer runs out and you're stuck, stop. Don't let frustration take over.
3.  **Seek Assistance Smartly**: After taking a break, leverage resources like AI assistants, discussion forums, or the community Discord.

## Workshop Prompts: Remix Favorites List (favorites.vy)

Based on the `favorites.vy` contract developed in the "Favorite's List" section, tackle the following three prompts.

### Prompt 1: Create an add Function

*   **Task**: Define a new function within your contract named `add`.
*   **Functionality**: This function should increment the value stored in the `my_favorite_number` state variable by 1 each time it's called.
*   **Context**: You are adding a new way to modify the stored number.

```vyper
# Hint structure
@external
def add():
    # Your logic here to increment self.my_favorite_number
```

### Prompt 2: Modify the Starting Value

*   **Task**: Change the initial value assigned to the `my_favorite_number` state variable when the contract is first deployed. Choose any value other than 7.
*   **Verification**: Deploy and check the value using `retrieve`.
*   **Context**: This involves editing the `__init__` constructor.

### Prompt 3: Create and Use a New Struct (Challenge)

This prompt involves multiple steps:

1.  **Define a New Struct**: Define a new custom data type with fields of your choice.
2.  **Create a New State Variable**: Declare a state variable of this new struct type.
3.  **Create a Struct-Populating Function**: Write a function that accepts inputs and creates an instance of your struct, storing it in the state variable.

**Extra Credit**: Create a `@view` function to retrieve the data stored in your new struct.

Now, put your knowledge into practice!
