# 01 Introduction

We chose C++ as our language of choice for learning computer science because it’s an incredibly powerful and versatile tool.
It offers low-level access to memory management when we need fine-grained control, yet also provides high-level abstractions when we prefer to think in broader, conceptual terms.
C++ is used to build everything from command-line utilities and desktop applications to high-performance systems such as 3D engines, game frameworks, and low-latency trading platforms.

To spark your interest, C++ is the language behind major AAA titles like Counter-Strike, Fortnite, the Warcraft series and many more.
Every industry-grade game engine—such as Unreal, Unity, or Godot—is built on it, and even modern operating systems, including parts of Windows, rely on C++ under the hood.

Also, it’s important to note that even if you’re not aiming for game development or high-performance programming—the areas where C++ truly shines—learning C++ gives you a remarkably solid foundation in how computers work.
Along the way, you’ll also get to peek into several programming paradigms and long-standing design ideas.
Not all of these ideas are universally great or perfectly modern, but they provide valuable context for how today’s languages evolved.

This kind of low-level understanding tends to carry over to almost every other language or technology stack you’ll encounter later on.

Another big reason C++ remains so relevant is that, in addition to its performance, it offers excellent portability.
It runs natively on everything—Windows, Linux, macOS, embedded devices, mobile platforms, game consoles—you name it.
It supports both 32-bit and 64-bit architectures and compiles directly to machine code, which means it can be very fast when written well.

But here’s the flip side: C++ gives you a lot of control—especially over memory—and that also means it’s easier to shoot yourself in the foot.
Sloppy code can lead to memory leaks, crashes, or bugs that are notoriously tricky to track down.
It’s a language that rewards careful thinking and clean design.

But despite its challenges, C++ remains one of the most enduring languages in modern computing.
Part of that longevity comes from its roots: C++ is built on top of C, one of the oldest and most influential programming languages still in use today.
Much of C code even compiles directly as valid C++, but C++ takes things further.
It introduces features that make your programs safer, more maintainable, and more modern—without losing the raw performance that made C so famous.

In this book, we’ll work through modern C++ from the ground up.
Along the way, we’ll occasionally dip into C concepts as well, especially when we want to get closer to the hardware or squeeze out every last bit of performance.

!!! info
    Before we jump into actual coding, we need to cover a few essentials in this introduction chapter.  
    We'll go through how to set up your development environment, introduce some basic terminology and conventions used throughout the book, and get familiar with how the C++ build process works.  
    Once those foundations are in place, we’ll be ready to start writing C++ code.


## Environment Setup

To write and run C++ code on your computer, you need two essential components:

- **Compiler:** A program that translates your human-readable C++ source code—essentially plain text—into machine-readable binary.
It acts as a bridge between your code and the hardware, allowing the CPU to execute your instructions.

- **Code Editor or IDE (Integrated Development Environment):** A tool that helps you write, edit, and manage your code.
Most IDEs also provide features like syntax highlighting, auto-completion and debugging tools.


### Recommended Setup for Windows

The easiest way to get started with C++ on Windows is to use Microsoft Visual Studio Community Edition.
It’s a fully featured IDE that comes preconfigured for C/C++ development and includes the MSVC compiler, so there’s no need to install it separately.
Everything you need comes in a single package.

**Installation Steps**

1. Download Visual Studio Community Edition from the Microsoft official website
   <a href="https://visualstudio.microsoft.com/vs/community/" target="_blank" rel="noopener noreferrer">https://visualstudio.microsoft.com/vs/community/</a> or directly from the Microsoft Store.
2. During installation, select “Desktop development with C++” to include the required compiler and tools.
3. After installation, launch Visual Studio
4. Click “Create a new project”
5. In the project language filter, choose C++
6. Select “Console App”
7. Enter your desired project name
8. Choose a location for the project (default is usually C:\Users\Username\source\repos)
9. Click Create

To test your setup, run the "Console App" sample program by clicking the green arrow button at the top center of the screen.

If a command line window appears showing “Hello World!”, your installation is complete and working correctly.

