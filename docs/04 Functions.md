# 04 Functions

When writing code, we often run into situations where the same logic needs to be used multiple times.
Take, for example, calculating the hypotenuse using the Pythagorean theorem, or performing an action to move a player in a video game.

If we needed to perform this calculation several times—say, four times, or in the case of a game, once every frame—we wouldn’t want to rewrite the same code repeatedly.
In fact, in many cases, doing so wouldn’t even be practical.

Not only would that be inefficient, but if we ever needed to make a change, we’d have to update every copy of that code manually.
That creates extra work and increases the risk of bugs creeping in.

Instead, we want our code to be reusable, readable, and easy to maintain—and that’s exactly where functions come in.
Functions let us define a block of code once and reuse it as many times as we need, simply by calling its name.
This keeps programs concise and makes them far easier to understand and modify.

You’ve already seen one example of a function: the main function.
It’s the special entry point of every C/C++ program.
However, main is unique in that it only appears once per program and cannot be renamed.

## Function Creation

A function in C++ is made up of two parts; the declaration, which specifies how to call the function, and the definition, which contains the actual code that runs when the function is called.

The declaration, also called the function signature, consists of four key elements.

| # | Part           | Description                                             |
| - | -------------- | --------------------------------------------------------|
| 1 | Return Type    | The data type of the value the function returns.        |
| 2 | Function Name  | A unique identifier to call the function.               |
| 3 | Parameter List | Input values the function accepts.                      |
| 4 | Function Body  | The block of code executed when the function is called. |

``` title="function signature structure"
<return-type> <function-name>(<parameter-list>) {
    <function-body>
}
```

When creating functions, it’s important to declare them before they are used to avoid errors.
Also, keep each function focused on a single task as it helps make your code easier to read, maintain, and reuse.

!!! warning
	Additionally, every time a function is called, the compiler generates a call instruction, which introduces overhead.
	The program must:

	1. Create a stack frame for the function
	2. Push the parameters onto the stack
	3. Store the return address
	4. Jump to a different part of the binary to execute the function
	
	All these memory operations can slow down the program—especially if the function isn't inlined meaning that the implementation is expanded directly at the call site by the compiler.

	A good rule of thumb is that if you find yourself repeating the same logic multiple times, it’s likely a good candidate for a function.

Functions receive input and output data, but there is no rule that requires them to take arguments or return a value.
If a function doesn't return any data, we use a unique data type called `void`, which explicitly indicates that the function does not return a value.

Functions with a `void` return type are primarily used for performing actions such as outputting to the terminal or working with pointers and references, where returning a value is unnecessary.

Example of the simplest function used to reduce the amount of code needed to print output to the terminal.

```cpp title="main.cpp"
#include <iostream>

void PrintText() {
	std::cout << "Hello World!\n";
	std::cout << "I am learning C++\n";
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

When we want to retrieve information from an operation performed inside a function and use it elsewhere—such as in the `main` function—we use the `return` keyword.
This sends data (a number, a variable’s value, or even a direct expression) back to the function’s caller.
However, the function’s declared return type must match the type of value being returned.

In the example below, we calculate the length of a hypotenuse using the Pythagorean theorem and return the result to the caller for output.

```cpp title="main.cpp"
#include <iostream>
#include <cmath>

double CalculateHypotenuse() {
    return std::sqrt(std::pow(3, 2) + std::pow(4, 2));
}

int main() {
    std::cout << "Hypotenuse = " << CalculateHypotenuse();
}
```

``` title="output"
Hypotenuse = 5
```

!!! note

	This example introduces the `<cmath>` header, which provides functions for a wide range of mathematical operations.
	Whenever you need to perform mathematical computations, `<cmath>` is the go-to header.

	You can explore its full list of available functions here:
	<a href="https://cplusplus.com/reference/cmath/" target="_blank" rel="noopener noreferrer">https://cplusplus.com/reference/cmath/</a>

!!! warning

	When a function has a non-void return type, it must return a value of the specified data type.
	Failing to do so results in undefined behavior.

	The only exception to this rule is the `main` function.
	Although `main` is typically declared as returning an `int`, we don’t always see an explicit return statement.
	This is because modern C++ compilers automatically insert `return 0;` at the end of `main`, indicating that the program executed successfully.

### Multiple Return Values

!!! info

	This section is considered more advanced at this point, as it requires knowledge of user-defined types.
	If you're not yet comfortable with those concepts, feel free to skip this section and return to it later once you're more confident.

There are situations where we need to return more than one value from a function.
For example, in a game, you might want a function to return a player's position — `x`, `y`, and `z` coordinates.
It would be inefficient and messy to create a separate function for each coordinate.

This problem can be solved in several ways, but the most preferred and readable solution is using a structure, which groups related values into a single type.

```cpp title="main.cpp"
#include <iostream>

