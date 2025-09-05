# 13 Build Systems with CMake

At this point, we want to build more than just command-line tools and utilities.
Those were a necessary starting point—but now it's time to scale up.
As C++ isn’t just used for small scripts; it’s the backbone of massive systems like game engines, CAD software and 3D tools, as well as complex systems in aerospace and robotics.
Since these projects often target multiple platforms, we’ll need a cross-platform build system to streamline development, integration and deployment.

A build system is a tool that automates the process of compiling, linking, and packaging code.
It manages file dependencies, handles platform-specific quirks, and generates the necessary build files for your compiler or IDE to produce an executable.
When the build system is cross-platform, it means you can define your build logic once and have it work seamlessly on Windows, macOS, or Linux—without rewriting anything.

The need for a cross-platform build system doesn’t arise from a lack of platform-specific tools—they absolutely exist.
Each major IDE comes bundled with its own build system, such as MSBuild in Microsoft Visual Studio or the build tools integrated into Apple’s Xcode.
These environments are powerful, but they tend to lock you into their ecosystems, making it harder to move between platforms or collaborate with developers using different setups.
And realistically, learning a different build system for every platform just isn’t practical.

What developers really need is a unified solution.
It’s far more efficient to master a single, flexible, and portable tool that works everywhere.
Also a cross-platform build system becomes especially valuable when you're using third-party libraries, switching between compilers, or integrating tools like formatters, linters, and static analyzers.
That’s why these systems are essential for maintainable development.

The thing is, as we've seen with IDEs being tied to their respective operating systems, C++ development isn’t as unified as in some more modern languages.
There’s no single, official cross-platform build system endorsed by the language itself.
Instead, the community relies on a variety of tools—some more established than others.

Fortunately, several solid options exist, but only one has emerged as the de facto standard—CMake.
It’s flexible, powerful, and widely used by major libraries and projects across the C++ ecosystem, making it the most worthwhile build system for beginners to learn and an essential skill for any C++ developer.

Other noteworthy build systems include Bazel, developed by Google with an emphasis on performance and large-scale builds, and Premake, which opts for a Lua-based configuration style that prioritizes readability and simplicity.
Still, if you're just starting out—or even working on serious cross-platform projects—CMake remains the best place to begin.

Some might argue—just like with C++ itself—that CMake is outdated and hard to write because of its unusual and unreadable syntax and behaviour.
While that may have been true a decade ago, modern CMake has evolved into a comprehensive and much more approachable tool.
That’s why, in this chapter—and as our build system of choice—we’ll focus on CMake: how it works and how to effectively use it in our projects.

## Getting Started

As mentioned, CMake is a standalone cross-platform build system, which means it must be installed separately.
Fortunately, installation is straightforward on all major platforms and can be done in a few simple steps.

The easiest way is through the official CMake website:
<a href="https://cmake.org/download/" target="_blank">https://cmake.org/download/</a>.

We then have to scroll down to the "Binary Distributions" section, choose the latest version available, and download the installer that matches our operating system and system architecture.
Then just run the installer and follow the default steps—it’s a simple next-next-finish process.

Alternatively, we can install CMake through a package manager available on our platform, such as winget, Homebrew, or Chocolatey.
This can be an even quicker option if you’re already comfortable with the terminal, but either method will get you to the same result.

## CMake Basics

The first thing to understand is that CMake has its own scripting language, and it expects build scripts to be placed in files named `CMakeLists.txt`—this is the specific filename CMake looks for when configuring a project.
There’s no limit to how many of these files a project can contain—each subdirectory can have its own—but just like in C++, there needs to be a clear entry point.
For CMake, that entry point is the root `CMakeLists.txt` file located at the top level of our project.
This is where the build configuration begins.

It's also highly recommended to use a dedicated `build/` or `out/` directory.
This is where CMake generates all the necessary build files, tailored specifically to our platform and compiler.
Keeping these generated files separate helps maintain a clean project structure, free from clutter.

The contents of this directory will vary depending on our system, compiler, and build tools, but that’s exactly where CMake’s power comes through.
We only need to write a single `CMakeLists.txt` file, and CMake takes care of generating the appropriate build files for whatever environment we’re using.

This approach not only keeps our source code tidy but also makes it easier to manage multiple builds—such as Debug and Release—simultaneously by using separate build directories for each configuration.

To get started, we'll begin with the simplest example and cover all the basics you need to understand to successfully configure and build a project using CMake.

