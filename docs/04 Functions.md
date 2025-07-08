# 04 Functions

In programming, we often run into situations where we need to reuse the same logic multiple times.
Take, for example, calculating the hypotenuse using the Pythagorean theorem.
If we need to perform this calculation several times—say, four times—we wouldn’t want to rewrite the same code each time.

Not only would that be inefficient, but if we ever needed to make a change, we’d have to update every copy of the code manually. This creates extra work and increases the chance of introducing bugs.

Instead, we aim to make our code more reusable and readable—and that’s exactly where functions come in.
Functions let us define a block of code once and reuse it by simply calling its name. This avoids duplication and makes our code easier to maintain and reason about.

You’ve already seen one example of a function: the main function. It’s the special entry point of every C and C++ program. However, main is unique in that it only appears once per program and cannot be renamed.

## Function Creation

A function in C++ is made up of two parts; the declaration, which specifies how to call the function, and the definition, which contains the actual code that runs when the function is called.

The declaration, also called the function signature, includes four key elements.

| Part           | Description                                                                                       |
| -------------- | ------------------------------------------------------------------------------------------------- |
| Return Type    | The data type of the value the function returns. If nothing is returned, it uses the `void` type. |
| Function Name  | A unique identifier to call the function.                                                         |
| Parameter List | Input values the function accepts (can be empty if no inputs are needed).                         |
| Function Body  | The block of code executed when the function is called.                                           |


When creating functions, it’s important to declare them before they are used to avoid errors.
Also, **keep each function focused on a single task** as it helps make your code easier to read, maintain, and reuse.

``` title="signiture"
<return_type> <function_name>(<parameter_type> <parameter_name>, ...) {
    <function_body>
}
```

Functions receive input and output data, but there is no rule that requires them to take arguments or return a value.
**If a function doesn't return any data, we use a unique data type called `void`**, which explicitly indicates that the function does not return a value.

Functions with a `void` return type are primarily used for performing actions such as outputting to the terminal or working with
pointers and references, where returning a value is unnecessary.

Example of the simplest function used to reduce the amount of code needed to print output to the terminal.

```cpp title="main.cpp"
#include <iostream>

void PrintText() {
	std::cout << "Hello World!" << std::endl;
	std::cout << "I am learning C++" << std::endl;
}

int main() {
	PrintText();
	PrintText();
	PrintText();
	PrintText();
}
```

``` title="output"
Hello World!
I am learning C++
Hello World!
I am learning C++
Hello World!
I am learning C++
Hello World!
I am learning C++
```

### Return Values

If we want to retrieve information from an operation performed inside a function and store it in a variable in the `main` function, we use the `return` keyword.
This sends data, such as a number, a variable's value, or a direct expression, back to the caller. **However, the function's return type must match the type of data being returned**.

Here, we will calculate the value of the hypotenuse using the Pythagorean theorem and return the result of the expression back to the caller for output.

```cpp title="main.cpp"
#include <iostream>
#include <cmath>

float PythagoreanTheorem() {
    return std::sqrt(pow(3, 2) + std::pow(4, 2));
}

int main() {
	std::cout << "Hypotenuse = " << PythagoreanTheorem() << std::endl;
}
```

``` title="output"
Hypotenuse = 5
```

