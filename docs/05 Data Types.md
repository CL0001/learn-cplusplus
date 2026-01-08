# 05 Data Types

By now, we've covered the absolute basics.
As part of that foundation, we introduced fundamental primitive data types, along with the kinds of values they can represent.

In the next few chapters, we’ll start working more closely with memory.
As we go deeper into the language, it becomes increasingly important to understand how memory behaves—especially when working with a lower-level language like C++.
At this point, it’s not just about writing code that works; it’s about knowing what’s really happening behind the scenes.

Now, it’s time to look more closely at how these basic types can be refined and extended into more specialized or optimized forms, depending on the needs of your data.

## Static Typing

C++ is a statically typed language.
This means that the type of every variable must be known at compile time.
In other words, the compiler needs to know what kind of data each variable will hold before the program even runs.

This gives us a few advantages.
First, it allows the compiler to catch many type-related errors early, before the program executes.
Second, it lets the compiler optimize memory usage and CPU instructions because it knows exactly how much space each variable takes and how to interpret it.

## Strong Typing

C++ is also considered strongly typed.
This means the language enforces rules about how different types can interact.
While you can convert between types using casts, the compiler won’t allow incompatible types to be mixed unintentionally.

For example, you can’t assign a double to a pointer to an int or automatically treat a `std::string` as a number.
The compiler forces you to be explicit, which prevents subtle bugs and unintended behavior.

In short, static typing tells the compiler what a variable is, and strong typing tells it what a variable cannot be.

## Primitive Data Types

In the introduction chapter, we introduced some of the primitive data types to kick-start our learning—but that was just the absolute core.
To give you more control and flexibility, C++ actually offers a broader range of primitive types, enabling more precise management of memory.

### Standard Integer Types

| Type        | Size (bytes)  | Signed Range                                            |
| ----------- | --------------| ------------------------------------------------------- |
| `char`      | 1             | -128 to 127                                             |
| `short`     | 2             | -32,768 to 32,767                                       |
| `int`       | 4             | -2,147,483,648 to 2,147,483,647                         |
| `long`      | 4 or 8        | Depends on the system                                   |
| `long long` | 8             | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 |

!!! note 

    On many 64-bit systems, `int` and `long` are both 4 bytes, but this can vary depending on the compiler and platform.

### Fixed-Width Integer Types

These types guarantee their size across different systems and compilers.
But to use the fixed-width integer types ending with `_t`, you need to include the `<cstdint>` header.

| Type        | Size (bytes)   | Signed Range                                                  |
| ----------- | -------------- | ------------------------------------------------------------- |
| `int8_t`    | 1              | -128 to 127                                                   |
| `int16_t`   | 2              | -32,768 to 32,767                                             |
| `int32_t`   | 4              | -2,147,483,648 to 2,147,483,647                               |
| `int64_t`   | 8              | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807       |
| `size_t`    | 4 or 8         | Represents the size of any object in memory (always unsigned) |

!!! note

    Types like `intptr_t` and `uintptr_t` are integer types capable of storing pointer values.
    They’re mostly useful in low-level or systems programming, such as writing memory managers or interfacing with hardware.
    But these data types will most C++ applications never need.

!!! note

    Similarly, `intmax_t` and `uintmax_t` represent the largest signed and unsigned integer types available on a given platform.
    While they sound powerful, they’re rarely used and are mainly helpful in highly portable libraries or template-heavy code.
    For most use cases, fixed-width types like `int64_t` or `size_t` are a better fit.

### Unsigned Integers

Signed integers divide their range between positive and negative numbers, effectively splitting their capacity in half.

However, in many cases, negative numbers are unnecessary or nonsensical.
For example, color channels such as RGBA are represented by values between 0 and 255, and quantities like array sizes or memory addresses can never be negative.

In these scenarios, using a signed integer like `int8_t` would be wasteful because half of the range goes unused.

To allocate the entire bit range exclusively for non-negative values you can use unsigned types.
For example, `uint8_t` ranges from 0 to 255, doubling the maximum value you can store in the same memory size.

| Type                 | Size (bytes) | Unsigned Range                  |
| -------------------- | ------------ | ------------------------------- |
| `unsigned char`      | 1            | 0 to 255                        |
| `unsigned short`     | 2            | 0 to 65,535                     |
| `unsigned int`       | 4            | 0 to 4,294,967,295              |
| `unsigned long`      | 4 or 8       | Depends on system               |
| `unsigned long long` | 8            | 0 to 18,446,744,073,709,551,615 |
| `uint8_t`            | 1            | 0 to 255                        |
| `uint16_t`           | 2            | 0 to 65,535                     |
| `uint32_t`           | 4            | 0 to 4,294,967,295              |
| `uint64_t`           | 8            | 0 to 18,446,744,073,709,551,615 |


### Floating-Point Types

| Type          | Size (bytes)       | Precision (Approx.)   | Range                          |
| ------------- | ------------------ | --------------------- | ------------------------------ |
| `float`       | 4                  | ~6-7 decimal digits   | ±1.5 × 10⁻⁴⁵ to ±3.4 × 10³⁸    |
| `double`      | 8                  | ~15-16 decimal digits | ±5.0 × 10⁻³²⁴ to ±1.8 × 10³⁰⁸  |
| `long double` | 8-16               | Varies                | Higher precision than `double` |

!!! note

    `double` is the default choice for floating-point arithmetic, providing more precision with minimal performance loss.
    However, when memory usage is critical, float may be preferred.
    To use floats, you must explicitly specify them by appending the value with an `f` suffix (e.g., 3.14f).
    Otherwise, the compiler treats floating-point literals as double by default.

### Character Types

So far, we have introduced only ASCII character encoding, which supports 128 characters (or 256 in extended ASCII).
However, ASCII is insufficient for representing the full range of characters used worldwide.

