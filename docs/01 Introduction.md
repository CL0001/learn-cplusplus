# 01 Introduction

We pick C++ as the language for learning computer science because, for one, it's an incredibly powerful and versatile tool.
C++ is used to build everything from command-line utilities and desktop apps to real-time systems and high-performance games.
It's the language behind major AAA titles like Counter-Strike, Fortnite, and the Warcraft series.
Game engines like Unreal Engine are built on it, and even modern operating systems—including parts of Windows—rely on C++ under the hood.

Even if you're not aiming for game development or systems programming, learning C++ gives you a solid foundation in how computers actually work.
This low-level understanding transfers to almost every other language or tech stack you’ll encounter later.

One of the big reasons C++ remains so relevant is its performance and portability.
It runs natively on everything—Windows, Linux, macOS, embedded devices, mobile platforms, game consoles—you name it.
It supports both 32-bit and 64-bit architectures and compiles directly to machine code, which means it can be very fast when written well.

But here’s the flip side: C++ gives you a lot of control—especially over memory—and that means it’s also easier to shoot yourself in the foot.
Sloppy code can lead to memory leaks, crashes, or bugs that are really tricky to track down.
It’s a language that rewards careful thinking and clean design.

C++ is built on top of C, one of the oldest and most influential programming languages still in use today.
In fact, a lot of valid C code is also valid C++.
But C++ goes further, introducing features that help you write safer, more maintainable, and modern code—without losing the performance edge that made C famous.

In this book, we’ll explore C++ from the ground up.
When it makes sense, we’ll also dip into some C concepts—especially when we want to get closer to the hardware or squeeze out maximum performance.

## Environment Setup

To write and run C++ code on your computer, you need to install two essential components:

1. Compiler: A program that translates your human-readable C++ source code—essentially just plain text—into machine-readable binary.
   It acts as a bridge between your code and the hardware, enabling the CPU to execute your instructions.
2. Code Editor or IDE (Integrated Development Environment): A tool that helps you write, edit, and manage your code.
   Most IDEs also offer features like syntax highlighting, auto-completion, and debugging tools.

### Recommended Setup for Windows

The easiest way to get started with C++ on Windows is by using Microsoft Visual Studio.
It’s a fully featured IDE that comes preconfigured for C++ development and includes the MSVC compiler, so there's no need to install it separately like with other solutions—it provides everything you need in a single package.

**Installation Steps**

1. Download Visual Studio Community Edition from the microsoft official website or Microsoft Store
2. During installation, select "Desktop development with C++" to include the required compiler and tools.
3. After installation, launch Visual Studio and follow these steps:
    1. Click "Create a new project"
    2. In the project language filter, choose C++
    3. Select "Console App"
    4. Enter a your desired project name
    5. Choose a location for the project (default is usually `C:\Users\Username\source\repos`)
    6. Click Create
5. To test your setup, run the Console App sample program by clicking the green arrow button at the top center of the screen.

If a terminal window appears showing "Hello World!", your installation is complete and working correctly.

!!! tip

    To add a new file in Visual Studio, right-click on "Source Files" or "Header Files" in the "Solution Explorer", depending on what kind of file you need.
    Then choose "Add" → "New Item".
    Give your file any name you like, such as `something_like.cpp`.

    Keep in mind that "Source Files" and "Header Files" are just filters in the Solution Explorer—they don’t represent actual folders on your system.
    All the files you add will be placed in the same physical project directory on disk.

!!! tip

    For a more advanced C++ development experience in Visual Studio, consider using Visual Assist.
    It enhances code navigation, refactoring, and auto-completion.  
    It's not free (offers a 1-month trial or is free with a student ID), but it’s highly recommended for serious C++ work.  
    More info: <a href="https://www.wholetomato.com/" target="_blank" rel="noopener noreferrer">https://www.wholetomato.com/</a>

### Recommended Setup for macOS

The easiest way to get started with C++ on macOS is by using Xcode, Apple’s official IDE.
It includes the Clang compiler and provides everything you need to build and run C++ command-line programs—all in one package.

**Installation Steps**

1. Open the Mac App Store and download Xcode.
2. Once installed, launch Xcode.
3. Follow these steps to create a C++ project:
    1. Click "Create a new Xcode project"
    2. Under macOS, select "Command Line Tool" and click Next
    3. Enter a product name (e.g., `HelloCpp`)
    4. Set Language to C++
    5. Click "Next"
    6. Choose a location to save your project and click Create