!!! note

	This example introduces the `<cmath>` header, which provides functions for a wide range of mathematical operations.
	For any math function, `<cmath>` is the go-to header.

	See the full list of its math functions here: [https://cplusplus.com/reference/cmath/](https://cplusplus.com/reference/cmath/)


#### Mandatory Return Values

**When a function has a non-void return type, it must return a value** of the specified data type. Failing to do so results in undefined behavior.

The only exception to this rule is the `main` function. Even though `main` is typically declared as returning `int`, we don’t always see an explicit return statement.
This is because modern C++ **compilers automatically add `return 0;` at the end of `main`**, indicating that the program executed successfully.

### Multiple Return Values

!!! info

	This section is considered more advanced at this point, as it requires knowledge of user-defined types.
	If you're not yet comfortable with those concepts, feel free to skip this section and return to it later once you're more confident.

There are situations where we need to return more than one value from a function. For example, in a game, you might want a function to return a player's position — `x`, `y`, and `z` coordinates.
It would be inefficient and messy to create a separate function for each coordinate.

This problem can be solved in several ways, but the most preferred and readable solution is using a structure, which groups related values into a single type.

```cpp title="main.cpp"
#include <iostream>

struct Vec3 {
	float x;
	float y;
	float z;
};

Vec3 GetPlayerCoords() {
	return Vec3 {
		.x = 43.4,
		.y = 38.2,
		.z = 29.5
	};
}

int main() {
	Vec3 player_position = GetPlayerCoords();
	
	std::cout << " x: " << player_position.x << std::endl;
	std::cout << " y: " << player_position.y << std::endl;
	std::cout << " z: " << player_position.z << std::endl;
}
```

``` title="output"
 x: 43.4
 y: 38.2
 z: 29.5
```

While using a `struct` is not the only way to return multiple values in C++, it is often the clearest and most maintainable option.

C++ also provides other tools for returning multiple values, such as:

- `std::tuple` – a general-purpose container for fixed-size groups of values.
- `std::pair` – similar to a tuple but limited to two values.

These options will be discussed later, but for now, `struct` is a great starting point for clean and understandable code.

### Parameter List

To make the function even more reusable, it would be useful to pass our own values into it. This is possible through the parameter list.
The parameter list is created by declaring empty variables inside the parentheses of the function declaration, these are called parameters.

Also, parameters and arguments are often used interchangeably, but technically:

- **Parameters** refer to the variables in the function declaration.
- **Arguments** are the actual values passed when calling the function.

```cpp title="main.cpp"
#include <iostream>
#include <cmath>

float PythagoreanTheorem(float a, float b) {
    return std::sqrt(pow(a, 2) + std::pow(b, 2));
}

int main() {
    std::cout << "Hypotenuse = " << PythagoreanTheorem(3, 4) << std::endl;
}
```

``` title="output"
Hypotenuse = 5
```

Parameters are assigned values during the function call, following the exact order in which they were defined.
These values can be either direct numbers or variables, but their types must always match the function's parameter types.

## Function Declaration vs. Definition

The difference between a declaration and a definition is that a declaration only specifies the function’s signature (return type, name, and parameters) and essentially informs the compiler that the function exists.
The definition, on the other hand, includes the actual implementation and logic of the function.

## Keep Functions Practical

While functions are essential for code reusability and readability, **it's important not to overuse them**.
Creating functions for every minor operation can lead to cluttered code that is difficult to maintain.

Additionally, **every time a function is called, the compiler generates a call instruction**, which introduces overhead. The program must:

1. Create a stack frame for the function
2. Push the parameters onto the stack
3. Store the return address
4. Jump to a different part of the binary to execute the function

All these memory operations can slow down the program, especially if the function isn't inlined (meaning that the implementation is tranformed into code that is inserted in the function stead) by the compiler.

A good rule of thumb is that **if you find yourself repeating the same logic multiple times, it’s likely a good candidate for a function**.

## Function Overloading

Function overloading occurs when we define multiple functions with the same name but different types or counts of parameters, allowing the function to perform the same action for different types of input.

For example, imagine we want to create a function for printing to the terminal that can handle multiple data types, such as integers or strings.
If a function is defined to accept only an int, passing a string, float, or char might cause data loss or a compiler error.

To solve this, we can overload the function—define multiple versions of it, each tailored to handle a different type of input.
The compiler will then automatically choose the correct one based on the arguments you pass.

```cpp title="main.cpp"
#include <iostream>
#include <string>

void Print(int value) {
    std::cout << "Integer only version: " << value << std::endl;
}

void Print(double value) {
    std::cout << "Double only version: " << value << std::endl;
}

void Print(int value1, double value2) {
    std::cout << "Combined version: " << value1 << " | " << value2 << std::endl;
}

int main() {
    Print(11);
    Print(74.53);
    Print(11, 74.53);
}
```

``` title="output"
Integer only version: 11
String only version: Hello, World!
Combined version: 11 | Hello World!
```

## Inline Functions

When we define a function, even if it's just a single line, every time we call it, the program performs a jump to the function's memory location, runs the code, and then jumps back.
This adds a bit of overhead, especially when calling very small functions repeatedly.

To reduce this overhead for short, frequently-used functions, we can use the `inline` keyword.
Instead of making the program jump, the compiler replaces the function call with the actual code of the function—essentially copying and pasting it at the call site.

This technique can slightly improve performance by avoiding function call overhead, especially in tight loops or small helper functions.

```cpp title="main.cpp"
#include <iostream>

inline int Square(int x) {
    return x * x;
}

int main() {
    std::cout << Square(4) << std::endl;
    std::cout << Square(7) << std::endl;

	/*
		In both cases, because we used the inline keyword,
		the function calls will be replaced by the compiler
		with the actual expression

		std::cout << 4 * 4 << std::endl;
        std::cout << 7 * 7 << std::endl;
	*/
}
```

!!! note

	The `inline` keyword is a suggestion to the compiler, not a command.
	Modern compilers are smart enough to decide whether inlining is worth it, even without the keyword.

	Inline functions are best used for very short functions (like getters, setters, or simple calculations).

	Avoid using `inline` on large functions—it can increase code size, which may negatively affect performance due to cache misses.

## Lambdas

Lambdas, also known as anonymous functions, let you define small, unnamed functions directly at the point of use.
They're mainly intended for short, one-off operations and behave like disposable function objects.
Lambdas are especially useful when you need to run a quick piece of logic without declaring a full function elsewhere.
They can also capture and use variables from the surrounding scope, making them both powerful and flexible.

### When to use lambdas

Whenever you have a function pointer, you can use a lambda in C++.
The best example of this is when we need to pass a function to an API so that it can call the function at a later time.
**Lambdas are ideal for defining code that should run in the future**.

This is useful when:

- The function requires data that isn’t available yet.
- We want to defer execution but still specify which function to call when needed.

```cpp title="main.cpp"
#include <iostream>
#include <vector>
#include <functional>

void ForEach(const std::vector<int>& values, const std::function<void(int)>& func) {
	for (int value : values)
		func(value);
}

int main() {
	std::vector<int> values = {27, 64, 3, 89, 51};
	
	std::function<void(int)> lambda = [](int value) { 
        std::cout << value << '\t'; 
    };

	ForEach(values, lambda);
}
```

``` title="output"
27	64	3	89	51	
```

The square brackets before a lambda expression are called capture clauses.
They allow the lambda to access variables from the surrounding scope using different capture modes, which are specified with various symbols.

| Capture Syntax             | Description                                                        |
|----------------------------|--------------------------------------------------------------------|
| `[]`                       | No capture – the lambda has no access to outer variables.          |
| `[variable]`               | Capture by value – creates a copy of `variable`.                   |
| `[&variable]`              | Capture by reference – modifies the original `variable`.           |
| `[=]`                      | Capture all by value – copies all outer variables used.            |
| `[&]`                      | Capture all by reference – references all outer variables.         |
| `[variable1, &variable2]`  | Mixed capture – `variable1` by value, `variable2` by reference.    |

These **captured variables are read-only by default** when captured by value.
If you want to modify them inside the lambda, you need to use the `mutable` specifier after the parameter list.

```cpp title="main.cpp"
#include <iostream>

int main() {
    int count = 10;

    auto lambda = [count]() mutable { 
        count++;  // Allowed because of `mutable`
        std::cout << "Lambda modified count: " << count << std::endl;
    };

    lambda();

    std::cout << "Original count: " << count << std::endl;
}
```

``` title="output"
Lambda modified count: 11
Original count: 10
```

### Lambda Use Case

A common use case for lambda expressions is within algorithm functions, such as `std::find_if`.
This function, which is part of the `<algorithm>` header, is used to find a specific value in an iterable data structure based on a given condition.

```cpp title="main.cpp"
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
	std::vector<int> values = {1, 5, 4, 2, 3};

	auto it = std::find_if(values.begin(), values.end(), [](int value) { 
        return value > 3;
    });
						   
	std::cout << "First number bigger than 3: " << *it << std::endl;
}
```

``` title="output"
First number bigger than 3: 5
```

## Chapter Summary

In this chapter, we explored the fundamental concepts of functions in C++, from their declaration to advanced topics like lambdas.

- **Function Declarations and Definitions**: A function must be declared before use and defined properly to ensure correctness.
- **Return Values**: Every function that returns a value must do so explicitly, and multiple return values can be achieved using structures.
- **Parameter Lists**: Functions can accept parameters by value, reference, or pointer, affecting performance and memory usage.
- **Practicality**: Functions should be small, well-defined, and reusable to improve readability and maintainability.
- **Lambdas** provide a concise way to define small, inline functions, making them useful for callbacks, functional programming, and local scope operations.