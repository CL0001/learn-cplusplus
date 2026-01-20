# 10 Macros

In the introductory chapter, we took a first look at how a C++ program comes together.
One important detail we touched on was that the compiler is not the first tool that sees your code.
Before any C++ syntax is parsed or type-checked, your source files pass through an earlier stage of the build pipeline: the preprocessor.

This fact matters because, in C++, macros are provided entirely by the preprocessor through a set of special directives.
Rather than operating on C++ constructs, macros work by rewriting source code before compilation begins.
In practice, macro expansion is a purely textual transformation—closer to an automated find-and-replace than to actual C++ logic.

Because macros operate at the textual level, they come with significant limitations.
They have no awareness of types, ignore scope, and can erase symbol names before the compiler ever sees them.
As a result, macros are often difficult to debug and can obscure what the code is really doing.

!!! warning

    In modern C++ macros should be avoided.
    As they cause the code you write to differ from the code the compiler actually sees.
    This can introduce subtle and unexpected behavior, especially since macros have global scope.
    Language features such as `constexpr`, strongly typed enums, and inline functions provide the same functionality with proper type safety, scoping, and far better diagnostics.
    Object-like macros today remain mainly for legacy code and build-time configuration.

This chapter therefore serves as a bridge into templates and metaprogramming, showing how C++ historically approached certain problems.
By studying macros first, we gain useful context for understanding why modern techniques exist and how they improve safety, readability, and maintainability.

## Object Like Macros

The simplest form of a macro is the object-like macro.
Despite the name, it does not define an object in the C++ sense.
Instead, it defines a textual substitution rule.

```cpp title="example"
#define SOME_CONSTANT 10
```

Every time the preprocessor encounters the identifier `SOME_CONSTANT`, it replaces it with the literal text `10` before the compiler ever sees the code.
After preprocessing, the compiler has no knowledge that `SOME_CONSTANT` ever existed—it only sees the value `10`.

```cpp title="main.cpp"
#define SOME_CONSTANT 10

int main()
{
    int value = SOME_CONSTANT;
}
```

```cpp title="main.i"
int main()
{
    int value = 10;
}
```

## Function Like Macros

Function-like macros resemble functions in syntax, but they are not functions in the usual programming sense.
Like all macros, they are handled entirely by the preprocessor and exist purely as text-substitution rules.

```cpp title="main.cpp"
#include <iostream>

#define SQUARE(x) x * x

int main()
{
    int result = SQUARE(5);

    std::cout << result;
}
```

```cpp title="main.i"
/* ... entirety of iostream header file ... */

int main()
{
    int result = 5 * 5

    std::cout << result;
}
```

``` title="output"
25
```

At a glance, this appears to define a function that squares a value.
In reality, it defines a rule that replaces every occurrence of `SQUARE(something)` with the literal text `something * something`.
So far, this seems to work exactly as intended.
The problems start to appear as soon as we pass anything more complex than a single literal.

```cpp title="main.cpp"
#include <iostream>

#define SQUARE(x) x * x

int main()
{
    double result = 72 / SQUARE(5 + 1);

    std::cout << result;
}
```

```cpp title="main.i"
/* ... entirety of iostream header file ... */

int main()
{
    double result = 72 / 5 + 1 * 5 + 1

    std::cout << result;
}
```

``` title="output"
20
```

This result is almost certainly not what was intended.
The issue arises because the macro does not insert any parentheses.
The preprocessor performs a blind textual replacement, and normal operator precedence rules then apply to the expanded code.

A common attempt to fix and a rule of thumb is to parenthesize every macro parameter and expression.

```cpp title="main.cpp"
#include <iostream>

#define SQUARE(x) ((x) * (x))

int main()
{
    double result = 72 / SQUARE(5 + 1);

    std::cout << result;
}
```

```cpp title="main.i"
/* ... entirety of iostream header file ... */

int main()
{
    double result = 72 / ((5 + 1) * (5 + 1))

    std::cout << result;
}
```

``` title="output"
2
```

Unfortunately, some problems cannot be fixed with parentheses.

```cpp title="main.cpp"
#include <iostream>

#define SQUARE(x) ((x) * (x))

int main()
{
    int n = 5;
    double result = SQUARE(n++);

    std::cout << "n: " << n << '\n';
    std::cout << "result: " << result;
}
```