5. To test the installation, run the sample code that is included in the project by clicking the “Run” button located at the top-left corner of the Xcode window.

Once you see "Hello, World!" output in the console, your setup is complete.

!!! tip

    If the console is not visible, go to View → Debug Area → Activate Console.

### Alternative Setup via Web

If your computer is too slow to run heavy IDEs—or if you simply don’t want to install anything—you can use an online C++ compiler to get started quickly.
This is a convenient way to write and run code directly in your browser.

However, keep in mind that most online compilers have limitations.
They often don’t support the latest C++ standards (such as C++17 or C++20), and they usually lack access to the local file system, meaning file input/output won’t work as expected.
Because of this, some features covered later here won’t be usable in a web-based environment.

One beginner-friendly example can be found here: <a href="https://www.programiz.com/cpp-programming/online-compiler/" target="_blank" rel="noopener noreferrer">https://www.programiz.com/cpp-programming/online-compiler/</a>

## How C++ Works

Now that the setup is behind us—and we've touched on why learning a lower-level language like C++ builds a solid understanding of how computers operate—let's start exploring how a C++ program actually comes together under the hood.

The files we write in C++ are just plain text, but they’re typically separated by purpose.
Source files—usually with a `.cpp` extension though you might also see `.cc` or `.cxx`—contain the actual logic and code of your program.
Header files—commonly ending in `.h`, `.hh`, or .`hpp`—are used to declare functions, classes, and variables that can be shared across multiple source files.

We’ll go through all of these concepts—like functions, classes, and variables—step by step in the upcoming chapters.
For now, just think of them as different parts of your program: some that do things, and others that describe what exists.
Don’t worry if words like “declare” or “variable” sound unfamiliar—we’ll explain everything clearly as we go.

!!! note

    In C++, file extensions are just a convention—they don't affect how the compiler interprets the code.
    There are some common extensions, but technically, you could name your file anything.
    What matters is how the file is treated by the compiler.
    As long as you tell the compiler that it's a C++ file, it will process it correctly.

To actually run on your computer, these files must be translated into a binary file that your machine can understand.
This binary is usually either an executable like a `.exe` file on Windows or a library, which contains pre-written, pre-compiled code that can be linked to an executable such as `.lib` or `.dll` files.
We’ll explore these library formats and the linking process in more detail in later chapters.

The next important thing to understand is that the compilation process—translating human-readable code into machine-executable instructions—involves two key settings: project configuration and target platform.

**Project configuration** determines how the compiled binary behaves. The two most common options are:

| Configuration | Description                                                                 | Use Case                 |
| ------------- | --------------------------------------------------------------------------- | ------------------------ |
| Debug         | Includes extra information like error messages and debugging symbols.       | Ideal during development |
| Release       | Optimized for speed; debugging info is removed.                             | For final builds         |


**Target platform** refers to the system architecture your program is intended to run on, such as x86 (32-bit) or x64 (64-bit).

!!! note

    Programs must be compiled separately for each target platform because a binary built for Windows will not run on macOS or Linux, and vice versa.
    Similarly, the architecture matters: a 32-bit program can generally run on a 64-bit system, but a 64-bit program cannot run on a 32-bit system.

### Entry Point

Every C++ program needs an entry point—the place where execution begins.
**There must be exactly one entry point, or the program won’t compile**.
Without it, your program has no defined starting point.

You’ve already seen this entry point during setup, where a sample file was created for you.


```cpp title="main.cpp"
#include <iostream>

int main() {
    std::cout << "Hello World!" << std::endl;
}
```

``` title="output"
Hello World!
```

!!! note

    `main.cpp` is the standard filename for the entry point of a C++ program.

In this example, the entry point is the main function on line 3.
This is where the program starts executing after it is launched.
Everything inside the curly braces is the function body, which contains the instructions that run when the program begins.

The `main` function is a special case in C++.
Even though it declares that it returns an `int`, you might not see a return statement—don’t worry, we’ll cover this in more detail later.
Just remember that `main` is unique in this regard, and we’ll emphasize why as we go along.
This happens because modern compilers automatically insert `return 0;` at the end of `main` if you don’t include it yourself.
This behavior is specific to `main` and does not apply to other functions.

### Preprocessor Directives

