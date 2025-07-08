# 05 Data Types

By now, we've covered the absolute basics.
As part of that foundation, we introduced fundamental primitive data types such as integers, characters, and floating-point numbers, along with the kinds of values they can represent.

In the next few chapters, we’ll start working more closely with memory.
As we go deeper into the language, it becomes increasingly important to understand how memory behaves—especially when working with a lower-level language like C++.
At this point, it’s not just about writing code that works; it’s about knowing what’s really happening behind the scenes.

As part of our earlier foundation, we introduced primitive data types, which act as the building blocks for all data structures.
Now, it’s time to look more closely at how these basic types can be refined and extended into more specialized or optimized forms, depending on the needs of your data.

## Static and Strong Typing

C++ is a statically typed language.
This means that the data type of every variable must be explicitly declared and known at compile time.
In other words, the compiler needs to know what kind of data each variable will hold before the program runs.

This requirement brings a few important benefits.
First, it allows the compiler to catch many type-related errors early—before the code ever executes.
Second, it enables more efficient memory usage and better optimization.
After all, the data type of a variable determines how much memory it occupies and how that memory is interpreted during execution—which in turn affects how the program interacts with the system’s memory layout.

Compared to dynamically typed languages such as Python and JavaScript, where types are resolved at runtime.
C++’s static typing provides stronger guarantees and performance benefits—though at the cost of some flexibility.

C++ is also considered a strongly typed language.
This refers to how strictly the language enforces type rules.
While C++ allows you to perform type conversions (also called casting), the compiler won't let you mix incompatible types without an explicit cast.
This helps prevent unintended behavior or subtle bugs caused by incorrect assumptions about how data is represented in memory.

## Primitive Data Types

In the introduction chapter, we introduced some of the primitive data types to kick-start your learning—but that was just the absolute core.
To give you more control and flexibility, C++ actually offers a broader range of primitive types, enabling more precise management of memory and data.

### Standard Integer Types

| Type        | Size         | Signed Range                                            |
| ----------- | ------------ | ------------------------------------------------------- |
| `char`      | 1 byte       | -128 to 127                                             |
| `short`     | 2 bytes      | -32,768 to 32,767                                       |
| `int`       | 4 bytes      | -2,147,483,648 to 2,147,483,647                         |
| `long`      | 4 or 8 bytes | Depends on the system                                   |
| `long long` | 8 bytes      | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 |

!!! note 

    On many 64-bit systems, `int` and `long` are both 4 bytes, but this can vary depending on the compiler and platform.

### Fixed-Width Integer Types

These types guarantee their size across different systems and compilers.
But to use the fixed-width integer types ending with `_t`, you need to include the `<cstdint>` header.

| Type        | Size         | Signed Range / Purpose                                        |
| ----------- | ------------ | ------------------------------------------------------------- |
| `int8_t`    | 1 byte       | -128 to 127                                                   |
| `int16_t`   | 2 bytes      | -32,768 to 32,767                                             |
| `int32_t`   | 4 bytes      | -2,147,483,648 to 2,147,483,647                               |
| `int64_t`   | 8 bytes      | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807       |
| `size_t`    | 4 or 8 bytes | Represents the size of any object in memory (always unsigned) |

!!! note

    Types like `intptr_t` and `uintptr_t` are integer types capable of storing pointer values.
    They’re mostly useful in low-level or systems programming, such as writing memory managers or interfacing with hardware. Most C++ applications will never need them.

!!! note

    Similarly, `intmax_t` and `uintmax_t` represent the largest signed and unsigned integer types available on a given platform.
    While they sound powerful, they’re rarely used in everyday programming and are mainly helpful in highly portable libraries or template-heavy code.
    For most use cases, fixed-width types like `int64_t` or `size_t` are a better fit.

### Unsigned Integers

Signed integers divide their range between positive and negative numbers, effectively splitting their capacity in half.

However, in many cases, negative numbers are unnecessary or nonsensical.
For example, color channels use values from 0 to 255, and array sizes or memory addresses are always non-negative.

In these scenarios, using a signed integer like int8_t would be wasteful because half of the range goes unused.

To allocate the entire bit range exclusively for non-negative values you can use unsigned types.
For example, `uint8_t` ranges from 0 to 255, doubling the maximum value you can store in the same memory size.

| Type                 | Unsigned Range                  |
| -------------------- | ------------------------------- |
| `unsigned char`      | 0 to 255                        |
| `unsigned short`     | 0 to 65,535                     |
| `unsigned int`       | 0 to 4,294,967,295              |
| `unsigned long`      | Depends on system               |
| `unsigned long long` | 0 to 18,446,744,073,709,551,615 |
| `uint8_t`            | 0 to 255                        |
| `uint16_t`           | 0 to 65,535                     |
| `uint32_t`           | 0 to 4,294,967,295              |
| `uint64_t`           | 0 to 18,446,744,073,709,551,615 |