```cpp title="main.i"
/* ... entirety of iostream header file ... */

int main()
{
    int n = 5;
    double result = ((n++) * (n++))

    std::cout << "n: " << n << '\n';
    std::cout << "result: " << result;
}
```

``` title="output"
n: 7
result: 30
```

The increment operation runs twice, completely breaking the intended logic.
Depending on the C++ standard and evaluation rules, this may also result in undefined behavior.

!!! note

    This example highlights a fundamental limitation of macros: they do not evaluate expressions or preserve intent.  
    They simply duplicate text.
    Macros were not designed for performing mathematical operations; they exist primarily to manipulate code at the textual level.

## Stringizing Operator

The stringizing operator allows a macro parameter to be converted into a string literal.
It is written as a single `#` placed directly in front of a macro parameter.

When the preprocessor expands the macro, the argument passed to that parameter is wrapped in double quotes and turned into a C-style string.

```cpp title="main.cpp"
#include <iostream>

#define PRINT_VAR(x) std::cout << "Var name: " << #x << '\n'

int main()
{
    int value = 10;
    PRINT_VAR(value);
}
```

```cpp title="main.i"
/* ... entirety of iostream header file ... */

int main()
{
    int value = 10;
    std::cout << "Var name: " << "value" << '\n';
}
```

``` title="output"
Var name: value
```

This can be useful for logging, debugging, or diagnostic output where you want to display the name of a variable rather than its value.
As with all macros, this happens purely at the textual level—the compiler never sees the original macro parameter.

## Token Concatenation Operator

The token concatenation operator—written as `##`—allows two tokens in a macro to be joined together into a single token during macro expansion.

This is useful in situations where code follows strict naming conventions and you want to generate identifiers programmatically rather than writing repetitive boilerplate by hand.

```cpp title="example.cpp"
#define COMMAND_HANDLER(name) void File##name()

COMMAND_HANDLER(Open)
COMMAND_HANDLER(Close)
```

```cpp title="example.i"
void FileOpen();
void FileClose();
```

The preprocessor treats File and Open as separate tokens, then pastes them together into a single identifier.
The same rule applies to any tokens that can legally be combined to form a valid identifier.

## Predefined Macros

In addition to user-defined macros, the compiler environment automatically provides a set of predefined ones.
These macros are always available and expand to information about the current source file, compilation context, or language version.

Because they are supplied by the compiler or preprocessing environment, predefined macros are particularly useful.

| Macro                 | Description                                                                                      |
| --------------------- | ------------------------------------------------------------------------------------------------ |
| `__FILE__`            | Expands to the current source file name.                                                         |
| `__LINE__`            | Expands to the current line number in the source file.                                           |
| `__DATE__`            | Expands to the compilation date as a string literal (e.g., `"Jan  6 2026"`).                     |
| `__TIME__`            | Expands to the compilation time as a string literal (e.g., `"14:30:00"`).                        |
| `__func__`            | A compiler-provided identifier that evaluates to the name of the current function (not a macro). |


For example, `__FILE__` and `__LINE__` are often combined to produce detailed error or log messages that point directly to the source location where an issue occurred.
`__func__` can provide additional context when tracing execution paths or debugging complex code.

!!! Note

    Not all predefined macros are standardized.
    Some are compiler-specific and should be used with care when writing portable code.

## Conditional Compilation

The preprocessor can include or exclude sections of code based on whether certain macros are defined.
This is called conditional compilation, and it’s controlled using directives such as those shown in the table below.

| Directive | Purpose                                |
| --------- | -------------------------------------- |
| `#ifdef`  | Checks whether a macro is defined.     |
| `#ifndef` | Checks whether a macro is not defined. |
| `#endif`  | Marks the end of a conditional block.  |

```cpp title="main.cpp"
#include <iostream>

#define DEBUG_MODE

int main() {
    #ifdef DEBUG_MODE
        std::cout << "Debug mode is enabled.\n";
    #endif

    #ifndef RELEASE_MODE
        std::cout << "Release mode is not defined.\n";
    #endif
}
```

```cpp title="main.i"
/* ... entirety of iostream header file ... */

int main() {
    std::cout << "Debug mode is enabled.\n";
    std::cout << "Release mode is not defined.\n";
}
```

``` title="main"
Debug mode is enabled.
Release mode is not defined.
```