struct Vec3 {
	double x;
	double y;
	double z;
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
	
	std::cout << " x: " << player_position.x << '\n';
	std::cout << " y: " << player_position.y << '\n';
	std::cout << " z: " << player_position.z;
}
```

``` title="output"
 x: 43.4
 y: 38.2
 z: 29.5
```

While using a `struct` is not the only way to return multiple values in C++, These will be discussed later, but for now, `struct` is an excellent starting point for clean and readable code.


### Parameter List

To make the function even more reusable, it would be useful to pass our own values into it.
This is possible through the parameter list.
The parameter list is created by declaring empty variables inside the parentheses of the function declaration, these are called parameters.

Also, parameters and arguments are often used interchangeably, but technically:

- Parameters refer to the variables in the function declaration.
- Arguments are the actual values passed when calling the function.

```cpp title="main.cpp"
#include <iostream>
#include <cmath>

double CalculateHypotenuse(double a, double b) {
    return std::sqrt(pow(a, 2) + std::pow(b, 2));
}

int main() {
    std::cout << "Hypotenuse = " << CalculateHypotenuse(3, 4) << '\n';
}
```

``` title="output"
Hypotenuse = 5
```

Parameters are assigned values during the function call, following the exact order in which they were defined.
These values can be either direct numbers or variables, but their types must always match the function's parameter types.

### Default Arguments

Now that we understand how parameters receive input through arguments, we can take this idea a step further.
In many functions, a parameter often uses the same value repeatedly.
Writing that value every time doesn’t add clarity — it only adds noise.
This is where default arguments become useful.

Default arguments allow you to assign a value directly in the function declaration.
If the caller doesn’t provide an argument for that parameter, the default is used automatically.

A default argument is written by assigning a value to a parameter inside the function declaration.
When a function call omits that argument, the compiler fills in the missing value with the default.

```cpp title="output"
#include <iostream>

void PrintNumber(int number, int repeat_count = 1) {
    for (int i = 0; i < repeat_count; ++i) {
        std::cout << number << ' ';
    }
    
    std::cout << '\n';
}

int main() {
    PrintNumber(87);
    PrintNumber(87, 10);
}
```

``` title="output"
87 
87 87 87 87 87 87 87 87 87 87
```

Default arguments must appear from right to left in the parameter list.
Once a parameter has a default value, every parameter after it must also have one — this ensures the compiler always knows which argument corresponds to which parameter.

Defaults also belong at the point of declaration, not definition.
If a function is declared in a header and defined in a source file, the default values should be placed in the header so all translation units see the same defaults.

Default arguments keep function calls clean without restricting flexibility.
They remove unnecessary repetition while still allowing callers to override the defaults whenever needed.

### Function Attributes

In addition to a function’s signature, C++ lets us describe extra behavior around a function.
One way to do this is through function attributes, a standardized mechanism for attaching metadata to declarations.

Attributes use the `[[...]]` syntax and are designed to be portable, structured, and easy for compilers—and readers—to understand.
They don’t change how a function fundamentally works; instead, they provide additional information or guidance.
In practice, only a small number of attributes are both common and broadly useful.

| Attribute                         | Meaning                                            | Typical Use                                                                                      |
|-----------------------------------|----------------------------------------------------|---------------------------------------------------------------------------------------------------|
| `[[nodiscard]]`                   | Warns if a caller ignores the return value.        | Functions that compute results where ignoring the output is likely a mistake.                    |
| `[[maybe_unused]]`                | Suppresses “unused variable/parameter” warnings.   | Conditional compilation, placeholder parameters, or temporary scaffolding.                       |
| `[[deprecated]]`                  | Marks a function as old or to be avoided.          | APIs you want callers to transition away from.                                                   |
| `[[likely]]`, `[[unlikely]]`      | Hints which branches the compiler should optimize. | Performance-critical conditions where profiling shows a consistent trend.                        |

```cpp title="main.cpp"
[[nodiscard]] int Multiply(int a, int b) {
    return a * b;
}