To address this, other encodings were developed, with UTF-8 becoming the most widely used due to its flexibility and backward compatibility with ASCII.

UTF-8 can represent a wide variety of characters, including:

- Latin characters with diacritics: `é, ñ, ü, č, š`
- Mathematical symbols: `∞, ∑, π, √`
- Currency symbols: `€, ¥, ₹, ₿`
- Emoji: `😀, ❤️, 🚀, 🔥`
- Non-Latin scripts: `你好 (Chinese), Привет (Russian), नमस्ते (Hindi), العربية (Arabic)`

| Type       | Size (bytes)        | Prefix  | Description                                       |
| ---------- | ------------------- | ------- | ------------------------------------------------- |
| `char`     | 1                   | None    | Holds a single character (typically ASCII)        |
| `wchar_t`  | 2 or 4              | `L'A'`  | Wide character (Unicode, size platform-dependent) |
| `char8_t`  | 1                   | `u8'A'` | UTF-8 character (C++20)                           |
| `char16_t` | 2                   | `u'A'`  | UTF-16 character (Unicode)                        |
| `char32_t` | 4                   | `U'A'`  | UTF-32 character (Unicode)                        |

These specialized types require prefixes before assigning literals to indicate their encoding.

Handling non-ASCII characters in the terminal can be tricky, especially on Windows.
To support UTF-8 output, you need to include the `<Windows.h>` header and adjust settings via its functions.
On Linux and macOS, this usually works without additional setup.

```cpp title="main.cpp"
#include <iostream>
#include <Windows.h>

#pragma execution_character_set("utf-8")

int main() {
	SetConsoleOutputCP(CP_UTF8);
	
	std::cout << "Testing unicode:\n";
	std::cout << " - English\n";
	std::cout << " - Ελληνικά\n";
	std::cout << " - Español\n";
 	std::cout << " - Русский\n";
	std::cout << " - aäbcdefghijklmnoöpqrsßtuüvwxyz\n";
}
```

``` title="output"
Testing unicode:
 - English
 - Ελληνικά
 - Español
 - Русский
 - aäbcdefghijklmnoöpqrsßtuüvwxyz
```

!!! note

    It’s  best to avoid non-ASCII characters in your source code unless necessary (e.g., in GUI applications).
    Non-ASCII characters can complicate compatibility across different environments and are rarely required in typical C++ code.

## Bitfields

!!! info

    This section is considered more advanced at this point, as it requires knowledge of user-defined types.
    If you're not yet comfortable with those concepts, feel free to skip this section and return to it later once you're more confident.

So far, all of the primitive data types we’ve introduced have used whole bytes as their smallest unit of storage.
That’s usually fine—but in low-level programming, we sometimes want more granular control.

In many real-world cases, a value only needs a few bits.
For example, a flag might only need to store true or false, a status code might fit in a range of 0–7, or a set of options might only require a handful of on/off switches.
Using a full `int` or even a full `char` for these cases wastes memory.

Bitfields allow us to pack multiple small values into a single integer type by specifying exactly how many bits each value should occupy.

Bitfields are declared inside a struct, using a colon followed by the number of bits allocated to the member.

```cpp title="main.cpp"
#include <iostream>

struct Flags {
    unsigned is_visible : 1;
    unsigned is_active  : 1;
    unsigned has_error  : 1;
};

int main() {
    Flags flags{1, 0, 1};

    std::cout << "Visible: " << flags.is_visible << '\n';
    std::cout << "Active: " << flags.is_active << '\n';
    std::cout << "Error: " << flags.has_error << '\n';
}
```

``` title="output"
Visible: 1
Active: 0
Error: 1
```

Each field here occupies only one bit, yet behaves like a normal integer when accessed.
Internally, the compiler packs these values together into a single storage unit.

Bitfields can also store small integer ranges, not just booleans.

```cpp title="example"
struct Color {
    unsigned red   : 8;
    unsigned green : 8;
    unsigned blue  : 8;
};
```

Each color channel is limited to 8 bits, giving it a range of 0–255—exactly what we want for RGB values.
This representation is both compact and expressive.

## Automatic Type Inference

!!! info 

    Before diving into this section, it’s helpful to have a grasp of collections of data, since type inference often involves more complex types like containers and iterators.

In C++, data types determine what kind of data a variable can hold and how much memory it uses.
Manually specifying types can sometimes be tedious or verbose—especially when the type is obvious from the context.
That’s where the auto keyword comes in: it lets the compiler automatically deduce the variable’s type based on its initializer, assignment, or the return value of a function.

Using auto can simplify your code and make it more concise, but it’s not without trade-offs.
Overusing it may reduce readability, making it harder to quickly understand what a variable represents.
It can also introduce subtle bugs if the inferred type doesn’t match your intentions, especially as your code evolves over time.

```cpp title="main.cpp"
#include <iostream>

std::string GetName() {
    return "name";
}

int main() {
    auto name = GetName(); // inferred as std::string
	
    // safe, std::string supports .size()
    std::cout << "String size: " << name.size();
}
```

``` title="output"
String size: 4
```

Here, name is correctly inferred as `std::string`.
However, if the return type of `GetName()` were changed to `const char*`, this code would no longer compile—C-style strings don’t support the `.size()` method.

This is a good example of how type inference can become fragile when the underlying return type changes.
In such cases, using an explicit type can help make your code more predictable.

One place where auto truly shines is in range-based for loops, where the element type is usually obvious and unlikely to change.

```cpp title="main.cpp"
#include <iostream>
#include <array>

int main() {
    std::array<int, 5> array{17, 48, 92, 64, 88};
	
    for (const auto& element : array) {
        std::cout << element << '\t';
    }
}
```