!!! note

    Every `#ifdef` or `#ifndef` must be closed with a corresponding `#endif`.
    Failing to do so will cause preprocessing or compilation errors.

## Multiline Macros

Sometimes a macro needs to expand to multiple lines of code.
To indicate that a macro continues onto the next line, we use a backslash `\` at the end of each line except the last.

```cpp title="main.cpp"
#define PRINT_SUM(a, b) \
    std::cout << "Sum: " << ((a) + (b)) << '\n';
```

Without the backslash, the preprocessor would treat each line as a separate directive or statement, leading to compilation errors.

A common problem with multiline macros arises when a macro contains multiple statements and is used inside a control-flow construct.
Simply enclosing the macro in braces `{}` may seem intuitive, but it can break the surrounding code if a semicolon follows, because the macro then expands to a compound statement that does not behave like a single statement.

```cpp title="example"
if (condition)
    MACRO();
```

The standard solution is to wrap the macro in a `do { ... } while(0)` loop
This pattern is widely used in professional codebases and library macros because it guarantees predictable expansion while keeping the preprocessor happy.

```cpp
#define MACRO_SAFE(x) do { \
    func1(x);              \
    func2(x);              \
} while (0)
```

## Variadic Macros

Variadic macros are function-like macros that can accept any number of arguments.
They are particularly useful for logging, debugging, or other situations where the number of parameters may vary.

```cpp title="main.cpp"
#include <iostream>

#define LOG(msg, ...)                         \
    std::cout << "[LOG] " << msg;             \
    ((std::cout << ' ' << __VA_ARGS__), ...); \
    std::cout << '\n';

int main()
{
    LOG("Player score:", "P1", 100);
    LOG("Game started");
}
```

``` title="output"

```

| Concept       | Description                                                                      |
| ------------- | -------------------------------------------------------------------------------- |
| `...`         | In the macro definition, captures all additional arguments after the first.      |
| `__VA_ARGS__` | Replaced by whatever extra arguments are provided when the macro is invoked.     |


The `__VA_ARGS__` mechanism allows the macro to work correctly with zero or more additional arguments.
This makes it possible to write flexible logging or debugging macros entirely in idiomatic C++.

## Questions

=== "question 1"

    What is a macro in C++?

=== "answer"

    A macro is a preprocessor directive that defines a rule for text substitution before compilation.

---

=== "question 2"

    What is the difference between object-like and function-like macros?

=== "answer"

    - Object-like macros resemble constants and do not take parameters.  
    - Function-like macros take parameters and resemble functions in syntax, but expand as text.

---

=== "question 3"

    What is the purpose of the stringizing operator `#` in a macro?

=== "answer"

    It converts a macro parameter into a string literal during preprocessing.

---

=== "question 4"

    How does the token concatenation operator `##` work?

=== "answer"

    It joins two tokens into a single token during macro expansion, allowing programmatic generation of identifiers.

---

=== "question 5"

    Why are parentheses important when defining function-like macros?

=== "answer"

    Parentheses prevent operator precedence issues by ensuring the macro arguments and the resulting expression evaluate correctly.

---

=== "question 6"

    What problem does the `do { ... } while(0)` pattern solve in macros?

=== "answer"

    It ensures that multi-statement macros behave like a single statement in any control-flow context, preventing syntax errors.

---

=== "question 7"

    What are variadic macros and how are they defined?

=== "answer"

    Macros that accept any number of arguments, defined with `...` in the macro parameter list, and using `__VA_ARGS__` to expand the extra arguments.

---

=== "question 8"

    What is a predefined macro? Give two examples.

=== "answer"

    Predefined macros are macros automatically provided by the compiler or preprocessor.  
    Examples:
    - `__FILE__` — expands to the current source file name.  
    - `__LINE__` — expands to the current line number.

## Exercises

=== "exercise 1"

    Write a function-like macro `CUBE(x)` that correctly computes the cube of an expression, handling operator precedence.

=== "answer"

    ```cpp
    #include <iostream>

    #define CUBE(x) ((x) * (x) * (x))

    int main() {
        int n = 5;

        std::cout << "Cube of " << n << " is " << CUBE(n) << '\n';
        std::cout << "Cube of (n + 1) is " << CUBE(n + 1) << '\n';
    }
    ```

---