First, recreate the file structure shown below and place the example code into the corresponding files.
This setup is necessary because we’ll build upon this example in the upcoming sections.

```
cmake-example-project/
├── build/
├── include/
│   └── calc.h
├── source/
│   ├── calc.cpp
│   └── main.cpp
└── CMakeLists.txt
```

```cpp title="calc.h"
#pragma once

int Sum(int arr[], int size);
```

```cpp title="calc.cpp"
#include "calc.h"

int Sum(int arr[], int size) {
    int sum = 0;

    if (size <= 0)
        return 0;

    for (int i = 0; i < size; ++i)
        sum += arr[i];

    return sum;
}
```

```cpp title="main.cpp"
#include <iostream>

#include "calc.h"

int main() {
    int arr[] = {1, 2, 3, 4, 5};
    std::cout << "Sum of array: " << Sum(arr, 5) << std::endl;
}
```

```cmake title="CMakeLists.txt"
cmake_minimum_required(VERSION 3.15...4.0)

project(
    CMakeExampleProject
    VERSION 0.1.0
    DESCRIPTION "A simple CMake project"
    LANGUAGES CXX
)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(
    PROJECT_SOURCES
    include/calc.h
    source/calc.cpp
    source/main.cpp
)

add_executable(${PROJECT_NAME} ${PROJECT_SOURCES})

target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_SOURCE_DIR}/include)
```

``` title="output"
Sum of array: 15
```

!!! note

    CMake functions are case-insensitive, so you can write them as `SET()` or `set()`.
    However, the convention is to use snake_case, like `set()` or `add_executable()`.

!!! info

    For versioning, it’s best to follow a consistent versioning system such as Semantic Versioning.
    This approach uses a `MAJOR.MINOR.PATCH` format to clearly communicate the scope of changes:

    - MAJOR version when you make incompatible API changes
    - MINOR version when you add functionality in a backwards-compatible manner
    - PATCH version for backwards-compatible bug fixes

    Adopting semantic versioning helps maintain clarity and predictability as your project evolves.

    More info on Semantic Versioning can be found here: <a href="https://semver.org/" target="_blank">https://semver.org/</a>

1. `cmake_minimum_required()`

    This is the first line in every root `CMakeLists.txt`.
    It sets the minimum required CMake version to 3.15 and supports any version up to (but not including) 4.0.
    The `...` syntax means "compatible with versions from 3.15 up to 4.0."
    This range indicates the versions that were tested and should work correctly with your project.

2. `project()`

    Defines the project name "CMakeExampleProject", version "0.1.0", description, and the programming languages used.
    CMake supports several languages including C, CXX (C++), Fortran, ASM, CUDA, CSharp, and Swift.
    Here we specify only CXX since it's a C++ project.

3. `set()`

    Used to create or modify variables.
    Here, `CMAKE_CXX_STANDARD` is set to 20, which means the project uses C++20.
    `CMAKE_CXX_STANDARD_REQUIRED ON` means this version is mandatory — the compiler must support C++20 or higher.

4. `set(SOURCES ...)`

    We define a variable named SOURCES listing all the source files and header files used in the project.
    Notice we also include header files even though they are not compiled directly.
    This is because CMake also informs IDEs and editors about which files belong to the project.
    Without listing headers here, tools like Visual Studio won't show them in the project explorer, or enable features like search and navigation properly.

5. `add_executable()`

    This tells CMake to create an executable with the project name `${PROJECT_NAME}` (variable referencing the project name defined in `project()`) from the listed source files.

6. `target_include_directories()`

    This sets the include paths for the compiler.
    Here, we add the include directory as a place to look for header files, so your source files can `#include "log.h"` without needing to write relative or absolute paths.

    | Access Modifier   | Used By Current Target   | Propagated to Dependent Targets  | Description                                                                |
    |-------------------|--------------------------|----------------------------------|----------------------------------------------------------------------------|
    | `PRIVATE`         | Yes                      | No                               | The include path (or library link) is used only by this target.            |
    | `PUBLIC`          | Yes                      | Yes                              | Used by this target and also passed on to targets that link to it.         |


!!! info

    To add comments in `CMakeLists.txt`, simply put a `#` at the start of each line.

Generating the build files with CMake and then building the project is straightforward.
First, open a terminal inside the `build/` directory—or navigate to it if terminal is already open.
Once there, run these two simple commands.

1. `cmake ..`

    Generates the build files in the current directory based on the CMakeLists.txt in the parent folder.