``` title="output"
17	48	92	64	88
```

Here, `auto` makes the loop cleaner and more readable, without sacrificing clarity or safety.
Since the type of element is tightly bound to the container, there's little risk of confusion or error.

## Volatile Specifier

The `volatile` keyword acts as a directive to the compiler’s optimizer: "Do not cache this value."
It informs the compiler that a variable's state can be altered by external factors—such as hardware, an interrupt, or a signal—that the compiler cannot see.
Consequently, the program must re-read the variable from memory every single time it is referenced, rather than relying on a previously stored value in a CPU register.

Normally, compilers aggressively optimize code by caching values in registers, reordering instructions, or eliminating repeated reads.
For most variables, this is exactly what we want—it makes programs faster. However, in some low-level scenarios, these assumptions are incorrect.

A variable should be declared volatile when its value can change due to external factors, such as:

- Hardware devices (Status registers)
- Signal handlers (Interacting with a running program)
- Memory-mapped I/O (Communicating with peripherals)

In these cases, every read and write must go directly to memory.

```cpp title="example"
#include <iostream>
#include <csignal>

volatile bool stop_flag = false;

void handle_signal(int signal) {
    stop_flag = true; 
}

int main() {
    // Register the handler for Ctrl+C (SIGINT)
    std::signal(SIGINT, handle_signal);

    std::cout << "Waiting for Ctrl+C...\n";

    while (!stop_flag) {
        // The compiler might optimize this into an infinite loop
    }

    std::cout << "Signal received! Exiting ...";
}
```

Without `volatile`, the compiler might assume that `stop_flag` never changes inside the loop and aggressively optimize the code—potentially turning the loop into an infinite one.
By marking the variable as `volatile`, we explicitly tell the compiler that its value may change unexpectedly, forcing it to reload the value from memory on every iteration.

It’s important to be precise about what it actually guarantees.

When a variable is declared `volatile`, the compiler is prevented from optimizing away reads and writes to that variable.
Every access is performed directly against memory rather than being cached in a register.
This ensures correct behavior when the value can change outside the normal flow of the program, such as through hardware or external events.

!!! note

    `volatile` should be used only in situations where the compiler’s normal assumptions about program flow do not hold.
    This typically includes low-level programming tasks such as interacting with hardware, working with memory-mapped registers, responding to signal handlers, or writing embedded or systems-level code.

    In regular application code, volatile is rarely needed.
    If you find yourself wanting to use it in ordinary logic, it’s often a sign that a different design or abstraction would be more appropriate.

## Register Specifier

Same as `volatile` the `register` specifier acts as a hint to the compiler suggesting that a variable should be stored in a CPU register rather than in memory.
The motivation was simple: accessing registers is faster than accessing memory, so keeping frequently used variables in registers could improve performance.

In early C and C++ compilers, this hint could sometimes make a noticeable difference.
Developers would mark loop counters or heavily used variables as register in an attempt to reduce memory access overhead.

```cpp title="example"
int main() {
    register int counter = 0;

    for (counter = 0; counter < 10; ++counter) {
        // work...
    }
}
```

However, modern compilers are far better at optimization than humans.
They perform sophisticated analysis to decide which variables should live in registers, how long they should stay there, and when spilling to memory is necessary.
As a result, the register keyword is almost always ignored by today’s compilers.

In fact, starting with C++17, `register` has been officially deprecated.
Using it no longer provides any performance benefit and may even trigger warnings in some compilers.

!!! note

    Although `register` no longer influences optimization, it still has one observable effect: you cannot take the address of a variable inside of a register.
    This restriction exists because a variable stored purely in a register does not have a stable memory address.

## Determining the Byte Size of Variables

We can get the byte size of any variable using the `sizeof` operator.

```cpp title="main.cpp"
#include <iostream>

int main() {
    double value = 145.32;

    std::cout << "Byte size of double is: " << sizeof(value);
}
```

``` title="output"
Byte size of double is: 8
```

## Type Casting

Sometimes, we need to reinterpret the data a variable holds by changing its type.
This process is called type casting.

But not all types in C++ are directly compatible, and trying to mix them without conversion can lead to errors or unexpected behavior.
Fortunately, C++ provides two main ways to perform type conversions.

- Implicit Conversion (Automatic):
  In many cases, the compiler can handle type conversions for you—this is called implicit conversion.
  For example, assigning an `int` to a `double` works automatically, because the compiler knows how to safely widen the value without losing information.
- Explicit Conversion (Casting):
  When a conversion isn’t safe or obvious, C++ requires you to perform an explicit cast.
  This tells the compiler, “Yes, I know what I’m doing—convert this anyway.” There are several ways to do this, and we’ll cover them.

### Conversions

In C++, conversions are implicit.
They occur when the compiler automatically converts one type to another without data loss, such as in arithmetic operations between different types or when assigning a `double` to an `int`.

```cpp title="main.cpp"
#include <iostream>

int main() {
    double pi = 3.14;
    int value = pi; // Implicit conversion: fractional part is lost

    char a = 'A';
    int b = a; // Implicit conversion: 'b' is assigned 65 (ASCII value of 'A')

    std::cout << "Double to integer conversion: " << value << '\n';
    std::cout << "Character to integer conversion: " << a << '\n';
}
```

``` title="output"
Double to integer conversion: 3
Character to integer conversion: 65
```

### C-Style Cast

Since C++ is built on top of C, it inherits nearly all of its features, including its casting techniques.
The C-style cast is one of the most commonly used casting methods, even in other programming languages, due to its simplicity and ease of use.
It is applied by prefixing a variable with a type in parentheses.