!!! tip

    To add a new file in Visual Studio, right-click on **Source Files** or **Header Files** in the **Solution Explorer**, depending on the type of file you need.
    Then choose **Add → New Item** and give it a name, such as `example.cpp`.

    Keep in mind that **Source Files** and **Header Files** are just filters in the **Solution Explorer**—they don’t represent the actual folder structure on your system.
    All files you add will be placed in the same physical project directory on disk.

!!! tip

    For a more advanced C++ development experience in Visual Studio, consider using Visual Assist. It enhances code navigation, refactoring, and auto-completion.

    It’s not free (offers a 1-month trial or is free with a student ID), but it’s highly recommended for serious C++ work.  
    More info here: <a href="https://www.wholetomato.com/" target="_blank" rel="noopener noreferrer">https://www.wholetomato.com/</a>


### Recommended Setup for macOS

The easiest way to get started with C++ on macOS is by using Xcode, Apple’s official IDE.
It includes the Clang compiler and provides everything you need to build and run C++, all in one package.

**Installation Steps**

1. Open the Mac App Store and download Xcode.
2. Once installed, launch Xcode.
4. Click “Create a new Xcode project”
5. Under macOS, select “Command Line Tool” and click Next
6. Enter a product name (e.g., HelloCpp)
7. Set Language to C++
8. Click Next
9. Choose a location to save your project and click Create

To test the installation, run the sample code included in the project by clicking the Run button at the top-left corner of the Xcode window.

Once you see “Hello, World!” output in the console, your setup is complete.

!!! tip

    If the console is not visible, go to View → Debug Area → Activate Console.


### Recommended Setup Without Installation

If your computer struggles to run large IDEs—or if you simply want to start coding immediately without installing anything—you can use an online C++ compiler.
This is a fast and convenient way to write, compile and run C++ code directly from your browser.

The best online environment is Compiler Explorer, created by Matt Godbolt.
It’s free, powerful and widely used by professional developers for testing and learning C++ features.

**How to Use**

1. Go to <a href="https://godbolt.org/" target="_blank" rel="noopener noreferrer">https://godbolt.org/</a>.
2. Select a compiler from the drop-down menu on the top-left
3. Type your C++ code in the editor pane on the left.
4. Click Run or press Ctrl + Enter to compile and execute your code.
5. The output will appear in the console area at the bottom-right of the screen.

!!! warning
    Compiler Explorer is ideal for quickly testing code snippets, comparing compiler behavior or experimenting with new C++ features without changing your local setup.
    However, it’s not designed for large projects—once you start building multi-file programs, you’ll want to switch to a local IDE.


## Build Process

!!! info
    Don’t worry if some words sound unfamiliar—we’ll explain everything clearly as we go.

Now that the setup is behind us let’s take a look at how C++ programs actually come together.

To tell a computer what we want it to do, we write code using a programming language—in our case, we've chosen C++.
This code is written in plain text files, but instead of using the standard `.txt` extension, they use different extensions to reflect their purpose and indicate which programming language they're written in.

Files referred to as source files—usually with a `.cpp` extension (though you might also see `.cc` or `.cxx`)—contain the actual logic and instructions that make your program run.

Files referred to as header files—commonly ending in `.h`, `.hh`, or `.hpp`—are used to declare things like functions, classes, and variables so they can be shared across multiple source files.

These file extensions are common, but they’re just conventions—they don’t affect how the compiler interprets your code.
Naming patterns help humans organize files, but technically, you could name them almost anything.
This separation of code into source and header files is foundational to how C++ projects are structured.

Once we’ve written code describing what we want the computer to do, the next step is to turn it into a binary file—a format the machine can execute directly.

This binary is usually either an executable such as a `.exe` file or a library, which contains pre-written, pre-compiled code that can be linked into an executable, like `.lib` or `.dll` files.
We'll dive deeper into libraries and the process of linking against them in later chapters.

The next important piece to understand is the compilation process itself—how your source code becomes a working program.  
This process is influenced by two key settings: project configuration and target platform.