### Floating-Point Types

| Type          | Size       | Precision (Approx.)   | Range                          |
| ------------- | ---------- | --------------------- | ------------------------------ |
| `float`       | 4 bytes    | ~6-7 decimal digits   | ±1.5 × 10⁻⁴⁵ to ±3.4 × 10³⁸    |
| `double`      | 8 bytes    | ~15-16 decimal digits | ±5.0 × 10⁻³²⁴ to ±1.8 × 10³⁰⁸  |
| `long double` | 8-16 bytes | Varies                | Higher precision than `double` |

!!! note

    `double` is the default choice for floating-point arithmetic, providing more precision with minimal performance loss.
    However, when memory usage is critical, float may be preferred.
    To use floats, you must explicitly specify them by appending the value with an `f` suffix (e.g., 3.14f).
    Otherwise, the compiler treats floating-point literals as double by default.

### Character Types

So far, we have introduced only ASCII character encoding, which supports 128 characters (or 256 in extended ASCII).
However, ASCII is insufficient for representing the full range of characters used worldwide.

To address this, other encodings were developed, with UTF-8 becoming the most widely used due to its flexibility and backward compatibility with ASCII.

**UTF-8** can represent a wide variety of characters, including:

- Latin characters with diacritics: `é, ñ, ü, č, š`
- Mathematical symbols: `∞, ∑, π, √`
- Currency symbols: `€, ¥, ₹, ₿`
- Emoji: `😀, ❤️, 🚀, 🔥`
- Non-Latin scripts: `你好 (Chinese), Привет (Russian), नमस्ते (Hindi), العربية (Arabic)`

| Type       | Size         | Prefix  | Description                                       |
| ---------- | ------------ | ------- | ------------------------------------------------- |
| `char`     | 1 byte       | None    | Holds a single character (typically ASCII)        |
| `wchar_t`  | 2 or 4 bytes | `L'A'`  | Wide character (Unicode, size platform-dependent) |
| `char8_t`  | 1 byte       | `u8'A'` | UTF-8 character (C++20)                           |
| `char16_t` | 2 bytes      | `u'A'`  | UTF-16 character (Unicode)                        |
| `char32_t` | 4 bytes      | `U'A'`  | UTF-32 character (Unicode)                        |

These specialized types require prefixes before assigning literals to indicate their encoding.

Handling non-ASCII characters in the terminal can be tricky, especially on Windows.
To support UTF-8 output, you need to include the `windows.h` header and adjust settings via its functions.
On Linux and macOS, this usually works without additional setup.

```cpp title="main.cpp"
#include <iostream>
#include <windows.h>

#pragma execution_character_set("utf-8")

int main() {
	SetConsoleOutputCP(CP_UTF8);
	
	std::cout << "Testing unicode: " << std::endl;
	std::cout << " - English" << std::endl;
	std::cout << " - Ελληνικά" << std::endl;
	std::cout << " - Español" << std::endl;
 	std::cout << " - Русский" << std::endl;
	std::cout << " - aäbcdefghijklmnoöpqrsßtuüvwxyz" << std::endl;
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

## Automatic Type Inference

Before diving into this section, it’s helpful to have a grasp of data structures, since type inference often involves more complex types like containers and iterators.

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
However, if the return type of `GetName()` were changed to `const char*`, this code would no longer compile—C-style strings don’t support the .size() method.

This is a good example of how type inference can become fragile when the underlying return type changes.
In such cases, using an explicit type can help make your code more predictable.

One place where auto truly shines is in range-based for loops, where the element type is usually obvious and unlikely to change.

```cpp title="main.cpp"
#include <iostream>
#include <array>

int main() {
    std::array<int, 5> array{17, 48, 92, 64, 88};
	
    for (const auto& element : array)
        std::cout << element << '\t';
}
```

``` title="output"
17	48	92	64	88
```

Here, `auto` makes the loop cleaner and more readable, without sacrificing clarity or safety.
Since the type of element is tightly bound to the container, there's little risk of confusion or error.

## Determining the Byte Size of Variables

You can get the byte size of any variable using the `sizeof` operator.
Even compound structures like C-style arrays can be measured this way, which is useful because it lets you calculate the number of elements in the array using a simple formula: the total byte size of the array divided by the byte size of a single element.

```cpp title="main.cpp"
#include <iostream>