```cpp title="main.cpp"
#include <iostream>

int main() {
    int number = 42;
    void* ptr = &number; // Implicit conversion allowed (int* -> void*)
    
    int* ptr_num = (int*)ptr; // Explicit cast
    
    std::cout << "Value: " << *ptr_num << '\n';
}
```

``` title="output"
Value: 42
```

### C++ Casts

C-style casts are simple but potentially dangerous.
They allow almost any kind of conversion without checks, which can easily lead to undefined behavior.
That’s why C++ introduced its own casting operators—`static_cast`, `dynamic_cast`, `const_cast`, and `reinterpret_cast`.

It’s important to understand that C++ casts don’t add new capabilities beyond what C-style casts can do.
Instead, they provide safer and more controlled ways to perform conversions.
By using explicit keywords, C++ casts make your intent clear and help the compiler enforce stricter type checks. This reduces the risk of accidental or unsafe conversions.

Another benefit is maintainability.
Because each cast uses a specific keyword, it's easier to search for and reason about casting operations in your code.
That makes tasks like debugging or refactoring much more manageable.

In short, C++ casts offer the same power as C-style casts, but with better safety and clarity.
While C-style casts are still widely used, especially in older code, you should prefer C++ casts in modern C++ projects.
They’re more explicit, more readable overall.

#### Static Cast

C++ equivalent of a traditional C-style cast is the `static_cast`.
Unlike C-style casts, it provides better type safety by restricting certain conversions (e.g., removing `const` or casting between incompatible pointer types) that could lead to undefined behavior.

```cpp title="main.cpp"
#include <iostream>

int main() {
    int value = 42;
    void* void_ptr = &value;

    int* int_ptr = static_cast<int*>(void_ptr);

    std::cout << "Value: " << *int_ptr << '\n';
}
```

``` title="output"
Value: 42
```

#### Dynamic Cast

!!! info

    It is recommended to go through user-defined structures before proceeding further, as the upcoming examples will focus on class behavior and involve casting between classes.

`dynamic_cast` adds another layer of safety to `static_cast` by performing runtime validation.
If the cast is unsuccessful, it returns a `nullptr`.
Unlike `static_cast`, which is evaluated at compile-time, `dynamic_cast` is evaluated at runtime, making it slightly slower but significantly safer when working with polymorphic class hierarchies.

Imagine we have three classes in our game: `Entity` (the base class), `Player` and `Enemy` (both derived from `Entity`).
Casting a `Player` to `Entity` is straightforward, as `Player` already inherits all the properties of `Entity`, only losing its own unique members in the process.

The challenge arises when trying to cast an `Entity` to a `Player`.
The compiler has no way of knowing whether the `Entity` instance actually represents a `Player`, an `Enemy`, or just a base `Entity`.
With `static_cast`, the compiler trusts the programmer, but if the cast is incorrect, accessing `Player`-specific members can lead to a crash.

This is where `dynamic_cast` becomes valuable.
If we have an `Entity` instance that is actually an `Enemy` and try to cast it to `Player` using `dynamic_cast`, the cast will fail and return `nullptr`, allowing us to safely check the result before accessing `Player`-specific members.

```cpp title="main.cpp"
#include <iostream>

class Entity {
public:
    // Necessary for dynamic_cast to work (ensures a vtable)
    virtual void PrintName() {
    }
};

class Player : public Entity {
};

class Enemy : public Entity {
};

int main() {
    // Implicit Cast (correct): Upcasting is safe
    // Player* player = new Player();
    // Entity* entity = player;

    // Implicit Cast (incorrect): Attempting to cast an Enemy to a Player
    Entity* unknown_entity = new Enemy();

    // Explicit C-style Cast (incorrect): It is unsafe without validation
    // Player* player_cast = (Player*)unknown_entity;

    // Correct usage of dynamic_cast
    Player* safe_player = dynamic_cast<Player*>(unknown_entity);

    // We can perform a simple check because dynamic_cast returns nullptr if the cast fails
    if (safe_player) {
        std::cout << "Cast successful";
        safe_player->PrintName();
    } else {
        std::cout << "Cast failed";
    }
}
```

``` title="output"
Cast failed
```

#### Constant Cast

Constant cast serves a single purpose: adding or removing the `const` qualifier from a variable.
This might sound simple, but it has important use cases, especially when dealing with legacy code, function parameters, or APIs that don’t support `const` properly.

Imagine you have a `const` variable, but you need to modify its value.
Normally, C++ prevents this to ensure safety, but `const_cast` allows you to override this restriction.
However, this should only be done when you’re absolutely sure it’s safe, modifying truly `const` values leads to undefined behavior.

```cpp title="main.cpp"
#include <iostream>

void ChangeValue(const int* ptr) {
    int* modifiable_ptr = const_cast<int*>(ptr);
    *modifiable_ptr = 99;
}

int main() {
    int value = 42;

    ChangeValue(&value);

    std::cout << "Value: " << value << '\n';
}
```

``` title="output"
Value: 99
```

#### Reinterpret Cast

!!! info

    It is recommended to go through user-defined types before proceeding further, as the upcoming examples will focus on class behavior and involve casting between classes.

Reinterpret cast is the most dangerous of all C++ casts, as it allows converting any pointer type to any other pointer type, even if they are unrelated.
Unlike `static_cast`, which ensures some level of type safety, `reinterpret_cast` simply reinterprets the memory without any validation.

The `reinterpret_cast` is commonly used in low-level programming, such as bit manipulation, working with hardware registers, or handling network protocols.
It allows for type punning, which enables treating a block of memory as a different type.
However, this cast should be used with caution, as improper usage can lead to undefined behavior and memory corruption.

```cpp title="main.cpp"
#include <iostream>

struct Data {
    int x;
    float y;
};

int main() {
    Data data = { 42, 3.14f };
    
    int* int_ptr = reinterpret_cast<int*>(&data);
    
    std::cout << "Interpreted int: " << *int_ptr << '\n';
}
```

