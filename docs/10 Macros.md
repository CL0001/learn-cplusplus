# 10 Macros

In the introduction chapter, we took a first look at how a C++ program comes together.
One important detail we touched on there was that the compiler is not the first tool that sees your code.
Before any C++ syntax is parsed or type-checked, your source files are passed through an earlier stage of the build pipeline: the preprocessor.

Macros are another feature provided by the preprocessor.
In C++, macros exist entirely in the form of preprocessor directives and allow us to “macro-fy” certain operations by rewriting code before compilation begins.
This makes macro expansion a purely text-editing stage—closer to an automated find-and-replace than to actual C++ logic.

Because macros work at the textual level, they come with serious limitations.
They have no type safety, ignore scope, and effectively erase symbol names before the compiler ever sees them.
As a result, macros can be difficult to debug and often obscure what the code is really doing.
For these reasons, modern C++ generally favors safer language features such as constexpr, inline functions, templates, and strongly typed enums.

This chapter therefore serves as an introduction to templates and metaprogramming, highlighting how C++ historically approached certain problems.
By studying macros first, we gain a better understanding of the motivation behind modern techniques—showing how higher-level language features improve safety, readability, and maintainability.

Even so, macros haven’t disappeared.
When used carefully, they can streamline repetitive code and enable patterns that are difficult—or impossible—to express otherwise.
Large frameworks like Qt and Unreal Engine rely heavily on macros to inject metadata, generate boilerplate, and extend user-defined types in a controlled and consistent way.

We’ll begin by separating macros into their two fundamental forms: object-like macros and function-like macros.

## Object-Like Macros

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

!!! note

    In modern C++, object-like macros should generally be avoided.
    Language features such as `constexpr` provide the same functionality with proper type safety, scoping, and far better diagnostics.
    Object-like macros remain mainly for legacy code and build-time configuration.

## Function-Like Macros

Function-like macros resemble functions in syntax, but they are not functions in any meaningful C++ sense.
Like all macros, they are handled entirely by the preprocessor and exist purely as text substitution rules.

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

A common attempt to fix this is to parenthesize every macro parameter and expression.

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

When the preprocessor expands the macro, the argument passed to that parameter is wrapped in double quotes and turned into a C-Style string.

```cpp title="main.cpp"
#include <iostream>

#define PRINT_VAR(x) std::cout << "Var Name: " << #x << '\n';

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
    std::cout << "Var Name: " << "value" << '\n';
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
The same rule applies to any valid tokens that can legally form a new identifier.

## Predefined Macros

In addition to user-defined macros, the compiler environment automatically provides a set of predefined ones.
These macros are always available and expand to information about the current source file, compilation context, or language version.

Because they are filled in by the compiler, predefined macros are particularly useful for debugging, logging, diagnostics, and tooling support.

| Macro                 | Description                                                                                   |
| --------------------- | --------------------------------------------------------------------------------------------- |
| `__FILE__`            | Expands to the current source file name.                                                      |
| `__LINE__`            | Expands to the current line number in the source file.                                        |
| `__DATE__`            | Expands to the compilation date as a string literal (e.g., `"Jan  6 2026"`).                  |
| `__TIME__`            | Expands to the compilation time as a string literal (e.g., `"14:30:00"`).                     |
| `__func__`            | Expands to the name of the current function (standard C++11).                                 |
| `__COUNTER__`         | Expands to an integer that increments each time it is used in a translation unit.             |
| `__cplusplus`         | Expands to a long integer representing the C++ standard version being used (e.g., `202402L`). |


For example, `__FILE__` and `__LINE__` are often combined to produce detailed error or log messages that point directly to the source location where an issue occurred.
`__func__` can provide additional context when tracing execution paths or debugging complex code.

The `__COUNTER__` macro expands to an integer value that increases with each use, which can be useful in advanced macro techniques where unique identifiers are required.

Finally, `__cplusplus` allows code to detect which version of the C++ standard the compiler is targeting.
This is commonly used to enable or disable features conditionally based on language support.

!!! Note

    Not all predefined macros are standardized.
    Some are compiler-specific and should be used with care when writing portable code.

## Conditional Compilation

The preprocessor can include or exclude sections of code based on whether certain macros are defined.
This is called conditional compilation, and it’s controlled using directives such as shown in a table below.

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

A common problem arises when a macro contains multiple statements and is used inside an if statement or other control flow.
Simply enclosing the macro in braces `{}` seems intuitive, but it can break the surrounding code if a semicolon follows.

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
#include <cstdio>

#define LOG(format, ...) \
    printf("[LOG] " format "\n", ##__VA_ARGS__);

int main()
{
    LOG("Player: %s Score: %d", "P1", 100);
    LOG("Game started");
}
```

| Concept       | Description                                                                      |
| ------------- | -------------------------------------------------------------------------------- |
| `...`         | In the macro definition, captures **all additional arguments** after the first.  |
| `__VA_ARGS__` | Replaced by **whatever extra arguments** are provided when the macro is invoked. |


The ## operator before `__VA_ARGS__` is a special trick that removes the trailing comma if no extra arguments are passed, allowing the macro to work correctly with zero or more parameters.

## The X Macro



## Questions



## Exercises