int main() {
    int array[] = {37, 82, 14, 69, 5};

    int size_of_array = sizeof(array) / sizeof(array[0]);

    for (int i = 0; i < size_of_array; ++i)
        std::cout << array[i] << '\t';
}
```

``` title="output"
37	82	14	69	5
```

The `sizeof` operator can also be used with data types directly. So, instead of dividing by `sizeof(array[0])`, you could divide by `sizeof(int)`:

```cpp 
int size_of_array = sizeof(array) / sizeof(int);
```

This works the same, but it's a bit more error-prone.
If you change the array's type, you'd need to update int in multiple places.
By dividing by sizeof(array[0]), your code automatically adapts to whatever type the array holds—making it safer and more maintainable.

## Type Casting

Sometimes, we need to reinterpret the data a variable holds by changing its type.
This process is called type casting.
Type casting is especially important in C++ because it's a statically typed language.
As a result, conversions are often necessary when working with different data types together, whether to ensure compatibility or to match the expected type in a specific context.

But not all types in C++ are directly compatible, and trying to mix them without conversion can lead to errors or unexpected behavior.
Fortunately, C++ provides two main ways to perform type conversions:

- **Implicit Conversion (Automatic)**:
  In many cases, the compiler can handle type conversions for you—this is called implicit conversion.
  For example, assigning an `int` to a `double` works automatically, because the compiler knows how to safely widen the value without losing information.
- **Explicit Conversion (Casting)**:
  When a conversion isn’t safe or obvious, C++ requires you to perform an explicit cast.
  This tells the compiler, “Yes, I know what I’m doing—convert this anyway.” There are several ways to do this, and we’ll cover them.

### Conversions

**In C++, conversions are implicit**.
They occur when the compiler automatically converts one type to another without data loss, such as in arithmetic operations between different types or when assigning a `double` to an `int`.

```cpp title="main.cpp"
#include <iostream>

int main() {
    double pi = 3.14;
    int value = pi; // Implicit conversion: fractional part is lost

    char a = 'A';
    int b = a; // Implicit conversion: 'b' is assigned 65 (ASCII value of 'A')

    std::cout << "Double to integer conversion: " << value << std::endl;
    std::cout << "Character to integer conversion: " << a << std::endl;
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
    
    std::cout << "Value: " << *ptr_num << std::endl;
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

    std::cout << "Value: " << *int_ptr << std::endl;
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

    std::cout << "Value: " << value << std::endl;
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
    
    std::cout << "Interpreted int: " << *int_ptr << std::endl;
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

    std::cout << "Memory address: " << address << std::endl;
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

## Constants and Compile-Time Constructs

In the introduction chapter, we introduced the idea of constants—variables that are meant to remain unchanged throughout a program's execution.
However, we later saw that `const` can be bypassed with `const_cast`, which means it doesn't provide absolute immutability.
This is generally a bad design choice, as it violates the principle that constants should truly be constant.

To ensure a variable or function is genuinely immutable and can be evaluated at compile time, modern C++ provides the `constexpr` keyword.
This is also a type-safe alternative to the old preprocessor macros like `#define` and offers a more reliable way to express intent in your code.

### Constexpr

The `constexpr` keyword guarantees that a **variable or function is evaluated at compile time**.
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
    std::cout << Add(5, 10) << std::endl;

    /*
        After compilation, it becomes this:
        std::cout << 5 + 10 + 20 << std::endl;
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
	std::cout << global_value << std::endl;
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
    std::cout << "Alignment of integer: "<< std::alignment_of_v<int> << std::endl;
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
	std::cout << "Size of entity: " << sizeof(Entity) << std::endl;
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
	std::cout << "Size of entity: " << sizeof(Entity) << std::endl;
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

	std::cout << "Value: " << value << std::endl; 
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
	
	std::cout << "Value: " << result << std::endl;
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
    
    std::cout << "Second array value: " << array[1] << std::endl;
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
        std::cout << "File was read successfully: " << *data << std::endl;
    } else {
        std::cout << "File was not read successfully!" << std::endl;
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
    std::cout << "String variant: " << std::get<std::string>(data) << std::endl;

    data = 3;
    std::cout << "Integer variant: " << std::get<int>(data) << std::endl;
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
    std::cout << "String value: " << *value << std::endl;
} else {
    std::cout << "Data does not hold a string." << std::endl;
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
    std::cout << "Now holding integer: " << std::any_cast<int>(data) << std::endl;

    data = std::string("String");
    std::cout << "Now holding string: " << std::any_cast<std::string>(data) << std::endl;
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

## Chapter Summary

By completing this chapter, you should now have a solid understanding of data types in C++—from the basic building blocks like integers, floating-point numbers, and characters, to more advanced tools like RTTI and type punning.

You’ve also explored how data types affect memory layout, performance, and the overall type safety of your programs.

In particular, you've learned:

- How to safely convert between types using modern C++ casting operators like `static_cast` and `dynamic_cast`, which offer greater clarity and safety than traditional C-style casts.
- The flexibility of dynamic typing through `std::optional`, `std::variant`, and `std::any`, each suited for different scenarios where type uncertainty or variability is involved.
- How type punning allows you to reinterpret data at a low level, and how RTTI (Run-Time Type Information) can help identify types at runtime—while being mindful of their performance implications.