``` title="output"
Interpreted int: 42
```

`reinterpret_cast` can also convert a pointer into an integer type (`uintptr_t`), which may be useful in low-level debugging or custom memory management, but should generally be avoided in high-level C++ code.

```cpp title="main.cpp"
#include <iostream>
#include <cstdint>

int main() {
    int value = 42;
    int* ptr = &value;

    uintptr_t address = reinterpret_cast<uintptr_t>(ptr);

    std::cout << "Memory address: " << address << '\n';
}
```

``` title="output"
Memory address: 140727907748748
```

### When to Cast

Casting should be avoided whenever possible.
If explicit conversion is required, consider alternative approaches before resorting to a cast.
C++ provides safer mechanisms, such as uniform initialization syntax and automatic type inference, which can help minimize unnecessary conversions.

General Guidelines for Casting:

- Prefer explicit casting over implicit casting whenever possible.
- Use uniform initialization (`T2{T1}`) and `auto` to reduce unnecessary implicit conversions.
- During code reviews, scrutinize casts carefully and require clear documentation explaining their necessity. Polymorphism is a valid justification.
- If casting is unavoidable, document its usage with well-written comments next to each cast or group of casts.
- Avoid C-style casts (`(T)expression`), as they are unsafe, bypass type checks, and make code harder to maintain.
- Do not misuse `reinterpret_cast` unless you fully understand the potential consequences.
- Do not remove `const` qualifiers unless absolutely necessary, as it can lead to unintended side effects.

## Compile-Time Constructs

In the introduction chapter, we introduced the idea of constants—variables that are meant to remain unchanged throughout a program's execution.
However, we later saw that `const` can be bypassed with `const_cast`, which means it doesn't provide absolute immutability.
This is generally a bad design choice, as it violates the principle that constants should truly be constant.

To ensure a variable or function is genuinely immutable and can be evaluated at compile time, modern C++ provides the `constexpr` keyword.
This is also a type-safe alternative to the old preprocessor macros like `#define` and offers a more reliable way to express intent in your code.

### Constexpr

The `constexpr` keyword guarantees that a variable or function is evaluated at compile time.
This is especially useful for constants, mathematical functions, and lookup tables that should not change during the program’s lifetime.

Unlike `const`, which merely promises not to modify a variable after initialization, `constexpr` enforces that the value is computed before runtime.
It Allows the compiler to optimize your code by "hard baking" the result directly into the binary.

```cpp title="main.cpp"
#include <iostream>

// Compile-time constant
constexpr int value = 20;

// Compile-time function
constexpr int Add(int a, int b) {
    return a + b + value;
}

int main() {
    std::cout << Add(5, 10) << '\n';

    /*
        After compilation, it becomes this:
        std::cout << 5 + 10 + 20 << '\n';
    */
}
```

``` title="output"
35
```

### Consteval and Constinit

!!! warning

    This is a feature of C++20, so make sure you are using a compatible compiler version to avoid build errors.

Modern compilers have become quite good at determining which expressions can be evaluated at compile time, making `constexpr` slightly less explicit than it once was.
However, if you need to guarantee that a function or variable is evaluated at compile time, you should use `consteval` for functions and `constinit` for variables.

- `consteval` ensures a function is always evaluated at compile time.
It cannot accept runtime values as arguments, making it ideal for functions that should never execute at runtime.
- `constinit` guarantees that a variable is initialized at compile time.
It can only be used with static or thread-local variables and requires a constant expression as its initializer, like a literal or a `consteval` function.
It cannot be used in function scope, as it is specifically intended for variables with static storage duration.

Keep in mind that `constinit` cannot be used with automatic (local) variables, and trying to initialize it with a runtime value will result in a compile error.

```cpp title="main.cpp"
#include <iostream>

consteval int GetValue() {
	return 42;
}

constinit int global_value = GetValue();

int main() {
	std::cout << global_value << '\n';
}
```

``` title="output"
42
```

## Padding and Alignment

!!! info 

    This section covers advanced memory management concepts, including how data is organized in memory and how to optimize it.
    This knowledge is generally not required unless you are working in performance-critical or low-level systems.

Padding and alignment refer to how data is organized in memory.
Every data type has specific alignment requirements, meaning it must be stored at memory addresses divisible by its size to ensure efficient access.
If these requirements are not met, the compiler adds padding to fill the gaps, potentially wasting memory.

For example, an `int` typically requires 4-byte alignment, meaning it must start at a memory address divisible by 4.

```
byte: 00 -> If an `int` is placed here, it will occupy 4 bytes
byte: 01
byte: 02
byte: 03
byte: 04 -> The next `int` can start here
byte: 05
byte: 06
byte: 07
byte: 08 -> Or here
```

We can retrieve the alignment requirement of any data type by including the `<type_traits>` header, which provides the `std::alignment_of_v<T>` operator.
The `_v` suffix stands for "value" and is a shorthand that gives you the result directly, rather than as a type like `int`.

```cpp title="main.cpp"
#include <iostream>
#include <type_traits>

int main() {
    std::cout << "Alignment of integer: "<< std::alignment_of_v<int> << '\n';
}
```

``` title="output"
Alignment of integer: 4
```

This concept is especially important when creating structs.
If the alignment is not correct, the compiler will automatically add padding to ensure proper alignment, potentially wasting otherwise usable memory.

!!! tip

    To detect padding and alignment issues, you can use Clang's `-Wpadded` compile flag, which will emit warnings for potentially inefficient struct layouts.
    Additionally, clang-tidy offers checks like `clang-analyzer-optin.performance.Padding` for deeper analysis.
    Unfortunately, MSVC and GCC do not have direct equivalents, so manual inspection or static analysis tools may be necessary for those compilers.