int main() {
	Multiply(10, 20);
}
```

``` title="output"
<source>: In function 'int main()':
<source>:6:13: warning: ignoring return value of 'int Multiply(int, int)', declared with attribute 'nodiscard' [-Wunused-result]
    6 |     Multiply(10, 20);
      |     ~~~~~~~~^~~~~~~~
<source>:1:19: note: declared here
    1 | [[nodiscard]] int Multiply(int a, int b) {
      |                   ^~~~~~~~
Compiler returned: 0
```

These attributes appear frequently in modern C++ codebases and help make APIs clearer without introducing any runtime overhead.
They’re small additions that improve communication—between you, future maintainers, and the compiler.

### Function Specifiers

While attributes serve as metadata, C++ also provides a set of function specifiers—keywords that directly influence how a function behaves.
These differ from attributes and do not use the `[[...]]` syntax.
Instead, they modify the rules a function must follow or introduce additional capabilities, such as compile-time evaluation, inlining hints, or exception guarantees.

The most immediately relevant specifier is `static`.

The `static` specifier changes how a function is linked and accessed.
For free (non-member) functions, it gives the function internal linkage, meaning it is only visible within the translation unit where it is defined.
This is useful for “private” helper functions that shouldn’t be used outside their file.

```cpp title="main.cpp"
[[nodiscard]] static int Multiply(int a, int b) {
    return a * b;
}

int main() {
	std::cout << Multiply(10, 20);
}
```

``` title="output"
200
```

We’ll encounter a few more specifiers as we move deeper into the language.
Introducing them gradually keeps the learning curve reasonable.
Whenever a new specifier appears in the book, it will be clearly called out and explained in context.

## Function Overloading

Function overloading occurs when we define multiple functions with the same name but different types or counts of parameters.
This allows the function to perform the same action for different types of input.

For example, imagine we want to create a function for printing to the terminal that can handle multiple data types, such as integers or strings.
If a function is defined to accept only an integer, passing a string, float, or char might cause data loss or a compiler error.

To solve this, we can overload the function—define multiple versions of it, each tailored to handle a different type of input.
The compiler will then automatically choose the correct one based on the arguments we pass.

```cpp title="main.cpp"
#include <iostream>
#include <string>

void Print(int value) {
    std::cout << "Integer only version: " << value << '\n';
}

void Print(double value) {
    std::cout << "Double only version: " << value << '\n';
}

void Print(int value1, double value2) {
    std::cout << "Combined version: " << value1 << " | " << value2 << '\n';
}

int main() {
    Print(11);
    Print(74.53);
    Print(11, 74.53);
}
```

``` title="output"
Integer only version: 11
Double only version: 74.53
Combined version: 11 | 74.53
```

## Inline Functions

When we define a function, even if it's just a single line, every time we call it, the program performs a jump to the function's memory location, runs the code, and then jumps back.
This adds a bit of overhead, especially when calling very small functions repeatedly.

To reduce this overhead for frequently used functions, we can use the inline keyword.
Instead of making the program jump, the compiler replaces the function call with the actual code of the function—essentially copying and pasting it at the call site.

This technique can improve performance slightly by avoiding function call overhead—especially in tight loops or small helper functions.

```cpp title="main.cpp"
#include <iostream>

inline int Square(int x) {
    return x * x;
}

int main() {
    std::cout << Square(4) << '\n';
    std::cout << Square(7);

	/*
		In both cases, because we used the inline keyword,
		the function calls will be replaced by the compiler
		with the actual expression

		std::cout << 4 * 4 << '\n';
        std::cout << 7 * 7;
	*/
}
```

!!! note

	The `inline` keyword is a suggestion to the compiler, not a command.
	Modern compilers are smart enough to decide whether inlining is worth it, even without the keyword.

	Inline functions are best used for very short functions (like getters, setters, or simple calculations).

	Avoid using `inline` on large functions—it can increase code size, which may negatively affect performance due to cache misses.

## Lambdas

!!! info

	This section is considered more advanced at this point, as it requires knowledge of pointers and references.
	If you're not yet comfortable with those concepts, feel free to skip this section and return to it later once you're more confident.

Lambdas, also known as anonymous functions, let you define small, unnamed functions directly at the point of use.
They're mainly intended for short, one-off operations and behave like disposable function objects.
Lambdas are especially useful when you need to run a quick piece of logic without declaring a full function elsewhere.
They can also capture and use variables from the surrounding scope, making them both powerful and flexible.

### When to use lambdas

Whenever you have a function pointer, you can use a lambda in C++.
The best example of this is when we need to pass a function to an API or event handler so that it can call the function at a later time.
Lambdas are ideal for defining code that should run in the future.

This is useful when:

- The function requires data that isn’t available yet.
- We want to defer execution but still specify which function to call when needed.

```cpp title="main.cpp"
#include <iostream>
#include <vector>
#include <functional>

void ForEach(const std::vector<int>& values, const std::function<void(int)>& func) {
	for (const auto& value : values) {
		func(value);
	}
}

int main() {
	std::vector<int> values = {27, 64, 3, 89, 51};
	
	std::function<void(int)> lambda = [](int value) { 
        std::cout << value << ' '; 
    };

	ForEach(values, lambda);
}
```

``` title="output"
27 64 3 89 51 
```

The square brackets before a lambda expression are called capture clauses.
They allow the lambda to access variables from the surrounding scope using different capture modes, which are specified with various symbols.

| # | Capture Syntax             | Description                                                        |
| - |----------------------------|--------------------------------------------------------------------|
| 1 | `[]`                       | No capture – the lambda has no access to outer variables.          |
| 2 | `[variable]`               | Capture by value – creates a copy of `variable`.                   |
| 3 | `[&variable]`              | Capture by reference – modifies the original `variable`.           |
| 4 | `[=]`                      | Capture all by value – copies all outer variables used.            |
| 5 | `[&]`                      | Capture all by reference – references all outer variables.         |
| 6 | `[variable1, &variable2]`  | Mixed capture – `variable1` by value, `variable2` by reference.    |

These captured variables are read-only by default when captured by value.
If you want to modify them inside the lambda, you need to use the `mutable` specifier after the parameter list.

```cpp title="main.cpp"
#include <iostream>

int main() {
    int count = 10;

    auto lambda = [count]() mutable { 
        count++;  // Allowed because of `mutable`
        std::cout << "Lambda modified count: " << count << '\n';
    };

    lambda();

    std::cout << "Original count: " << count;
}
```

``` title="output"
Lambda modified count: 11
Original count: 10
```

### Lambda Use Case

A common use case for lambda expressions is within algorithm functions, such as `std::find_if`.
This function, which is part of the `<algorithm>` header file, is used to find a specific value in an iterable data structure based on a given condition.

```cpp title="main.cpp"
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
	std::vector<int> values = {1, 5, 4, 2, 3};

	auto it = std::find_if(values.begin(), values.end(), [](int value) { 
        return value > 3;
    });
						   
	std::cout << "First number bigger than 3: " << *it;
}
```

``` title="output"
First number bigger than 3: 5
```

## Recursion

Sometimes, the simplest way to solve a problem is to break it into smaller instances of the same problem.
This approach is called recursion.

A recursive function is a function that calls itself, either directly or indirectly, to handle a smaller piece of the original problem.
Each call gradually approaches a situation that can be solved directly, known as the base case.
Once the base case is reached, the function stops calling itself, and the results of the smaller problems are combined to produce the final solution.

Recursion is particularly useful for tasks that naturally involve repetition or hierarchy.
Examples include navigating tree-like structures, calculating mathematical sequences such as factorials or Fibonacci numbers, and solving puzzles or problems that can be divided into smaller, similar steps.

A recursive function generally consists of two essential components: the base case, which defines the condition under which the recursion stops, and the recursive case, where the function calls itself with a simpler or smaller version of the problem.
Without a base case, a recursive function would continue indefinitely, eventually consuming all available memory and causing the program to crash.

```cpp title="main.cpp"
#include <iostream>