2. `cmake --build .`

    Builds the project using the generated build system in the current directory.

The build executable is defaultly located inside the `build/Debug/` directory.

## Creating a Library

When a project includes larger, reusable components—such as a logging system or a configuration manager—that are used across multiple parts of the codebase, it's a good practice to separate them into their own modules, commonly referred to as libraries.
These libraries represent code that functions as a self-contained unit and typically doesn't change often during development.
By isolating them, we avoid the need to recompile everything from scratch every time we build the project—saving time and improving build efficiency.

!!! note

    This doesn’t apply to small helper functions or quick utilities.
    A library is typically for stable, well-defined code that doesn’t change often.
    Once finished, we rarely touch it—and that means we can save time during the build process, since CMake won't recompile it every time the main program changes.

In CMake, we can define a library using the `add_library()` command, along with a few optional tweaks to control how it's built and linked.

Let’s build upon the file structure and code from the previous section by adding a library.

```
cmake-example-project/
├── build/
├── include/
│   └── calc.h
├── libs/
│   └── logger/
│       ├── logger.cpp
│       └── logger.h
├── source/
│   ├── calc.cpp
│   └── main.cpp
└── CMakeLists.txt
```

```cpp title="logger.h"
#pragma once

namespace logger {
    void LOG(const char* message);
    void LOG(int value);
}
```

```cpp title="logger.cpp"
#include "logger.h"

#include <iostream>

void logger::LOG(const char* message) {
    std::cout << message << std::endl;
}

void logger::LOG(int value) {
    std::cout << value << std::endl;
}
```

```cpp title="main.cpp"
#include "calc.h"
#include "logger.h"

int main() {
    logger::LOG("Hello, World!");

    int arr[] = {1, 2, 3, 4, 5};
    logger::LOG(Sum(arr, 5));
}
```

```cmake title="CMakeLists.txt"
cmake_minimum_required(VERSION 3.15...4.0)

project(
    CMakeExampleProject
    VERSION 0.1.0
    DESCRIPTION "A simple CMake project"
    LANGUAGES CXX
)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(
    logger STATIC
    libs/logger/logger.cpp
    libs/logger/logger.h
)

target_include_directories(logger PUBLIC libs/logger)

set(
    PROJECT_SOURCES
    include/calc.h
    source/calc.cpp
    src/main.cpp
)

add_executable(${PROJECT_NAME} ${PROJECT_SOURCES})

target_include_directories(${PROJECT_NAME} PRIVATE include)

target_link_libraries(${PROJECT_NAME} PRIVATE logger)
```

``` title="output"
Hello, World!
15
```

!!! note

    It's considered best practice to wrap each library in its own namespace. This helps avoid name clashes in the global namespace and makes it easier to identify which library a given function, class, or symbol comes from.

    Likewise, in larger (non-example) projects, the main project's source and header files (typically inside `src/` and `include/`) should also be wrapped inside a namespace matching the project's name.

Before we dive into these details, let’s clarify what a target means in CMake: a target is essentially a buildable entity—this could be an executable, a static library, or a shared library.
Targets are the central units CMake manages when configuring and building your project.

1. `add_library()`

    This command tells CMake to create a library target named logger.
    The keyword STATIC specifies the type of library—we’re building a static library.

    | Type       | Description                                                                                           | Produces                                            | Typical Use Case                                                                             |
    |------------|------------------------------------------------------------------------------------------------------ |-----------------------------------------------------|----------------------------------------------------------------------------------------------|
    | `STATIC`   | Compiles source files into a static library. The code is embedded into the final executable.          | `.lib` (Windows) / `.a` (Unix)                      | Useful for internal libraries or when distribution simplicity is preferred.                  |
    | `SHARED`   | Builds a dynamic/shared library. The code is loaded at runtime and kept separate from the executable. | `.dll` (Windows) / `.so` (Linux) / `.dylib` (macOS) | Ideal when sharing common code across multiple executables or applications.                  |
    | `INTERFACE`| No library is actually compiled. Used for header-only libraries or metadata targets.                  | None                                                | For header-only libraries or when only properties (e.g. include dirs) need to be propagated. |

2. `target_link_libraries()`

    This tells CMake to link the logger library with your main project executable (${PROJECT_NAME} refers to the name defined in project() earlier).

    In other words, this line connects your main program to the logger library so the functions and classes defined in it can be used by the main program.

## Programming in CMake

🚧 This chapter is under construction... 🚧