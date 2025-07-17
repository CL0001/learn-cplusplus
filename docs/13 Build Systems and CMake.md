# 13 Build Systems with CMake

At this point, we want to build more than just command-line tools and utilities.
Those were a necessary starting point—but now it's time to scale up.
As C++ isn’t just used for small scripts; it’s the backbone of massive systems, game engines, and high-performance cross-platform applications.
And since these kinds of projects are often meant to run on more than one platform, we’ll need a cross-platform build system to streamline the entire development and deployment process.

That’s not to say platform-specific tools don’t exist—they absolutely do.
Each major IDE comes bundled with its own build system, and so far we’ve relied on tools like Microsoft Visual Studio or Apple’s Xcode, both of which handle that part for us.
These are powerful environments, but they lock you into their ecosystems, making it difficult to move between platforms or collaborate with developers using different setups.
And learning a different build system for every environment just isn’t practical.
It’s far more efficient to master one flexible, portable tool that works everywhere.
That’s why cross-platform build systems are essential.

This build system we've been mentioning automates the process of compiling, linking, and packaging our code.
It manages file dependencies, handles platform-specific quirks, and generates all the necessary build files for our compiler or IDE.
And when that build system is cross-platform, it means you can define our build logic once and have it work on Windows, macOS, or Linux without needing to rewrite anything.

Also a build system becomes especially important when you're using third-party libraries, switching between compilers, or integrating tools like formatters, linters, and static analyzers.

Fortunately, there are several solid options to choose from—but only one is widely recognized as the industry standard and best suited for beginners: CMake.
It’s the most complete and flexible solution available, and it's already used by countless libraries and projects across the C++ ecosystem.
Other notable examples include Bazel, developed by Google with a focus on speed and scalability, and Premake, which uses a Lua-based syntax and aims for readability and simplicity.

So in this chapter, we’ll focus on CMake: how it works, and how to use it in our projects.

## Getting Started

CMake is a standalone build system, which means it needs to be installed separately.
Fortunately, installation is straightforward on all major platforms and can be done in a few simple steps.

The easiest way is through the official CMake website:
<a href="https://cmake.org/download/" target="_blank">https://cmake.org/download/</a>.

Scroll down to the Binary Distributions section, choose the latest version available, and download the installer that matches our operating system and system architecture.
Then just run the installer and follow the default steps—it’s a simple next-next-finish process on most platforms.

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
We only need to write a single `CMakeLists.txt` file, and CMake takes care of generating the appropriate build files for whatever environment we’re targeting.

This approach not only keeps our source code tidy but also makes it easier to manage multiple builds—such as Debug and Release—simultaneously by using separate build directories for each configuration.

```
cmake-example-project/
├── build/
├── include/
│   └── calc.h
├── src/
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
    SOURCES
    include/calc.h src/calc.cpp
    src/main.cpp
)

add_executable(${PROJECT_NAME} ${SOURCES})

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

Let's start by recreating the file structure shown above and placing the example code into the respective files.
It is neccessary as we will build upon this example in the next few sections.

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

    This tells CMake to create an executable with the project name (`${PROJECT_NAME}` is a variable referencing the project name defined earlier) from the listed source files.

6. `target_include_directories()`

    This sets the include paths for the compiler.
    Here, we add the include directory as a place to look for header files, so your source files can `#include "log.h"` without needing to write relative or absolute paths.

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

    This doesn’t apply to small helper functions or quick utilities you might define in a single translation unit.
    A library is typically for stable, well-defined code that doesn’t change often.
    Once finished, we rarely touch it—and that means we can save time during the build process, since CMake won't recompile it every time the main program changes.

In CMake, we can define a library using the `add_library()` command, along with a few optional tweaks to control how it's built and linked.

let build upon the cmakelists and filestructure from the previous section and add to it a librry

```
cmake-example-project/
├── build/
├── include/
│   └── calc.h
├── libs/
│   └── logger/
│       ├── logger.cpp
│       └── logger.h
├── src/
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
    libs/logger/logger.h
    libs/logger/logger.cpp
)

target_include_directories(logger PUBLIC libs/logger)

set(
    SOURCES
    include/calc.h src/calc.cpp
    src/main.cpp
)

add_executable(${PROJECT_NAME} ${SOURCES})

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

🚧 This chapter is under construction... 🚧