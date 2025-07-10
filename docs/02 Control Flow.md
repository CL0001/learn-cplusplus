# 02 Control Flow

!!! info

    This chapter goes hand in hand with the 03 Operators chapter.
    So keep in mind that the two chapters are closely related, and many concepts work together.
    If something seems skipped here, it's likely because it was better suited for that chapter.

There always comes a point when our code needs to branch out — meaning we only want certain actions to happen if specific conditions are met.

For example, in a game, we might want to restrict access to certain areas for low-level players to keep beginner zones separate from tougher ones.
Or maybe a quest can only be completed if the player has a specific item.
These decisions depend on conditions — and the possibilities are endless.
It can also be used to check for errors — something we'll explore more deeply in the chapter focused on error handling — and you'll see it come up repeatedly throughout the early chapters as well.

We manage this kind of behavior using control flow statements, which let us check a condition and decide what happens based on whether it’s true or false.
Think of them as checkpoints in your code: Before moving forward, the program asks if the condition true.
If it is, the code inside the check runs; otherwise, it is skipped.

### If Statements

The most basic and essential control flow tool in programming is the `if` statement.
It does exactly what it sounds like: it checks a condition and decides what to do based on the result.

The condition is written inside parentheses immediately following the `if` keyword.
We typically use comparison and logical operators to form this condition — it must evaluate to either `true` or `false`.

If the condition is `true`, the code inside the curly braces (called the statement body, block, or scope) will execute.
If the condition is `false`, that block is skipped.

```cpp title="main.cpp"
#include <iostream>

int main() {
	int player_has_item = true;

	if (player_has_item) {
		std::cout << "Quest Completed!" << std::endl;
	}
}
```

``` title="output"
Quest Completed!
```

It’s also possible to extend an `if` statement with two additional keywords to form a complete conditional structure.

The first is `else if`, which allows us to check another condition if the original if condition wasn’t met.
For example, if a player is too low-level, the first condition will block them from entering.
But with `else if`, we can also check if the player is too high-level for a specific zone — and handle that differently, such as temporarily reducing their level or showing a warning.

The final piece is `else`, which does not take a condition.
It acts as a fallback: if none of the previous conditions were met, the code inside the else block will execute.

```cpp title="main.cpp"
#include <iostream>

int main() {
    int player_level = 42;

    if (player_level < 20) {
        std::cout << "Your level is too low to enter this zone.\n";
    } else if (player_level > 80) {
        std::cout << "You are overleveled for this area! Consider a tougher challenge.\n";
    } else {
        std::cout << "You’ve entered the zone. Good luck!\n";
    }
}
```

``` title="output"
You’ve entered the zone. Good luck!
```

### Switch Statements

When we need to compare a variable against several fixed values, writing a long chain of if-else statements can get messy and harder to read.
In these situations, a `switch` statement offers a cleaner and often more efficient alternative.

One reason `switch` can be faster is that many compilers optimize it using a jump table, allowing the program to instantly jump to the matching case without checking each condition one by one.

Also, unlike an `if` statement, a `switch` doesn’t evaluate full conditions inside its parentheses.
Instead, it works with a single variable and checks its value against several predefined options, known as cases.

You place the variable you want to match inside the parentheses of the `switch` statement, then list the possible values using `case` followed by each specific value.
After each case, you write the code that should run when that value matches.
Each case typically ends with a `break` to prevent the program from unintentionally continuing into the next case.
If none of the cases match, you can include a `default` case at the end to handle all other values.

```cpp title="main.cpp"
#include <iostream>

int main()  {
    char grade = 'C';
    
    switch (grade) {
        case 'A': 
            std::cout << "Excellent!" << std::endl;
            break;
        case 'B': 
            std::cout << "Good job!" << std::endl;
            break;
        case 'C': 
            std::cout << "You passed." << std::endl;
            break;
        case 'D': 
            std::cout << "You should work harder." << std::endl;
            break;
        case 'F': 
            std::cout << "You failed." << std::endl;
            break;
        default:
            std::cout << "Invalid grade." << std::endl;
    }
}
```

``` title="output"
You passed.
```

!!! note

    The values a `switch` statement can match against must be integer types, such as `int` or `char`.
    Whole strings (text) cannot be used directly.

In a `switch` statement, leaving out the `break` between cases causes what's called a fallthrough—the execution "falls through" to the next case until it encounters a break or reaches the end of the `switch`.
This can be intentional and useful when multiple cases should trigger the same behavior, like grouping `'A'`, `'B'`, and `'C'` to all print the same thing.
However, be careful: unintentional fallthroughs can lead to bugs, and since the compiler doesn’t always warn you, they can be tricky to catch.