This determines how the compiled binary behaves. The two most common project configurations are:

| Configuration | Description                                                      | Use Case                 |
|---------------|------------------------------------------------------------------|--------------------------|
| Debug         | Includes extra information like error messages and debug symbols | Ideal during development |
| Release       | Optimized for performance; debugging info is removed             | For final builds         |

Target platform refers to the system architecture your program is intended to run on, such as x86 (32-bit) or x64 (64-bit).

!!! note

    Programs must be compiled separately for each target platform.
    A binary built for Windows won’t run on macOS or Linux and vice versa.

    Architecture also matters: a 32-bit program can usually run on a 64-bit system, but a 64-bit program will not run on a 32-bit system.


### Entry Point

Every program needs an entry point—the place where program execution begins and in C++, that entry point is always the `main` function.

There must be exactly one entry point; if it’s missing or if there’s more than one, the compiler won’t know where to start running your code, resulting in a compilation error.

We’ve already seen an entry point during setup, where a sample file was created for us by the IDE.

```cpp title="main.cpp" hl_lines="3"
#include <iostream>

int main() {
    std::cout << "Hello World!\n";
}
```

``` title="output"
Hello World!
```

!!! note

    `main.cpp` is a common filename for the source file containing your program’s entry point, but it’s not required.
    You can name the file anything you want—what matters is the main function itself.

In the example, the entry point is as expected—the function called main highlighted on line 3. 
This is where the program starts executing after it is launched.

Everything inside the curly braces is called the function body, which contains the instructions that execute when the function is called—in this case, when the program begins.

One other thing to note about the main function is that, even though it declares a return type of integer, you might not always see a return statement.
Don’t worry—we’ll cover return statements in more detail in a later chapter focused solely on functions.

For now, it’s enough to know that what’s happening here is that modern compilers automatically insert a `return 0;` at the end of main function body if you don’t include it yourself.
This behavior is specific to main and does not apply to other functions.


### Preprocessor Directives

In C++, any line that begins with a sharp symbol is called a preprocessor directive.
These are instructions handled before actual compilation begins—hence the name preprocessor.
They aren’t part of the C++ language itself but are understood by a separate tool called the preprocessor, which runs just before the compiler in the build process.

We’ve already seen one of these in the sample program we ran earlier.

```cpp title="main.cpp" hl_lines="1"
#include <iostream>

int main() {
	std::cout << "Hello World!\n";
}
```

``` title="output"
Hello World!
```

The highlighted line tells the preprocessor to find the file named `iostream` and copy-paste its entire contents into this file at that exact location.
This makes all the functionality defined in `<iostream>` — such as input/output using `std::cout` (console output), `std::cin` (console input) — available in our program.

!!! note

    This is only a brief introduction to what the preprocessor really is.  
    The `#include` directive is just one of many, and preprocessors themselves are part of a much broader topic involving macros and metaprogramming—concepts we’ll gradually work our way toward much later in the book.


### Compiler

The C++ compiler transforms your human-readable code into a format that computers can understand—but it does so in several stages.

First, it creates intermediate files—typically with a `.obj` extension on Windows or `.o` on macOS/Linux.
These files are usually found inside your project directory, often in folders like `x64/Debug` or `build/`.

During compilation, the compiler first processes all preprocessor directives, then tokenizes and parses your code.
Parsing builds an internal representation called an Abstract Syntax Tree (AST), which describes the structure and meaning of your program.

Once the AST is ready, the compiler generates machine code, producing a binary object file based on your original source code.

Here’s a sample of what a raw `.obj` file might look like.