```cpp title="main.cpp"
#include <iostream>

struct Entity {              // Total: 19 bytes 
	char rank;               // 1 byte
	int level;               // 4 bytes
	char type;               // 1 byte
	int health;              // 4 bytes
	long long experience;    // 8 bytes
	char status;             // 1 byte
};

int main() {
	std::cout << "Size of entity: " << sizeof(Entity) << '\n';
}
```

``` title="output"
Size of entity: 32
```

As we can see from this example, the original struct wastes 13 bytes of memory due to padding, resulting in approximately 75% overhead.
This happens because each member must be aligned according to its alignment requirements, leaving gaps in memory.

```
byte: 00 -> 'rank'       (1 byte)  
byte: 01 -> padding  
byte: 02 -> padding  
byte: 03 -> padding  
byte: 04 -> 'level'      (4 bytes) - 3 bytes lost  
...  
byte: 08 -> 'type'       (1 byte)  
byte: 09 -> padding  
byte: 10 -> padding  
byte: 11 -> padding  
byte: 12 -> 'health'     (4 bytes) - 3 bytes lost  
...  
byte: 16 -> 'experience' (8 bytes) - 4 bytes lost  
...  
byte: 24 -> 'status'     (1 byte)  - 3 bytes lost  
byte: 25 -> padding  
byte: 26 -> padding  
byte: 27 -> padding
```

We can significantly reduce padding by reordering the struct members from largest to smallest data types.
This approach minimizes internal fragmentation, though some padding may still remain at the end, as it is platform-dependent and cannot be completely eliminated.

```cpp title="main.cpp"
#include <iostream>
#include <type_traits>

struct Entity {              // Total: 19 bytes 
	long long experience;    // 8 bytes
	int level;               // 4 bytes
	int health;              // 4 bytes
	char rank;               // 1 byte
	char status;             // 1 byte
	char type;               // 1 byte
};

int main() {
	std::cout << "Size of entity: " << sizeof(Entity) << '\n';
}
```

``` title="output"
Size of entity: 20 or 24
```

### Difference Between Alignment and Size 

While `sizeof` gives you the total memory size (in bytes) that a type occupies—including any internal padding—`std::alignment_of_v<T>` tells you the alignment requirement of the type T.

| Expression               | Description                                   |
| ------------------------ | --------------------------------------------- |
| `sizeof(T)`              | How much space an object of type `T` takes up |
| `std::alignment_of_v<T>` | How that object must be aligned in memory     |

Alignment ensures that variables are placed in memory in a way that’s efficient for the CPU.
On many systems, certain types must be aligned to memory addresses divisible by 4, 8, 16, etc.
Misalignment can lead to performance penalties or even runtime errors on strict architectures.

## Type Punning

!!! info

    This section covers advanced memory management concepts, including how data is organized in memory and how to optimize it.
    This knowledge is generally not required unless you are working in performance-critical or low-level systems.

C++ is considered a strongly typed language, but it still allows for certain implicit conversions—such as converting integers to floating-point numbers or pointers between compatible types.
While variables in C++ have explicitly defined data types, the language also provides low-level memory access, giving us the ability to reinterpret data in ways that would typically be restricted in stricter languages.
This flexibility comes from the ability to directly access and manipulate memory, allowing a block of memory to be treated as a different type when necessary.

```cpp title="main.cpp"
#include <iostream>

int main() {
	int a = 50;
	double value = *(double*)&a;

	std::cout << "Value: " << value << '\n'; 
}
```

``` title="output"
Value: 2.47033e-322
```

The reason why the output is so strange is that we pass the address of `a`, which is an integer (4 bytes), to an double pointer.
The pointer tells the compiler that the value under this address is a `double` (8 bytes).
Therefore, when we try to output it, the program reads 4 bytes past our value and accesses some unknown memory.

```cpp title="main.cpp"
#include <iostream>

struct Entity {
	int x;
    int y;
};

int main() {
	Entity e{5, 8};
	
	int result = *(int*)((char*)&e + 4);
	
	std::cout << "Value: " << result << '\n';
}
```

``` title="output"
Value: 8
```

What is done in this example is called raw memory manipulation.
This is also one of the reasons why C++ is such a powerful language: it can manipulate memory very easily and freely.
While the code in this example should probably never exist in a real-world application, it demonstrates how data types are represented in memory and how they can be manipulated.

However, what can be useful is the representation and access to memory.
In the last example, we moved through the struct's values using raw memory manipulation, but it could also be easily interpreted as a C-style array, for example.

```cpp title="main.cpp"
#include <iostream>

struct Entity {
    int x, y;
	
    int* CreateArrayFromEntity() {
        return &x;
    }
};

int main() {
    Entity e{5, 8};
    
    int* array = e.CreateArrayFromEntity();
    
    std::cout << "Second array value: " << array[1] << '\n';
}
```

``` title="output"
Second array value: 8
```

Here, we just manipulate it using indices.
This is possible because the `int` pointer is pointing to the address of `x`, and if it moves by four bytes (which is specified by the `[1]`), it will then point to the variable `y`.
This works because structs are also organized in contiguous memory.

## Dynamic Typing

C++ is known as a statically-typed language; however, it also provides mechanisms that allow for a certain level of dynamic typing, meaning the data types of variables can be determined at runtime.

### Optional Data

!!! warning

    This is a feature of C++17, so make sure you are using a compatible compiler version to avoid build errors.

What often happens when reading from a file or another dynamic source is that the function responsible needs to return a value if it finds some text or not.
The common approach is to return a signal that nothing was found — for example, returning an empty string if a file is empty or missing content.
However, this isn't ideal, as it makes it hard to distinguish between missing data and intentionally empty data.