```cpp title="main.cpp"
#include <iostream>

int main()  {
    char grade = 'A';
    
    switch (grade) {
        case 'A':
        case 'B':
        case 'C': 
            std::cout << "You passed." << std::endl;
            break;
        case 'D': 
            std::cout << "You should work harder." << std::endl;
            break;
        case 'F': 
            std::cout << "You failed." << std::endl;
            break;
        default:
            std::cout << "Invalid grade." << std::endl;
    }
}
```

``` title="output"
You passed.
```

### Go to

The `goto` statement is a control flow mechanism that allows you to jump to a labeled part of the code.
While it does exist in C++, it's generally discouraged because it can make your code harder to read, understand, and maintain.
Introducing arbitrary jumps in the program breaks the natural, structured flow of execution and often leads to what's known as “spaghetti code.”

There are very few legitimate use cases for `goto`, such as breaking out of deeply nested loops in older codebases, but in modern C++, there are almost always better alternatives like functions, loops, and exception handling.
In short: avoid using goto unless absolutely necessary — and in most cases, it won’t be.

```cpp title="example"
#include <iostream>

int main() {
    int value;
    
    std::cout << "Enter a number (0 to exit): ";
    std::cin >> value;

    if (value == 0)
        goto end;

    std::cout << "You entered: " << value << std::endl;

end:
    std::cout << "Program ended." << std::endl;
}
```

In the first example, it's easy to see how using `goto` makes the code feel awkward and harder to follow.
It introduces an unnatural jump in the program’s flow, which hurts readability — especially in larger projects.

Instead, the second example uses a simple `if` statement, making the logic more structured and easier to understand.
Whenever you find yourself reaching for goto, it’s usually a sign that the code structure should be rethought.
There's almost always a cleaner alternative.

```cpp title="example"
#include <iostream>

int main() {
    int value;

    std::cout << "Enter a number (0 to exit): ";
    std::cin >> value;

    if (value != 0) {
        std::cout << "You entered: " << value << std::endl;
    }

    std::cout << "Program ended." << std::endl;
}
```

## Loops

One of the most common challenges in programming is how to repeat a task multiple times without writing the same code over and over again.

Sure, if we need to print a message a hundred times, we could write the same line of code a hundred times — but that would be tedious, error-prone, and completely impractical.
And for example, what if we want to keep displaying something endlessly until the user closes the program, writing it out manually just isn’t an option.

Loops let us repeat a block of code as long as a certain condition is met, making repetitive tasks much cleaner and easier to manage.

### While Loops

The simplest type of loop in C++ is the `while` loop.
It works similarly to an `if` statement, but instead of running just once, it repeatedly executes the code block as long as the condition remains true.
This loop is especially useful when you don’t know in advance how many times a task needs to be repeated—for example, reading a password from a user or processing lines in a file where the total number of lines isn’t known.

```cpp title="main.cpp"
#include <iostream>

int main() {
	while (true) {
		std::cout << "Hello World!" << std::endl;
	}
}
```

``` title="output"
Hello World!
Hello World!
Hello World!
Hello World!
Hello World!
...
```

In this example, the condition is checked before each loop iteration.
As long as the condition is `true`, the code inside the block continues to run.
Since `true` never becomes `false`, this creates an infinite loop.

There’s also a variation called the do-while loop, where the code block is executed at least once, and the condition is checked afterward.
It’s essentially an inverted while loop that guarantees the code executes at least once before the condition is checked.

```cpp title="example"
#include <iostream>

int main() {
    int value;
    
    do {
        std::cout << "Enter number: " << count << std::endl;
        std::cin >> value;
    } while (value > 0);
}
```

As you can see, the condition in a `while` loop or do-while loop can be more complex than just a single variable or value, similar to an `if` statement.
It can also involve expressions that evaluate to `true` or `false`.

### For Loops

When we don’t know how many times a process should repeat—because it depends on user input, external conditions, or when the program is closed—we use a while loop.
On the other hand, when we do know exactly how many times we want to repeat a process, a for loop is a cleaner and more concise choice.

For example, if we want to print numbers from 1 to 10, a for loop is ideal.
A for loop consists of three parts.

| Part                    | Description                                                  |
| ----------------------- | ------------------------------------------------------------ |
| Initialization          | Sets the starting value of the loop variable.                |
| Condition               | Defines when the loop should stop executing.                 |
| Increment/Decrement     | Controls how the loop variable changes after each iteration. |