``` title="main.obj" linenums="0"
00000000 64 86 6B D0 D7 95 06 69 BC 02 01 00 3E 02 00 00 d.k....i....>...
00000010 00 00 00 00 2E 64 72 65 63 74 76 65 00 00 00 00 ....drectve...
00000020 00 00 00 00 CA 01 00 00 CC 10 00 00 00 00 00 00 ................
00000030 00 00 00 00 00 00 00 00 00 0A 10 00 2E 64 65 62 ........ .deb
00000040 75 67 24 53 00 00 00 00 00 00 00 CC AC 00 00 00 ug$S............
00000050 96 12 00 00 62 BF 00 00 00 00 00 00 EE 00 00 00 ....b...........
00000060 40 00 10 42 2E 62 73 73 00 00 00 00 00 00 00 00 @..B..bss.......
00000070 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 ................
00000080 00 00 00 00 00 00 00 00 80 10 30 C0 2E 6D 73 76 ........ .msv
00000090 63 6A 6D 63 00 00 00 00 00 00 00 00 00 00 00 00 cjmc............

... (truncated for brevity)
```

``` title="main.asm" linenums="0"
.LC0:
        .string "Hello World!\n"
main:
        push    rbp
        mov     rbp, rsp
        mov     esi, OFFSET FLAT:.LC0
        mov     edi, OFFSET FLAT:std::cout
        call    std::basic_ostream<char, std::char_traits<char>>& std::operator<<<std::char_traits<char>>(std::basic_ostream<char, std::char_traits<char>>&, char const*)
        mov     eax, 0
        pop     rbp
        ret
```

You don’t need to understand these low-level details right now—they’re just examples of how code is represented internally at different stages of compilation.
Keep in mind that these snippets don’t directly correspond to one another—they’re simplified excerpts from larger generated files.

!!! tip

    You can easily explore how your C++ code is compiled into assembly using Compiler Explorer:
    <a href="https://godbolt.org/" target="_blank" rel="noopener noreferrer">https://godbolt.org/</a>

Each preprocessed source file in your project becomes what’s known as a translation unit.
The compiler processes each translation unit independently, generating one object file for each.

If your program contains multiple source files, the compiler will produce multiple object files—one per translation unit.
These object files are then passed to the linker, which combines them into the final executable or library.
If your program consists of only a single source file, you’ll end up with just one object file.


### Assembler

Once the compiler generates low-level instructions from your translation units, these instructions are still not in a format the CPU can execute directly—they’re stored as human-readable assembly code inside object files.

This is where the assembler comes in.
The assembler converts those assembly instructions into pure machine code—binary instructions made up of 0s and 1s that the CPU can understand and execute directly.

In practice, this step is handled automatically by the compiler toolchain, so you rarely interact with the assembler directly.
However, it’s useful to know that this step exists because it explains why compiled languages like C++ are so fast: once assembled, the resulting machine code runs natively on the hardware without any intermediary layer like.


### Linker

After the compiler processes each translation unit into object files, the linker’s job is to bring everything together into a final executable.
Without linking, each translation unit only knows about its own contents, so the program can’t run as a whole.

The linker connects all the pieces by resolving function calls, variables and other symbols across translation units.
Even if your program consists of just a single translation unit, the linker still plays an important role—it locates the program’s entry point so execution knows where to begin.

Below is an example illustrating a common linker error.

```cpp title="main.cpp"
#include <iostream>

int Multiply(int a, int b); // Function declaration
// Missing function definition 

int main() {
	std::cout << Multiply(3, 7) << '\n';
}
```

``` title="output"
Linker Error: unresolved external symbol
```

Linker errors appear only after the program has been compiled and the linker tries to combine all object files into one executable.
Although each individual source file may compile successfully on its own, the linker might still fail if it can’t find a function or variable definition that was declared elsewhere.

In this example, the problem occurs because the function `Multiply` is declared but never defined.
The linker tries to connect the `Multiply` call to actual machine code, but it can’t find it—so the build fails.

You can fix the error by simply providing the function definition.

```cpp
#include <iostream>

int Multiply(int a, int b) {
	return a * b;
}

int main() {
	std::cout << "Multiplication: " << Multiply(3, 7) << '\n';
}
```

``` title="output"
Multiplication: 21
```

This might seem like a simple mistake, but in large projects with dozens of translation units and external libraries, linker errors like this can be surprisingly tricky to diagnose and fix.


## Header Files

We mentioned earlier that in C++, there are two main types of files: source files and header files.