This is where `std::optional` becomes useful; to use it, you must include the `<optional>` header.
It is a type that holds a value that may or may not be present, stored on the stack.
It provides a clear and type-safe way to represent optional data, making your code more expressive and reliable when handling uncertain results.

```cpp title="example"
#include <iostream>
#include <fstream>

std::string ReadFileToString(const std::string& path) {
	std::fstream stream(path);
	if (stream) {
		std::string result;
		
		// Code for reading a file
		
		stream.close();
		
		return result;
	}
	
	return "";
}

int main() {
	std::string data = ReadFileToString("data.txt");
	if (data != "") {
		// code ...
	}
}
```

This approach isn’t necessarily wrong, but it’s not ideal either—it’s somewhat clunky and lacks clarity.
It's similar to adding a separate flag variable to indicate whether the output was successful.

These kinds of workarounds are unnecessary in modern C++.
The cleaner solution is to use `std::optional`, which directly expresses the idea of a value that might not be present.

```cpp title="example"
#include <iostream>
#include <fstream>
#include <optional>

std::optional<std::string> ReadFileToString(const std::string& path) {
	std::fstream stream(path);
	if (stream) {
		std::string result;
		
		// Code for reading a file
		
		stream.close();
		
		return result;
	}
	
	return {};
}

int main() {
	std::optional<std::string> data = ReadFileToString("data.txt");
    if (data) {
        std::cout << "File was read successfully: " << *data << '\n';
    } else {
        std::cout << "File was not read successfully!" << '\n';
    }
}
```

In this example, the use of `std::optional` explicitly indicates that the returned data may or may not be present.

The check for the presence of data is clean and straightforward: we simply check if the `std::optional` contains a value.
If it does, we dereference the optional variable to access the data.
If the file reading process fails, the function returns an empty `std::optional` (i.e., the default constructor), indicating the absence of data.

Using `std::optional` is often preferable to returning an empty string, as it avoids unnecessary string construction and copying, effectively representing a "no value" state without the overhead of a full string object.

If the file could not be read and you want to set the data to a default value, you can use the `.value_or` method. This ensures that if `data` contains a value, it will be used; otherwise, the provided default value will be assigned.

```cpp title="example"
std::string value = data.value_or("default value");
```

Beyond its primary use case of representing "no value" or a missing result, `std::optional` is also extremely useful when you need to delay initialization of a variable, especially in situations where immediate construction isn’t possible or desirable.

For example, in game development or GUI applications, you often have to wait until certain systems are initialized (like a rendering context or asset loader) before you can safely create certain objects.
Instead of using raw pointers, manual flags, or heap allocation, you can wrap your object in a `std::optional` and initialize it later with the `.emplace()` method when the conditions are right.

```cpp title="example"
#include <optional>

struct Texture {
    // code ... 
};

std::optional<Texture> player_texture;

void LoadResources() {
    player_texture.emplace(/* load texture here */);
}
```

### Multi-Type Data Holder

!!! warning

    This is a feature of C++17, so make sure you are using a compatible compiler version to avoid build errors.

What often happens when handling inputs from external sources—like files, command-line arguments, or network responses—is that the data may come in different types.
For example, a value might be a string in one case, a number in another, or something else entirely.

This is where `std::variant` becomes useful. It allows you to store a value that could be one of several types, all in a single, type-safe variable.
Think of it as a modern, type-safe union—making your code more flexible without sacrificing safety or clarity.

To use `std::variant`, you must include the `<variant>` header.

Also the union is still useful for low-level optimization but does not offer type safety. When absolute optimization is not required, `std::variant` should be preferred.

```cpp title="main.cpp"
#include <iostream>
#include <variant>

int main() {
    std::variant<std::string, int> data;

    data = "Random";
    std::cout << "String variant: " << std::get<std::string>(data) << '\n';

    data = 3;
    std::cout << "Integer variant: " << std::get<int>(data) << '\n';
}
```

``` title="output"
String variant: Random
Integer variant: 3
```

This is a simple example of using `std::variant` and how to retrieve its data using `std::get<T>()`.
However, if the stored type doesn't match the requested type, it throws a bad variant access exception.
To avoid this, you can use `std::get_if<T>()` to safely access the value only if it holds the correct type.

```cpp title="example"
// Initializer inside the statement is a C++17 feature
if (auto value = std::get_if<std::string>(&data)) {
    std::cout << "String value: " << *value << '\n';
} else {
    std::cout << "Data does not hold a string." << '\n';
}
```

### Any Data Type

!!! warning 

    This is a feature of C++17, so make sure you are using a compatible compiler version to avoid build errors.

What often happens in flexible systems—like plugin architectures, event dispatchers, or scripting layers—is the need to store and pass around values of any type, without knowing in advance what that type will be.

This is where `std::any` becomes useful. It is a type-safe alternative to `void*`, allowing you to store any value while preserving its type information.

To use `std::any`, include the `<any>` header.

```cpp title="main.cpp"
#include <iostream>
#include <any>

int main() {
    std::any data;

    data = 3;
    std::cout << "Now holding integer: " << std::any_cast<int>(data) << '\n';

    data = std::string("String");
    std::cout << "Now holding string: " << std::any_cast<std::string>(data) << '\n';
}
```

``` title="output"
Now holding integer: 3
Now holding string: String
```

At first glance, `std::any` may seem similar to `std::variant`, with the key difference being that `std::any` doesn't require you to specify possible types in advance.
However, this flexibility is also its biggest drawback—`std::variant` is often the better choice because its explicitly defined type list ensures type safety at compile time.