int Factorial(int number) {
    if (number <= 1) { // base case: 0! = 1 and 1! = 1
        return 1;
    }
    return number * Factorial(number - 1); // recursive case
}

int main() {
    int value = 5;
    std::cout << "Factorial of " << value << " is " << Factorial(value);
}
```

``` title="output"
Factorial of 5 is 120
```

!!! info

	Recursion can feel unintuitive at first—and that’s completely normal.  
	The key is to stay patient and focus on the structure: every recursive function must make progress toward a simple base case.


## Questions

=== "question 1"

	What are the four key elements of a function creation?

=== "answer"

	1. Return type  
	2. Function name  
	3. Parameter list  
	4. Function body

---

=== "question 2"

	What keyword is used in C++ to indicate a function does not return a value?

=== "answer"

	`void`

---

=== "question 3"

	What is the difference between parameters and arguments?

=== "answer"

	Parameters are variables in the function declaration; arguments are the actual values passed when calling the function.

---

=== "question 4"

	How do default arguments work, and where should they be declared?

=== "answer"

	A default argument provides a value for a parameter if the caller does not supply one.
	Default values must appear in the function declaration, not the definition.
	They are processed from right to left, so once a parameter has a default, all parameters to its right must also have defaults.

---

=== "question 5"

	What does the `[[nodiscard]]` attribute do?

=== "answer"

	It warns the programmer if the return value of a function is ignored.

---

=== "question 6"

	What is a function specifiers? Give one common example.

=== "answer"

	A function specifiers is a keyword that modifies a function’s behavior or guarantees (not an attribute).  
	Example: `static`, which restricts a free function’s visibility to the translation unit where it is defined, effectively giving it internal linkage.

---

=== "question 7"

	How does function overloading work in C++?

=== "answer"

	Multiple functions can have the same name as long as their parameter types or counts differ.  
	The compiler chooses the correct version based on the arguments provided.

---

=== "question 8"

	What is an inline function, and when should it be used?

=== "answer"

	An inline function suggests to the compiler to replace the function call with its body to avoid call overhead.  
	Best for very short functions; avoid using on large functions.
	Thou for modern compilers its only a suggestion and they will desice on theyre own

---

=== "question 9"

	What is a lambda function, and what is the purpose of its capture list?

=== "answer"

	A lambda is an unnamed function defined at the point of use.  
	The capture list specifies which variables from the surrounding scope the lambda can access, and whether by value or reference.

---

=== "question 10"

	What are the two essential parts of a recursive function?

=== "answer"

	1. Base case — condition under which recursion stops  
	2. Recursive case — the function calls itself on a smaller or simpler problem


## Exercises

=== "exercise 1"

	Write a function named `Greet` that prints "Hello, C++ Learner!" to the console.  
	In main, call this function `n` times, where `n` is a number entered through the terminal.

=== "answer"

	```cpp
	#include <iostream>

	void Greet() {
		std::cout << "Hello, C++ Learner!\n";
	}

	int main() {
		int number;

		std::cout << "Enter number of repetetions: ";
		std::cin >> number;

		for (int i = 0; i < number; ++i) {
			Greet();
		}
	}
	```

---

=== "exercise 2"

	Write a function `Square` that takes a number parameter and returns its square.  
	In `main`, call it with the values loaded from the terminal, and print the results.

=== "answer"

	```cpp
	#include <iostream>

	[[nodiscard]] double Square(double a) {
		return a * a;
	}

	int main() {
		double number;
		
		std::cout << "Enter a number: ";
		std::cin >> number;
		
		std::cout << "Output: " << Square(number);
	}
	```

---

=== "exercise 3"

	Write a custom function `Pow` that takes a number and an exponent parameter and returns its power.
	Let the exponent default to 2, and ensure the function is accessible only within the current translation unit.
	In `main`, read the number and exponent values from the terminal, call the function, and print the result.

=== "answer"

	```cpp
	#include <iostream>

	[[nodiscard]] static double Pow(double base, int exponent = 2) {
		if (exponent == 0) {
			return 1.0;
		}

		double result = 1.0;

		for (int i = 0; i < exponent; ++i) {
			result *= base;
		}

		return result;
	}

	int main() {
		double number;
		int exponent;

		std::cout << "Enter a number: ";
		std::cin >> number;

		std::cout << "Enter an exponent: ";
		std::cin >> exponent;

		std::cout << "Default exponent: " << Pow(number) << '\n';
		std::cout << "Custom exponent: " << Pow(number, exponent);
	}
	```

---

=== "exercise 4"

	Write a recursive function `Fibonacci` that returns the n-th number in the Fibonacci sequence.  
	Read the value of `n` from the terminal in main, call the function, and print the result.

=== "answer"

	```cpp
	#include <iostream>

	int Fibonacci(int n) {
		if (n <= 2) {
			return 1;
		}

		return Fibonacci(n - 1) + Fibonacci(n - 2);
	}

	int main() {
		int number;

		std::cout << "Enter a positive number: ";
		std::cin >> number;

		std::cout << "Fibonacci " << number << "th is: " << Fibonacci(number);
	}
	```