Source files (like the ones we’ve been working with so far) contain the actual implementation and logic of your program.
Each source file gets compiled into an object file, forming what’s known as a translation unit.

The second type is the header file.
You’ve already seen one in the sample your IDE generated.

```cpp title="main.cpp" hl_lines="1"
#include <iostream> // iostream is the name of a header file

int main() {
	std::cout << "Hello World!" << '\n';
}
```

``` title="output"
Hello World!
```

Header files are used primarily for declarations—not definitions.
They describe what exists (functions, classes, constants, etc.), but not how they work.
By including a header, you tell the compiler: “Hey, these functions and objects exist somewhere—trust me, you’ll find their definitions later during linking.”

Without these headers, the compiler would be completely unaware of things like std::cout, and you’d get a long list of errors, as shown below.

``` title="example"
<source>: In function 'int main()':
<source>:2:14: error: 'cout' is not a member of 'std'
    2 |         std::cout << "Hello World!\n";
      |              ^~~~
<source>:1:1: note: 'std::cout' is defined in header '<iostream>'; this is probably fixable by adding '#include <iostream>'
  +++ |+#include <iostream>
    1 | int main() {
Compiler returned: 1
```

Let’s look at a simple example using our own header file.

```cpp title="log.h"
#pragma once

void Log(const char* message);
```

```cpp title="log.cpp"
#include <iostream>

void Log(const char* message) {
	std::cout << message << '\n';
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

Here, `main.cpp` includes `log.h`, which declares the `Log` function, while the actual implementation lives in `log.cpp`.
As we’ve learned, `#include` is a preprocessor directive that tells the compiler to copy the contents of the specified file into the current translation unit.

When compiled, both files become separate translation units.
The linker then connects them by matching the function call in `main.cpp` with the definition in `log.cpp`.

That’s why `main.cpp` knows about the `Log` function—even though it’s implemented elsewhere.
Without the header file, the compiler would have no idea what `Log` is and would throw an error during compilation, never making it to the linking stage.


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
While a bit more verbose than `#pragma once`, it’s more widely supported and commonly used.

```cpp title="log.h"
#ifndef LOG_H_
#define LOG_H_

void Log(const char* message);

#endif
```

This pattern works by checking whether `LOG_H_` has already been defined.
If not, it defines it and includes the content inside.
If the file has already been included elsewhere, the compiler skips its contents—avoiding duplicate declarations.

### Header File Search

In `main.cpp`, we used two different notations when including header files.

```cpp
#include <iostream>
#include "log.h"
```

The difference between them lies in where the compiler looks for the header file.

| Syntax            | Search Path                                                                                   | Typical Use Case                           |
| ----------------- | --------------------------------------------------------------------------------------------- | ------------------------------------------ |
| `<...>`           | Searches system and standard library include paths.                                           | For standard headers like `<iostream>`.    |
| `"..."`           | Searches relative to the including file, usually starting with the current working directory. | For project-specific headers like `log.h`. |


## Comments

Comments are lines in your code that are completely ignored by the compiler.
This means they have no effect on what the program does—they exist solely for humans to read.

Comments are essential because, as programs grow larger and more complex, it becomes necessary to document your code.
This helps others—or even your future self—understand what the code is doing and why certain decisions were made.

However, keep in mind that good code should be self-documenting.
Avoid writing comments that merely restate what the code already makes obvious, as comments should explain why the code does something, not what it does.

Use comments to:

- Document non-obvious behavior or complex algorithms.
- Leave notes for future improvements or reminders.
- Temporarily disable code for debugging or testing purposes.

### Comment Types

| Type                    | Syntax      | Description                                                   |
| ----------------------- | ----------- | ------------------------------------------------------------- |
| Single-line comment     | `//`        | Anything after `//` on the same line is treated as a comment. |
| Multi-line comment      | `/* ... */` | Used for longer explanations that span multiple lines.        |

```cpp title="main.cpp"
#include <iostream> // This is a single-line comment

/* 
    This is a multi-line comment.
    It can span multiple lines without needing // on each line. 
*/ 

int main() {
    std::cout << "Hello World!\n";
}
```