For example, in the code above, we had to assign the string using `std::string("String")`.
If we had written just `"String"`, it would have been stored as a `const char*`, which could lead to a type mismatch when retrieving it with `std::any_cast<std::string>(data)`.
With `std::variant`, this problem doesn't arise—because the type must be one of the predefined options, `"String"` would be implicitly converted to `std::string` when matched against the allowed types.

Additionally, `std::variant` can be more memory-efficient when working with large data types, especially if you're passing them by reference.
`std::any`, on the other hand, always stores a copy of the value, which can introduce unnecessary overhead.

In short, `std::any` should only be used when you truly need type-erased behavior and cannot determine the set of possible types in advance.
Even then, the design should be reconsidered—`std::variant` is in most cases the safer alternative.

## RTTI (Run-Time Type Identification) 

Run-Time Type Identification (RTTI) is a feature in C++ that allows you to determine the actual type of an object at runtime.
This is useful in scenarios involving inheritance and polymorphism, particularly when working with base class pointers or references that may point to derived class objects.

RTTI supports two key tools:

- `typeid` operator
- `dynamic_cast` operator

### Type Id

The `typeid` operator returns a reference to a `std::type_info` object, which contains information about the type of the expression passed to it.
To use `typeid`, include the `<typeinfo>` header.

```cpp title="main.cpp"
#include <iostream>
#include <typeinfo>

class Base { 
	virtual void dummy() {
    }
};

class Derived : public Base {
};

int main() {
    Base* base_ptr = new Derived;
    
    std::cout << "Type: " << typeid(*base_ptr).name(); // Output: Derived
    
    delete base_ptr;
}
```

``` title="output"
Type: 7Derived
```

Although the output may look odd at first glance, `7Derived` is the mangled name of the `Derived` type.
In many C++ compilers (like GCC or Clang), `typeid(...).name()` returns an encoded string where:

- `7` indicates the number of characters in the class name (Derived has 7 letters).
- Derived is the actual class name.

This process is called name mangling, and it's used by compilers to encode type information for linking and overloading support.

### Considerations When Using RTTI

- RTTI increases memory overhead because types need to store additional information about themselves.
- Each time a `dynamic_cast` is performed, a runtime check is required to validate whether the cast is correct.

It is possible to disable RTTI, but the process varies depending on the compiler.
When RTTI is turned off, the compiler will typically generate a warning after building the project, indicating that RTTI is disabled.
Additionally, `dynamic_cast` will exhibit unpredictable behavior, potentially leading to crashes or undefined results.

## Questions

=== "question 1"

    What does it mean for C++ to be a statically typed language, and how does this differ from dynamic typing?

=== "answer"

    C++ is statically typed, meaning the type of every variable must be known at compile time.  
    In contrast, dynamically typed languages determine types at runtime.

---

=== "question 2"

    Explain the difference between static typing and strong typing in C++.  
    Why are these two concepts often confused?

=== "answer"

    Static typing describes when types are known (at compile time), while strong typing describes how strictly the language enforces type rules.

---

=== "question 3"

    Why are fixed-width integer types such as `int32_t` preferred in portable code over types like `int` or `long`?

=== "answer"

    Fixed-width integer types such as int32_t guarantee their size across different systems and compilers.  
    This makes them more predictable and portable than types like `int` or `long`, whose sizes depend on the platform.

---

=== "question 4"

    In what situations is it more appropriate to use an unsigned integer instead of a signed one?  
    Give two concrete examples.

=== "answer"

    Unsigned integers should be used when negative values are unnecessary or nonsensical.  
    Examples include color channels like RGBA values, which range from 0 to 255, and quantities such as array sizes or memory addresses, which are always non-negative.

---

=== "question 5"

    What problem do bitfields solve, and why are they commonly used in low-level or memory-sensitive code?

=== "answer"

    Bitfields allow multiple small values to be packed into a single integer by specifying how many bits each value occupies.  
    They are useful when values only need a few bits, such as flags or small ranges, and using full bytes would waste memory.

---

=== "question 6"

    What does the `auto` keyword do in C++, and what potential risks can arise if it is overused?

=== "answer"

    The `auto` keyword lets the compiler deduce a variable’s type based on its initializer or return value.  
    Overusing `auto` can reduce clarity and make code fragile if the inferred type changes unexpectedly.

---

=== "question 7"

    What guarantees does the `volatile` specifier provide?

=== "answer"

    The volatile specifier tells the compiler not to cache a variable’s value and to always read it directly from memory.

---

=== "question 8"

    Why has the `register` keyword become obsolete in modern C++, and what limitation does it still impose on variables declared with it?

=== "answer"

    The `register` keyword was originally a hint suggesting that a variable should be stored in a CPU register for performance.  
    Modern compilers perform this optimization automatically, making the keyword obsolete and officially deprecated in C++17.  
    Its only remaining effect is that you cannot take the address of a variable declared as register.

---

=== "question 9"

    Explain the difference between `sizeof(T)` and `std::alignment_of_v<T>`.  
    How do padding and alignment affect struct memory layout?

=== "answer"

    `sizeof(T)` returns the total number of bytes an object of type T occupies, including any padding.  
    `std::alignment_of_v<T>` returns the alignment requirement for that type in memory.  
    Padding is added by the compiler to satisfy alignment rules, which can increase the size of structs and affect memory.

---

=== "question 10"

    Compare `std::optional`, `std::variant`, and `std::any`.  
    In what situations should each be used, and why is `std::variant` often preferred over `std::any`?

=== "answer"

    `std::optional` represents a value that may or may not be present.  
    `std::variant` stores one value from a predefined set of types in a type-safe way.  
    `std::any` can store a value of any type, but at the cost of weaker compile-time guarantees and potential overhead.  
    In most cases, `std::variant` is preferred over `std::any` because it enforces type safety at compile time.


## Exercises