In C++, any line that begins with a `#` is called a preprocessor directive.
These are instructions handled before actual compilation begins—hence the name preprocessor.
They aren’t part of the C++ language itself but are understood by the preprocessor, which runs just before the compiler.

You’ve also already seen an example of a preprocessor directive in our earlier sample.

```cpp title="main.cpp"
#include <iostream>

int main() {
	std::cout << "Hello World!" << std::endl;
}
```

``` title="output"
Hello World!
```

The line `#include <iostream>` tells the preprocessor to find the file named `iostream` and copy-paste its entire contents into this file at that exact location.
This makes all the functionality defined in `<iostream>` — such as input/output using `std::cout` (console output), `std::cin` (console input), and `std::endl` (newline) — available in our program.

### Compiler

The C++ compiler transforms your human-readable code into a format that computers can understand, but it does so in several intermediate steps.

First, it creates intermediate files—typically with a `.obj` extension on Windows or `.o` on macOS/Linux.
These files are often found in your project directory under folders like x64 → Debug.

During compilation, the compiler begins by processing all preprocessor directives (lines starting with #).
Then, it tokenizes and parses your code.
Parsing builds an internal structure called an Abstract Syntax Tree (AST), which represents the logic and meaning of your program.

Once the AST is ready, the compiler generates machine code based on your original source file.

Here’s an example of what a raw `.obj` file might look like (shown as hexadecimal bytes).

``` title="main.obj"
00000000 64 86 5A 00 8B C5 06 68 87 EC 00 00 EF 01 00 00 d.Z....h..
00000010 00 00 00 00 2E 64 72 65 63 74 76 65 00 00 00 00 .....drectve....
00000020 00 00 00 00 90 01 00 00 24 0E 00 00 00 00 00 00 ....$.......
00000030 00 00 00 00 00 00 00 00 00 0A 00 2E 64 65 62 00 ....... ..deb
00000040 75 67 24 53 00 00 00 00 00 00 00 00 28 A6 00 00 ug$S........(..
00000050 B4 0F 00 00 DC 85 00 00 00 00 00 00 04 00 00 00 ............
00000060 40 00 10 42 2E 6D 73 76 63 6A 6D 63 00 00 00 00 @..B.msvcjmc....
00000070 00 00 00 00 6A 00 00 00 24 BE 00 00 00 00 00 00 ....j...$.......
00000080 00 00 00 00 00 00 00 00 40 00 10 C0 2E 74 65 78 ........@...tex

... (truncated for brevity)
```

``` title="main.asm"
.LC0:
        .string "Hello World!"
main:
        push    rbp
        mov     rbp, rsp
        mov     esi, OFFSET FLAT:.LC0
        mov     edi, OFFSET FLAT:std::cout
        call    std::basic_ostream<char, std::char_traits<char>>& std::operator<<<std::char_traits<char>>(std::basic_ostream<char, std::char_traits<char>>&, char const*)
        mov     esi, OFFSET FLAT:std::basic_ostream<char, std::char_traits<char>>& std::endl<char, std::char_traits<char>>(std::basic_ostream<char, std::char_traits<char>>&)
        mov     rdi, rax
        call    std::ostream::operator<<(std::ostream& (*)(std::ostream&))
        mov     eax, 0
        pop     rbp
        ret

... (truncated for brevity)
```

You don’t need to understand these low-level details right now—they’re just examples of how code is represented internally at different stages of compilation.
Also, note that these examples don’t directly correspond to each other, as they are just snippets from the actual full files.

!!! tip

    You can easily explore the assembly output of nearly any code you write using different compilers at this website: 
    <a href="https://godbolt.org/" target="_blank" rel="noopener noreferrer">https://godbolt.org/</a>

Each source file is refered to as a translation unit.
The compiler processes each translation unit independently, producing an intermediate object file.
When your program consists of multiple source files, these translation units are later linked together to create the final executable or library.
If your entire program is in a single source file, then there will be only one translation unit.

### Linker

After the compiler processes each translation unit into object files, the linker’s job is to bring everything together into a final executable.
Without linking, each translation unit only knows about its own contents, so the program can’t run as a whole.

The linker connects all the pieces by resolving function calls, variables, and other symbols across translation units.
Even if your program consists of a single translation unit, the linker still plays an important role—it locates the program’s entry point so the execution knows where to begin.

Below is an example illustrating a common linker error.

```cpp title="main.cpp"
#include <iostream>

int Multiply(int a, int b); // Function declaration
// Missing function definition 

int main() {
	std::cout << Multiply(3, 7) << std::endl;
}
```

``` title="output"
Linker Error: unresolved external symbol
```

Linker errors only appear when the program is compiled and linked as a whole, since the linker’s job is to combine all parts together.
Although each individual source file may compile successfully on its own, during the linking stage you might encounter a **common error: unresolved external symbol**.

That happens because while the function declaration exists, the function definition (body) is missing.
The linker is trying to connect the `Multiply` call to actual code, but it can’t find it—so it fails.
You can fix the error by simply providing the function definition.

```cpp
#include <iostream>

int Multiply(int a, int b) {
	return a * b;
}

int main() {
	std::cout << "Multiplication: " << Multiply(3, 7) << std::endl;
}
```

``` title="output"
Multiplication: 21
```

This might seem like a simple mistake, but when working on a large project with dozens of translation units and external libraries, it’s surprisingly easy to overlook—and linker errors like this can be tricky to debug.

## Header Files

In C++, header files are one of the two main types of files.
The first are the source files, which we’ve been working with so far.
This file contains the actual code and logic of your program.
It gets compiled into an object file, forming a translation unit.

The second type is the header file.
You’ve already seen an example of one in the sample your IDE generated.

```cpp title="main.cpp"
#include <iostream> // header file

int main() {
	std::cout << "Hello World!" << std::endl;
}
```

``` title="output"
Hello World!
```

Header files are used mainly for declarations.
They don’t usually contain full implementations—just the "signatures" or interfaces.
These declarations are included in source files so that the compiler knows what exists.

Without these headers, the compiler would be completely unaware of things like `std::cout` or `std::endl`, and you’d get a long list of errors, as shown below.

``` title="output"
Main.cpp: In function ‘int main()’:
Main.cpp:2:10: error: ‘cout’ is not a member of ‘std’
    2 |     std::cout << "Hello World!" << std::endl;
      |          ^~~~
Main.cpp:1:1: note: ‘std::cout’ is defined in header ‘<iostream>’; did you forget to ‘#include <iostream>’?
  +++ |+#include <iostream>
    1 | int main() {
Main.cpp:2:41: error: ‘endl’ is not a member of ‘std’
    2 |     std::cout << "Hello World!" << std::endl;
      |                                         ^~~~
Main.cpp:1:1: note: ‘std::endl’ is defined in header ‘<ostream>’; did you forget to ‘#include <ostream>’?
  +++ |+#include <ostream>
    1 | int main() {
```

Including headers tells the compiler: _"Hey, this function or object is declared somewhere—trust me, you'll find its full definition later during linking."_

```cpp title="log.h"
#pragma once

void Log(const char* message);
```

```cpp title="log.cpp"
#include <iostream>

void Log(const char* message) {
	std::cout << message << std::endl;
}
```

```cpp title="main.cpp"
#include <iostream>

#include "log.h"

int main() {
	Log("Using Logging Function!");
}
```

``` title="output"
Using Logging Function!
```

In the example above, we can see how header files are used. As we’ve already learned, `#include` is a preprocessor directive that tells the compiler to copy the contents of the specified file into the current translation unit.

That’s why `main.cpp` knows about the `Log` function—even though it’s implemented elsewhere. Without the header file, the compiler would have no idea what `Log` is and would throw an error during compilation, never making it to the linking stage.

### Header Guard

You may have noticed a new directive at the top of `log.h`.

```cpp
#pragma once
```

This is called a header guard.
It ensures that the contents of the file are included only once per translation unit—even if the file gets included multiple times through different paths.
Without a header guard, the same declarations (such as functions or variables) could be included more than once, leading to redefinition errors during compilation.

Header guards are especially important in larger projects, where headers may be chained together or included indirectly through other headers.

The traditional (and more portable) approach uses the `#ifndef` directive.
While a bit more verbose than `#pragma once`, it’s still widely supported and commonly used.

```cpp title="log.h"
#ifndef _LOG_H_
#define _LOG_H_

void Log(const char* message);

#endif
```

This pattern works by checking whether `_LOG_H_` has already been defined.
If not, it defines it and includes the content inside.
If the file has already been included elsewhere, the compiler skips its contents—avoiding duplicate declarations.

### Header Search

In `main.cpp`, we used two different notations when including header files.

```cpp
#include <iostream>
#include "log.h"
```

The difference between them lies in where the compiler looks for the header file.

| Syntax           | Search Path                                                                                   | Typical Use Case                           |
| ---------------- | --------------------------------------------------------------------------------------------- | ------------------------------------------ |
| `#include <...>` | Searches system and standard library include paths.                                           | For standard headers like `<iostream>`.    |
| `#include "..."` | Searches relative to the including file, usually starting with the current working directory. | For project-specific headers like `log.h`. |


## Code Comments

Comments are lines in your code that are completely ignored by the compiler.
This means they have no effect on how the program runs—they're written solely for humans to read.

Comments are useful because, as programs grow larger and more complex, it becomes necessary to document your code.
This helps others or even your future self understand what the code is doing and why certain decisions were made.

However, keep in mind that good code should be self-explanatory.
Avoid writing comments that simply restate what the code is already clearly doing.

```cpp title="main.cpp"
// "Main function" is a bad comment
int main() {
    // code ...
}
```

Instead, comments should be used to:

- Explain logic or reasoning behind complex code.
- Make notes for future improvements or reminders.
- Temporarily disable code (useful for debugging or testing).
- Provide documentation for advanced algorithms or non-obvious behavior.

### Types of Comments

**Single-line comments** (`//`): Anything after `//` on the same line is considered a comment. 

```cpp title="main.cpp"
#include <iostream> // This is a single-line comment

int main() {
	std::cout << "Hello World!" << std::endl;
}
```

``` title="output"
"Hello World!"
```

**Multi-line comments** (`/* ... */`): Used for longer explanations spanning multiple lines.
 
```cpp title="main.cpp"
#include <iostream>

/* 
    This is a multi-line comment.
    It can span multiple lines without needing // on each line. 
*/ 

int main() {
	std::cout << "Hello World!" << std::endl;
}
```

``` title="output"
"Hello World!"
```

## Data Types and Variables

In programming, data types and variables are fundamental concepts.
That’s because programming is ultimately about working with data—and to work with data effectively, two things need to happen:

| Requirement | Description                                                                                               |
| ----------- | --------------------------------------------------------------------------------------------------------- |
| Storage     | The data must be placed somewhere in the computer's memory.                                               |
| Typing      | The program must understand what kind of data it's dealing with (e.g., a number, a character, and so on). |


To represent different kinds of data, we use data types.
To actually hold that data in memory, we use variables.

Even though all data in memory is ultimately stored as binary, data types give those raw bits meaning.
The main differences between data types come down to how much memory they use and how those bits are interpreted.

!!! info

    Data types are important enough to deserve their own full chapter—but for now, this brief introduction will do just fine.

### Primitive Data Types

The most basic data types in C++ are called primitive types. They form the foundation for all more complex types you'll encounter later.

| Type     | Name                        | Description                                                                 | Size                            |
| -------- | --------------------------- | --------------------------------------------------------------------------- | ------------------------------- |
| `int`    | Integer                     | Stores whole numbers                                                        | Typically 4 bytes               |
| `float`  | Floating-point number       | Stores numbers with decimals. Precision is limited.                         | 4 bytes                         |
| `double` | Double-precision float      | Similar to `float`, but with more precision.                                | 8 bytes                         |
| `char`   | Character                   | Stores a single ASCII character (e.g., 'A', 'b', '#').                      | 1 byte                          |
| `bool`   | Boolean                     | Stores only `true` (1) or `false` (0).                                      | 1 byte (implementation-defined) |

!!! tip

    In programming, instead of using spaces to separate large numbers for readability (which would cause a syntax error), you can group digits using single quotes.
    For example, instead of writing 1976834, you can write 1'976'834—this makes the number easier to read, especially when dealing with thousands or millions.

### ASCII

ASCII (American Standard Code for Information Interchange) is a character encoding standard that represents text characters using numerical values. For example:

| ASCII Code | Character |
|------------|-----------|
| 65         | A         |
| 66         | B         |
| 97         | a         |
| 48         | 0         |

Full table can be found here: <a href="https://www.asciitable.com/" target="_blank" rel="noopener noreferrer">https://www.ascii-code.com/</a>


### Declaring and Naming Variables

A variable is a named storage location for data in memory. When we create a variable, we assign it a data type and name so that we can refer to it later in our program.

**Rules for Naming Variables:**

- Must start with a letter or underscore, but cannot start with a number.
- Can contain letters, numbers, and underscores, but no spaces.
- Cannot use C++ reserved keywords (e.g., `int`, `float`, `return`).
- Should be descriptive (e.g., `user_age` instead of `x`).

We assign values to variables using the `=` symbol. There are a few important rules to keep in mind when assigning values:

- Floating-point numbers: Use a dot instead of a comma, e.g., `3.14`, `-2.71`.
- Characters: Must be enclosed in single quotes, e.g., `'A'`, `'b'`, `'#'`.
- Escape sequences such as `\t` (tab) and `\n` (new line) are special characters that affect text formatting.

```cpp title="main.cpp"
#include <iostream>

int main () {
	int user_age = 25;
	const float pi = 3.14;    
	char user_grade = 'A';
	bool underaged = false;

    std::cout << "User age: " << user_age << std::endl;
    std::cout << "PI: " << pi << std::endl;
    std::cout << "User grade: " << user_age << std::endl;
    std::cout << "Underaged? " << underaged << std::endl;
}
```

``` title="output"
User age: 25
PI: 3.14
User grade: 25
Underaged? 0
```

**In C++, each declaration or statement must end with a semicolon `;`**. This symbol tells the compiler that the statement is complete.

Additionally, in the example above, you might have noticed a specific way of naming variables. This naming style is called snake_case,
where all characters are lowercase and words are separated by underscores (e.g., user_age, player_score). This style improves readability, especially in longer variable names, and is commonly used in many C++ codebases.

!!! info

    Throughout this guide, we will follow the Google C++ Style Guide, which recommends using snake_case for variable names and PascalCase for function and class names.

    You can learn more about these conventions and other best practices here: 
    <a href="https://google.github.io/styleguide/cppguide.html" target="_blank" rel="noopener noreferrer">https://google.github.io/styleguide/cppguide.html</a>

### Constants

Sometimes, we want our stored data to be read-only, especially when displaying information in a terminal, website, or other output formats.
To achieve this, we use the `const` keyword in front of a variable declaration, which ensures the variable remains unchangeable after initialization.

```cpp title="main.cpp"
#include <iostream>

int main() {
    const int number = 54;

    number = 34;

    std::cout << number << std::endl; 
}
```

``` title="output"
error: assignment of read-only variable ‘number’
```

As shown in the example above, a constant variable cannot be reassigned a value—it results in an error.

## Terminal Input and Output

So far, we've focused on how programs store data internally.
Equally important, though, is understanding how that data enters and exits the program.
While data can come from sources like files, databases, or web APIs, the most fundamental method is through terminal input and output.

In C++, this is handled using the standard library `iostream`, which provides tools for both displaying messages to the user and reading input from the terminal.

The following example demonstrates how to use `std::cout` to prompt the user and `std::cin` to read their response.

```cpp title="main.cpp"
#include <iostream>

int main() {
    int user_age;

    std::cout << "Enter your age: ";
    std::cin >> user_age;

    std::cout << "User age: " << user_age << std::endl;
}
```

``` title="output"
It depends on the input you provided
```

It can be tricky for beginners to remember which symbols to use for input (`>>`) and output (`<<`) streams. A helpful way to visualize them is to think of them as arrows showing the direction of data flow:

| Statement               | Description                                          |
| ----------------------- | ---------------------------------------------------- |
| `std::cin >> variable;` | Data flows from the terminal into the variable.      |
| `std::cout << message;` | Data flows from the program out to the terminal.     |

## Exercises

=== "task 1"

    ```
    Write a program that will print into the terminal
    your name in one line and surname into another
    ```

=== "answer"

    ```cpp
    #include <iostream>

    int main() {
        std::cout << "Name" << std::endl;
        std::cout << "Surname" << std::endl;
    }
    ```

---

=== "task 2"

    ```
    Write a program that will ask user for how old they are
    and store it in a variable that will be subsequantily
    printed into the terminal
    ```

=== "answer"

    ```cpp
    #include <iostream>

    int main() {
        int user_age;

        std::cout << "Enter your age: ";
        std::cin >> user_age;

        std::cout << "User age: " << user_age << std::endl; 
    }
    ```