``` title="output"
"Hello World!"
```

## Data Types and Variables

In programming, data types and variables are fundamental concepts.
Programming is ultimately about working with data—and to work with it effectively, two things must happen.

| Requirement | Description                                                                                                  |
| ----------- | ------------------------------------------------------------------------------------------------------------ |
| Storage     | Data must be placed somewhere in the computer’s memory.                                                      |
| Typing      | The program must understand what kind of data it’s dealing with (e.g., a number, a character, or a boolean). |


To represent different kinds of data, we use data types.
To actually hold that data in memory, we use variables.

Even though all data in memory is ultimately stored as binary, data types give those raw bits meaning.
The main differences between data types come down to how much memory they use and how those bits are interpreted.

!!! info

    Data types are important enough to deserve their own full chapter—but for now, this brief introduction will do just fine.


### Primitive Data Types

The most basic data types in C++ are called primitive types. They form the foundation for all more complex types you’ll encounter later.

| Type     | Name                        | Description                                                                 | Size (bytes)                    |
| -------- | --------------------------- | --------------------------------------------------------------------------- | ------------------------------- |
| `int`    | Integer                     | Stores whole numbers                                                        | Typically 4                     |
| `float`  | Floating-point number       | Stores numbers with decimals. Precision is limited.                         | 4                               |
| `double` | Double-precision float      | Similar to `float`, but with more precision.                                | 8                               |
| `char`   | Character                   | Stores a single ASCII character (e.g., 'A', 'b', '#').                      | 1                               |
| `bool`   | Boolean                     | Stores only `true` (1) or `false` (0).                                      | 1 (implementation-defined)      |

!!! tip

    In programming, instead of using spaces to separate large numbers for readability (which would cause a syntax error), you can group digits using single quotes.
    For example, instead of writing 1976834, you can write 1'976'834—this makes the number easier to read, especially when dealing with thousands or millions.


### ASCII

When we talk about text or characters in code, what we’re really dealing with under the hood are just numbers.

Each character—whether a letter, digit, or symbol—is stored as an integer value defined by the ASCII (American Standard Code for Information Interchange) standard.
ASCII assigns a unique numeric code to each printable and control character.

| ASCII Code | Character |
| ---------- | --------- |
| 65         | A         |
| 66         | B         |
| 97         | a         |
| 48         | 0         |


That means characters can participate in arithmetic operations just like integers.

So, whenever you see a character literal, remember: it’s really just an integer with a special meaning attached.

You can explore the full ASCII table here: <a href="https://www.asciitable.com/" target="_blank" rel="noopener noreferrer">https://www.ascii-code.com/</a>

### Declaring and Naming Variables

A variable is a named storage location for data in memory.
When we create a variable, we assign it a data type and name so that we can refer to it later in our program.

Rules for Naming Variables:

- Must start with a letter or underscore, but cannot start with a number.
- Can contain letters, numbers, and underscores, but no spaces.
- Cannot use C++ reserved keywords (e.g., `int`, `float`, `return`).
- Should be descriptive (e.g., `user_age` instead of `x`).

We assign values to variables using the `=` symbol, called the assignment operator.
There are a few important rules to keep in mind when assigning values:

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

    std::cout << "User age: " << user_age << '\n';
    std::cout << "PI: " << pi << '\n';
    std::cout << "User grade: " << user_grade << '\n';
    std::cout << "Underaged? " << underaged;
}
```

``` title="output"
User age: 25
PI: 3.14
User grade: 25
Underaged? 0
```

In C++, each declaration or statement must end with a semicolon `;`.
This symbol tells the compiler that the statement is complete.

!!! danger

    You might come across codebases or developers who declare multiple variables of the same type on a single line, as shown in the snippet below.  

    ```cpp linenums="0"
    int number_one, number_two;
    ```

    While this is allowed and will work, it’s considered a bad habit because it reduces readability and can lead to subtle bugs.


Additionally, in the example above, you might have noticed a specific way of naming variables. This naming style is called snake_case,
where all characters are lowercase and words are separated by underscores (e.g., user_age, player_score). This style improves readability, especially in longer variable names, and is commonly used in many C++ codebases.

!!! info

    Throughout this book, we will follow the Google C++ Style Guide, which recommends using snake_case for variable names and PascalCase for function and class names.

    You can learn more about these conventions and other best practices here: 
    <a href="https://google.github.io/styleguide/cppguide.html" target="_blank" rel="noopener noreferrer">https://google.github.io/styleguide/cppguide.html</a>


### Constants

Sometimes, we want our stored data to be read-only.
To achieve this, we use the `const` keyword in front of a variable declaration, which ensures the variable remains unchangeable after initialization.

```cpp title="main.cpp"
#include <iostream>