Because all three parts are combined in the loop header, a `for` loop keeps your code compact and easy to read, especially when the number of repetitions is fixed or known ahead of time.

```cpp title="main.cpp"
#include <iostream>

int main() {
    for (int i = 1; i <= 10; ++i) {
        std::cout << i << '\t';
    }
}
```

``` title="output"
1	2	3	4	5	6	7	8	9	10
```

### Nested Loops

Sometimes you’ll need to repeat a loop inside another loop — this is called a nested loop.  
Each iteration of the outer loop runs the entire inner loop. This is useful for working with multi-dimensional data or creating patterns.

```cpp title="main.cpp"
#include <iostream>

int main() {
    int rows = 5;

    for (int i = 1; i <= rows; ++i) {
        for (int j = 1; j <= i; ++j) {
            std::cout << "* ";
        }
        std::cout << std::endl;
    }
}
```

``` title="output"
* 
* * 
* * * 
* * * * 
* * * * * 
```

!!! warning

    Always use a different variable name for the inner loop.
    If the inner and outer loops share the same variable, it can cause unexpected behavior and logic errors.

## Break and Continue

Control flow inside loops can be further refined with the break and continue statements.

| Keyword    | Description                                                    |
| ---------- | -------------------------------------------------------------- |
| `break`    | Immediately exits the loop, skipping all remaining iterations. |
| `continue` | Skips the current iteration and jumps to the next one.         |

These are helpful to control loop execution without complicating conditions. 

```cpp title="main.cpp"
#include <iostream>

int main() {
    for (int i = 1; i <= 10; ++i) {
        // Exit loop when i is 5
        if (i == 5) {
            break;
        }

        // Skip even numbers
        if (i % 2 == 0) {
            continue;
        }

        std::cout << i << " ";
    }
}
```

``` title="output"
1 3 
```

## Shortened Notations

These statements we went trough follow the same pattern and there fore this applies to all of them.
And that is that They can also be written without curly braces when they contain only a single line of the code inside of the body.

```cpp title="example"
#include <iostream>

int main() {
    int count = 1;

    // While loop without braces
    while (count <= 5)
        std::cout << count++ << " ";

    std::cout << std::endl;

    // For loop without braces
    for (int i = 1; i <= 10; ++i)
        std::cout << i << " ";

    std::cout << std::endl;

    int x = 3;

    // If statement without braces
    if (x > 0)
        std::cout << "x is positive" << std::endl;
}
```

``` title="output"
1 2 3 4 5 
1 2 3 4 5 6 7 8 9 10 
x is positive
```

## Exercises

=== "task 1"

    Write a program that asks the user to enter an integer.  
    Use an if-else statement to check whether the number is even or odd, and print the appropriate message.

=== "answer"

    ```cpp
    #include <iostream>

    int main() {
        int number;

        std::cout << "Enter an integer: ";
        std::cin >> number;

        if (number % 2 == 0)
            std::cout << "Number is even" << std::endl;
        else
            std::cout << "Number is odd" << std::endl;
    }
    ```

---

=== "task 2"

    Write a program that counts down from 10 to 1 using a `for` loop.

    - Skip the number 5
    - Stop the loop entirely when the number reaches 2

=== "answer"

    ```cpp
    #include <iostream>

    int main() {
        for (int i = 10; i >= 1; --i) {
            if (i == 5)
                continue;

            if (i == 2)
                break;

            std::cout << i << '\t';
        }
    }
    ```

---

=== "task 3"

    Write a program that prints a 5 × 5 square made of `#` characters.

    - Skip the third row entirely so that it appears as a blank line in the output.

=== "answer"

    ```cpp
    #include <iostream>

    int main() {
        for (int i = 0; i < 5; ++i) {
            if (i == 2) {
                std::cout << std::endl;
                continue;
            }

            for (int j = 0; j < 5; ++j) {
                std::cout << "# ";
            }

            std::cout << std::endl;
        }
    }
    ```

---

=== "task 4"

    Write a program that prints a reversed pyramid of `@` characters, starting with 5 symbols in the first row and decreasing by one each subsequent row.

=== "answer"

    ```cpp
    #include <iostream>

    int main() {
        for (int i = 5; i > 0; --i) {
            for (int j = i; j > 0; --j) {
                std::cout << "@ ";
            }

            std::cout << std::endl;
        }
    }
    ```