int main() {
    const int number = 54;

    number = 34;

    std::cout << number; 
}
```

``` title="output"
error: assignment of read-only variable ‘number’
```

As shown in the example above, a constant variable cannot be reassigned a value—it results in an error.


## Console Input and Output

So far, we've focused on how programs come together and store data internally.
Equally important, however, is understanding how data enters and exits a program.
While programs can receive input and produce output through many sources—such as files, databases, or different APIs—the most fundamental method is through console input and output.

In C++, this is handled by the standard library header `<iostream>`, which provides tools for displaying messages to the user and reading input from the console.

The following example demonstrates how to use `std::cout` to prompt the user and `std::cin` to read their response.

```cpp title="main.cpp"
#include <iostream>

int main() {
    int user_age;

    std::cout << "Enter your age: ";
    std::cin >> user_age;

    std::cout << "Entered age was - " << user_age;
}
```

``` title="output"
Depends on the input you provided
```

It can be tricky at first to remember which symbols to use for input and output streams.
A helpful way to visualize them is to think of them as arrows showing the direction of data flow.

| Type   | Statement               | Description                                          |
| ------ | ----------------------- | ---------------------------------------------------- |
| input  | `std::cin >> variable;` | Data flows from the console into the variable.       |
| output | `std::cout << message;` | Data flows from the program out to the console.      |

### Additional Output Streams

Up to this point, we’ve only used `std::cout` to send output to the console.
In practice, however, C++ provides three standard output streams, each intended for a slightly different purpose.

Alongside `std::cout`, we are also provided with `std::cerr` and `std::clog`.

All three can print text to the console using the same syntax, but `std::cerr` and `std::clog` are primarily intended for diagnostic output—such as error messages, warnings, or debugging information.
As you’ll see in the next subsection, the key difference between them lies in how their output is handled internally.

This distinction may not matter much in very small programs, but it becomes important as programs grow larger.

### Stream Buffers

When we output something to the screen, it may feel like the text appears instantly.
Behind the scenes, however, the process is a little more involved.

Every time a program writes output, it must make a system call—a request to the operating system—to display text on the console.
System calls are relatively slow, and performing one for every small piece of output would be inefficient.

To avoid this, C++ uses stream buffers.

A stream buffer is a temporary area in memory that collects output data before it is actually written to the console or a file.
Instead of sending each character immediately, the program stores output in the buffer and writes it out in larger chunks.

The contents of a buffer are written out (this is called flushing) when one of the following occurs:

- The buffer becomes full
- An explicit flush is requested, such as by using `std::flush` or `std::endl`
- The program finishes execution normally

This buffering behavior is why different output streams exist.
For example, `std::cerr` is typically unbuffered, ensuring that error messages appear immediately, while `std::clog` is buffered, making it more suitable for logging.

One subtle point to note is that `std::cout` is tied to `std::cin` by default.
This means that whenever your program reads input from `std::cin`, `std::cout` is automatically flushed first.
This ensures that prompts or messages appear before the program waits for input.
`std::clog`, on the other hand, is not tied to `std::cin`, so it does not flush automatically when reading input.

!!! warning

    One small caveat is worth keeping in mind when mixing output streams.
    Since `std::cerr` is typically unbuffered, its output is written immediately, while output sent to buffered streams like `std::cout` or `std::clog` may be delayed.

    As a result, error messages written to `std::cerr` can sometimes appear before earlier output, even though the program executed the statements in the expected order.

    When the exact order of diagnostic messages matters—such as when logging program behavior—it can be preferable to use std::clog, which is buffered and preserves a more predictable output sequence.

### Newlines and Flushing

In many classic C++ examples, you might see the following line.

```cpp title="main.cpp" hl_lines="4"
#include <iostream>

int main() {
    std::cout << "Hello World!" << std::endl;
}
```

This works fine, but it can be misleading.
Unlike the newline character `\n`, `std::endl` not only moves the output to a new line—it also forces the output buffer to flush immediately.
Internally, `std::endl` expands to the following.

```cpp title="main.cpp" hl_lines="4"
#include <iostream>

int main() {
    std::cout << "Hello World!" << '\n' << std::flush;
}
```

For small programs, this difference doesn’t matter.
But in larger programs or when printing lots of text quickly, forcing a flush on every line can become a performance bottleneck.


!!! note

    In modern C++, we should avoid using `std::endl` altogether because it forces a flush every time, slowing down our program.
    If we want a newline, we should use the newline character `\n`.
    
    When we really need to flush the output immediately—for example, when printing error messages or debugging information.
    We have streams like `std::cerr` which are unbuffered and automatically print immediately, so in those cases, we do not need `std::endl` for flushing either.

## Questions

=== "question 1"

    How many entry points can a valid C/C++ program have?

=== "answer"

    Exactly one — the main function.

---

=== "question 2"

    How does the `#include` preprocessor directive work?

=== "answer"

    It copies the contents of the specified file directly into
    the location where it was included.

---

=== "question 3"

    What is a preprocessed source file called?

=== "answer"

    A translation unit.

---

=== "question 4"

    During the compile step, does the compiler require a full function
    definition, or is a declaration enough?

=== "answer"

    A declaration is enough — the actual connection to the definition
    happens during the linking phase.

---

=== "question 5"

    What does a header guard protect against?

=== "answer"

    Against multiple inclusion of the same header file.

---

=== "question 6"

    Name the primitive data types mentioned in this chapter
    and their typical byte sizes.

=== "answer"

    int    - 4  
    float  - 4  
    double - 8  
    char   - 1  
    bool   - 1

---

=== "question 7"

    Which header provides input and output stream functionality in C++?

=== "answer"

    `<iostream>`

---

=== "question 8"

    What is a stream buffer used for?

=== "answer"

    It temporarily stores output data before sending it to the terminal or file.

---

=== "question 9"

    List the standard output streams covered in this chapter and explain their primary differences and typical use cases.

=== "answer"

    - `std::cout` — Normal program output; buffered.
    - `std::cerr` — Error messages; unbuffered, prints immediately.
    - `std::clog` — Logging and diagnostic messages; buffered, prints in order, useful for tracking program behavior.

    Primary differences:
    
    - Buffered vs unbuffered (affects when output appears)
    - Intended purpose (normal output vs error vs logging)
    - `std::cerr` is immediate, `std::clog` preserves order like `std::cout`

---

=== "question 10"

    Explain difference between a `\n` and `std::endl` for writing a new line.

=== "answer"

    `\n` only adds a newline character to the output.
    `std::endl` adds a newline and flushes the output buffer,
    which can slow the program down if used excessively.


## Exercises

=== "exercise 1"

    Write a program that prints your first name on one line 
    and your surname on the next line in the terminal.


=== "answer"

    ```cpp  
    #include <iostream>

    int main() {
        std::cout << "Name\n";
        std::cout << "Surname";
    }
    ```

---

=== "exercise 2"

    Write a program that asks the user for their age, stores it in a variable, and then prints it back to the command line.
    
=== "answer"

    ```cpp
    #include <iostream>

    int main() {
        int user_age;

        std::cout << "Enter your age: ";
        std::cin >> user_age;

        std::cout << "User age: " << user_age;
    }
    ```
