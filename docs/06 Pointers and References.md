# 06 Pointers and References

Now, we arrive at one of the most feared topics in C/C++, pointers.
Many people associate pointers with confusion, often because they tend to overthink them.
In reality, pointers are quite simple once you understand the core concept.

A pointer is essentially an integer that represents a memory address.
Think of computer memory as a long, one-dimensional array of bytes, each with a unique address, like houses lined up along a street.
Just as we wouldn’t physically move a house to show someone what it looks like, but instead give them its address so they can go and take a look, a pointer stores the address of a specific block of memory.
This approach allows programs to work directly with data without unnecessary duplication.

| Address     | 1             | 2             | 3             | 4             | ...          |
| ----------- | ------------- | ------------- | ------------- | ------------- | ------------ |
| Byte        | Byte 1        | Byte 2        | Byte 3        | Byte 4        | ...          |
| Value       | 0000 0101     | 0000 1001     | 0000 0010     | 0000 0000     | ...          |

Pointers are extremely important because everything in computing involves reading and writing to memory.
While you can write C/C++ code without directly using pointers, they offer a powerful way to manage memory.

It is also important to note that pointers themselves aren’t tied to specific data types.
Whether you're dealing with characters, booleans, integers, or any other data type, a pointer is still just a number representing a memory address.
The reason we specify a type for pointers is to indicate how much data should be read or written at that memory location.

## Memory Allocation

Memory (RAM) is where our program is loaded and where it stores data.
It is divided into two distinct regions: the stack and the heap.
The stack is typically a fixed-size memory area, often around 2 MB (depending on the platform), while the heap grows dynamically as the application runs—though it is still limited by the system and available memory.

A common misconception is that the stack and heap are stored in the CPU cache, but they actually reside in RAM.
These regions serve as the primary storage areas for variables and other essential data during program execution.

While both stack and heap memory allow us to read and write data, they differ significantly in how memory is allocated and managed.
For example, when storing an integer (typically 4 bytes), stack memory is automatically allocated and freed based on scope—once the scope is exited, the memory is released.
In contrast, heap memory must be explicitly allocated and deallocated by the programmer, and it persists until it is manually freed.
This act of requesting memory is known as memory allocation.

## Object Lifetimes

The stack region in memory can be imagined as a stack of books, to access a book in the middle, you must first remove the ones on top.
In a computers, instead of books, we push stack frames onto the stack.

When a function is called, a stack frame is created to store local variables, function parameters, return address, and other necessary information like saved registers and the previous stack frame pointer.
This structure allows the function to execute and return properly, maintaining the state of the program during its execution.
Once the function ends, the stack frame is automatically removed, and all variables within it are cleared from memory.

Scopes in C++ are not limited to functions; classes, statements, and loops also have their own scopes.
Additionally, we can create an isolated scope by enclosing code within curly brackets inside the `main` function or any other part of the program.
Variables declared inside such a block exist only within that scope and are destroyed once the block ends.

```cpp title="main.cpp" hl_lines="9"
#include <iostream>

int main() {
	{
		int number = 16;
		std::cout << number << '\n';
	}
	
	std::cout << number;
}
```

``` title="output"
Error: 'number' was already cleared from memory because the scope in which it was declared has ended.
```

On the other hand, objects allocated on the heap persist until they are manually deallocated or handled by automatic mechanisms like smart pointers.
Failing to free heap memory can lead to memory leaks, where allocated memory is never reclaimed, or heap exhaustion, where excessive allocations exceed the available heap space, potentially causing a crash.

```cpp title="example"
int* CreateArray() {
    int array[50];  // Local array on the stack
    return array;   // Returning a pointer to stack memory (dangerous!)
}

int main() {
    int* array = CreateArray(); // Dangling pointer!
}
```

This is a very common mistake, returning a pointer to a local stack-allocated array—or variables in general.
Since the array is created inside the function, it only exists within that function's scope.
Once the function returns, the stack frame is destroyed, and the array no longer exists, leaving us with a dangling pointer that points to invalid (or corrupted) memory.

## Raw Pointers

We will start by introducing `void*` (void pointer), a generic pointer that is not associated with any specific data type.
It can store the address of any variable, but since it lacks type information, we cannot directly read from or write to the memory it points to—we don't know how many bytes belong to it without first converting it to a specific pointer type.
This reinforces the idea that pointers are simply memory addresses, independent of data types.

### Declaring Pointers

Pointers are declared by appending an asterisk to a data type, followed by the variable name.
The `*` symbol is known as the dereference operator, which allows us to access the value stored at the memory address the pointer holds.

```cpp title="example"
int main() {
	void* ptr1 = 0;
	
	void* ptr2 = NULL;
	
	void* ptr3 = nullptr;
}
```

### Null Pointers

In the example above, we assigned `0` to a pointer.
Since memory addresses do not start at zero, `0` is an invalid memory address.
However, having an invalid address is a perfectly acceptable state for a pointer, as it indicates that the pointer is not currently pointing to valid memory.

We also used `NULL`, which is simply a macro-defined constant representing `0`.
While it functions the same as writing `0` directly, it improves readability.

Additionally, we introduced `nullptr`, a C++ keyword specifically designed to represent an invalid pointer.
Unlike `NULL`, `nullptr` has stronger type safety, making it the preferred choice in modern C++.

### Accessing the Memory Address of a Variable

Earlier, we mentioned that everything created in a program has a memory address that points to where the data is stored.
This applies even to simple integer variables, each variable resides at a unique location in memory.

We can access a variable's address by prefixing it with `&`, which is called the address-of operator (sometimes referred to as the reference operator).
This operator returns the memory address of the variable, allowing us to work with its location directly.

```cpp title="main.cpp"
#include <iostream>

int main() {
	int value = 8;
	void* ptr = &value; // Storing the memory address of 'value'
	
	std::cout << &value;
}
```

``` title="output"
0x7ffceeb396ac
```

!!! note

    The actual address printed will vary every time you run the program.
    This is due to Address Space Layout Randomization (ASLR) — a security feature used by modern operating systems to randomize memory addresses, making certain types of attacks harder to perform.

In this example, we assign a valid memory address to the pointer by using the address-of operator on a variable.
This ensures that the pointer correctly stores the location of `value` in memory.

### Dereferencing a Pointer

We have now reached the point where we may want to retrieve the data stored at the memory address held by a pointer.
To do this, we use the dereference operator as a prefix to the pointer variable.
This allows us to access and manipulate the value stored at that memory location, just as we would with a regular variables.

```cpp title="main.cpp"
#include <iostream>

int main() {
	int value = 8;
	void* ptr = &value;
	
	std::cout << *ptr;
}
```

``` title="output"
Error: cannot dereference a void pointer
```

This will produce an error because `void*` represents a generic memory address without a specific type.
While it can store the address of any data type, it cannot be dereferenced because the compiler does not know how many bytes belong to the variable at that address.

To fix this, we need to create an `int*` pointer for an integer value, or cast it to the correct pointer type before using it. 
This way, the compiler knows that 4 bytes (on most modern systems) after the address belong to the integer, allowing proper reading and writing of the data.

```cpp title="main.cpp"
#include <iostream>

int main() {
    int value = 8;
    int* int_ptr = &value;
    void* void_ptr = &value;

    std::cout << "Value of pointer trough int*: " << *int_ptr << '\n';
    std::cout << "Value of pointer trough cast to int*: " << *(int*)void_ptr;
}
```

``` title="output"
Value of pointer trough int*: 8
Value of pointer trough cast to int*: 8
```

### Dynamic Memory Allocation

Up to this point, all variables have been allocated on the stack, meaning their lifetime is tied to their current scope.
This is generally safe, but it also means they are automatically deleted once the scope ends, limiting their lifespan.

To preserve the existence of important values beyond their original scope, we can use pointers in combination with the `new` keyword.
This reserves memory on the heap and returns a pointer to the allocated memory.

Using `new`, we can create variables on the heap, ensuring they remain accessible even after their original scope ends—provided we manage them correctly.

```cpp title="example"
int main() {
	char* buffer = new char[8]; // Allocate 8 bytes on the heap
}
```

In the example above:

- We create a pointer of type `char*`.
- We use the `new` keyword to allocate 8 bytes of memory on the heap.
- Since a `char` is 1 byte in size, allocating an array of 8 chars reserves 8 contiguous bytes in memory.
- The pointer `buffer` stores the address of the first element in this allocated memory block.

#### Initializing Heap Memory

Heap memory is not automatically initialized, meaning it can contain garbage values.
To initialize memory, we can use the `memset` function from the `<cstring>` header file.

```cpp title="example"
#include <cstring>

int main() {
	char* buffer = new char[8]; // Allocate 8 bytes on the heap
	memset(buffer, '\0', 8);    // Set all bytes to the ASCII null character
}
```

#### Heap Memory Management

Memory allocated on the heap is not automatically freed when a variable goes out of scope.
Unlike stack memory, which is managed by the compiler, heap memory must be manually deallocated to prevent memory leaks.
A situation where memory is allocated but never freed, causing a program to consume increasing amounts of memory over time and potential heap overflow, which occurs when the program exhausts all available heap memory and can no longer allocate new data.

To properly free heap memory, we use the `delete` keyword followed by the pointer holding the allocated address.

```cpp title="example"
#include <cstring>

int main() {
    // Allocate a single integer on the heap
	int* ptr = new int(10);

    // Allocate an array of 8 characters on the heap
	char* buffer = new char[8];
	memset(buffer, 0, 8);

	delete ptr;       // Deallocate memory for the single integer
	delete[] buffer;  // Deallocate memory for the character array
}
```

When deallocating a dynamically allocated single object, we use `delete`.
However, when deallocating a dynamically allocated array, we must use `delete[]` to ensure the entire block of memory is freed correctly.

### Constant Pointers

By now, we understand how pointers work, how to create, manipulate, and delete them.
However, there are cases where we need to enforce restrictions on either the pointer itself or the data it points to.

A constant pointer applies specific constraints, ensuring that either the pointer’s address remains unchanged, the pointed-to value cannot be modified, or both.

#### Pointer to Constant

This type of pointer cannot modify the value it points to, but it can be reassigned to another address.

Use case: When you want to protect the data from being modified but allow the pointer to point elsewhere.

```cpp title="main.cpp"
#include <iostream>

int main() {
    int a = 10;
    int b = 20;

    const int* ptr = &a; // Pointer to a constant value

    // *ptr = 15; // Error: Cannot modify the value through the pointer
    ptr = &b;     // Allowed: Pointer can be reassigned

    std::cout << "Reassigned pointer to B: " << *ptr << std::endl;
}
```

``` title="output"
Reassigned pointer to B: 20
```

#### Constant Pointer

This type of pointer cannot be reassigned, but it can modify the value it points to.

Use case: When you want a pointer to always point to the same object but still allow modifications to the object.

```cpp title="main.cpp"
#include <iostream>

int main() {
    int a = 10;
    int b = 20;

    int* const ptr = &a; // Constant pointer to an integer

    *ptr = 15;   // Allowed: Can modify the value through the pointer
    // ptr = &b; // Error: Cannot reassign a constant pointer

    std::cout << "Modified variable A: " << *ptr << std::endl;
}
```

``` title="output"
Modified variable A: 15
```

#### Constant Pointer to Constant

This type of pointer cannot be reassigned and cannot modify the value it points to.

Use case: When you want a pointer to always point to the same object and ensure that the object cannot be modified through the pointer.

```cpp title="main.cpp"
#include <iostream>

int main() {
    int a = 10;
    int b = 20;

    const int* const ptr = &a; // Constant pointer to a constant value

    // *ptr = 15; // Error: Cannot modify value
    // ptr = &b; // Error: Cannot reassign pointer

    std::cout << "Unchanged pointer value: " << *ptr << std::endl;
}
```

``` title="output""
Unchanged pointer value: 10
```

### Pointer to Pointer

Since pointers are just variables that store memory addresses, it is possible to create a pointer that points to the location of another pointer.
This is known as a pointer to a pointer.
While it is theoretically possible to create multiple levels of pointers, doing so is impractical and rarely useful in real-world applications.

A pointer to a pointer is created by adding an additional asterisk to a regular pointer variable.
This means the first pointer stores the address of the second pointer, and the second pointer stores the address of the actual data.

```cpp title="main.cpp"
#include <cstring>

int main() {
	char* buffer = new char[8];
	memset(buffer, 0, 8);

	char pointer_to_buffer = &buffer; // A pointer to the pointer 'buffer'
}
```

``` title="output"
None
```

In this example:

- `buffer` is a pointer to a block of memory on the heap.
- `pointer_to_buffer` is a pointer to a pointer that holds the address of `buffer`.

While pointers to pointers can exist, they are rarely needed and should be used only when there is a valid use case.
For most applications, a single pointer is sufficient.

### Pointer Safety

Working directly with addresses and heap memory comes with risks.
If not handled correctly, it can lead to serious memory management errors, such as dangling pointers, memory leaks, buffer overflows, and undefined behavior.

In this section, we will cover common pitfalls when working with pointers and best practices to write safer and more reliable C++ code.

#### Dangling Pointers

A dangling pointer is a pointer that references memory that has already been freed or is no longer valid.
Accessing such memory leads to undefined behavior, which can result in crashes, corrupted data, or security vulnerabilities.

How Dangling Pointers Occur:

1. Deleted Memory Access - A pointer still holds an address to memory that has already been deallocated.
2. Returning Pointers to Local Variables - A pointer to a local variable is returned from a function, but the variable is destroyed when the function exits.
3. Uninitialized Pointers - A pointer is used without being properly initialized, leading it to point to an arbitrary or invalid location.

#### Memory Leaks

A memory leak occurs when dynamically allocated memory is not properly deallocated, causing the program to consume more memory over time without releasing it.
If a program continuously leaks memory, it may slow down, crash, or exhaust system resources.

Memory leaks are particularly dangerous in long-running applications, such as servers or embedded systems, where unmanaged memory growth can lead to performance degradation or failure.

#### Buffer Overflows

A buffer overflow occurs when a program writes more data into a buffer (such as an array) than it was allocated to hold.
This results in overwriting adjacent memory, potentially causing program crashes, security vulnerabilities, or unpredictable behavior.

Buffer overflows are particularly dangerous because they can corrupt data, cause segmentation faults, or be exploited by attackers to execute malicious code.

#### Pointer Aliasing and Ownership

Pointer aliasing occurs when multiple pointers reference the same memory location.
While this can be useful, it can also lead to unintended side effects, such as modifying a value unexpectedly or causing performance issues due to compiler optimizations being invalidated.

#### Preventions

Most pointer-related safety issues stem from raw pointer management and manual memory handling, which are prone to errors like memory leaks, dangling pointers, and undefined behavior.

To avoid these risks, modern C++ provides:

- Smart Pointers (`std::unique_ptr`, `std::shared_ptr`, `std::weak_ptr`) - Automatically manage memory and prevent leaks.
- STL Containers (`std::vector`, `std::array`, etc.) - Provide automatic memory management and prevent buffer overflows.

## Pointer Arithmetic

After becoming familiar with raw pointers, one of the next important aspects to understand is how to use them in practical scenarios.
This is where pointer arithmetic becomes extremely useful. Pointer arithmetic allows us to perform mathematical operations on pointers to navigate through memory locations, particularly when working with arrays or structs.

Pointer arithmetic works by adding or subtracting integer values to pointers, which effectively adjusts the memory address they point to. The pointer itself does not store the data but holds the address of the data in memory.
By performing arithmetic on pointers, we can access different elements in arrays or traverse contiguous blocks of memory.

In the example below, pointer arithmetic is used to iterate through an array of structures.

```cpp title="main.cpp"
#include <iostream>

struct Person {
    char name[64];
    int age;
};

int main() {
    Person people[5];

    Person* p_person = people; // Pointer to the first element of the array

    for (int i = 0; i < 5; ++i) {
        p_person->age = 53;      // Access and modify the age of the person
        p_person->name[0] = 'R'; // Access and modify the name of the person

        p_person++; // Move the pointer to the next Person object in memory
    }
    
    for (int i = 0; i < 5; ++i)
      std::cout << people[i].name[0] << '\t' << people[i].age << std::endl;
}
```

``` title="output"
R	53
R	53
R	53
R	53
R	53
```

Pointer arithmetic takes into account the size of the object the pointer is pointing to.
In this case, when we increment `p_person`, it doesn’t just move by 1 byte.
It moves by the size of a `Person` object, which is 68 bytes (64 bytes for `name` and 4 bytes for `age`).
This is why the pointer moves to the next `Person` in the array, not just the next byte in memory.

This topic is further built upon in the section [[07 Data Types#Type Punning|Type Punning]].

## References

Pointers and references in C++ are fundamentally similar in terms of what the computer actually does.
However, semantically, they have subtle differences.
A reference is essentially a syntax shortcut for a pointer, making the code more readable and easier to follow.

As the name suggests, a reference is used to refer to an existing variable.
Unlike pointers, a reference cannot be null and must always be bound to a valid variable.
This means you cannot set a reference to `nullptr`, and it must always refer to an existing object.

```cpp title="main.cpp"
#include <iostream>

int main() {
	int a = 10;
	int& b = a;  // b is a reference to a

    std::cout << "Reference to A: " << b << std::endl;
}
```

``` title="output"
Reference to A: 10
```

In this example, `b` is a reference to `a`, meaning it acts as an alias for `a`.
Any modifications to `b` will directly affect `a`, and vice versa.

### Reference vs. Address-of Operator

In C++, the reference operator (`&`) and the address-of operator (`&`) can sometimes be confusing, but they serve distinct purposes depending on their usage.

- When `&` is appended to the data type, it signifies a reference. A reference is simply an alias for an existing variable, meaning it acts as another name for that variable.
- When `&` is used as a prefix before a variable name, it means the address-of operator, which returns the memory address of the variable. 

### Pass by Value vs. Pass by Reference

In C++, there are two common ways to pass data into a function: pass-by-value and pass-by-reference.
Each method has its own behavior and implications.

In pass-by-value, when we pass a variable to a function, a copy of the variable is created.
The function then works with this copy, and any changes made to the parameter within the function do not affect the original variable.

```cpp title="main.cpp"
#include <iostream>

void Increment(int number) {
    number++;
}

int main() {
    int number = 5;

    Increment(number);

    std::cout << "Number: " << number << std::endl;
}
```

``` title="output"
Number: 5
```

In this case, the `Increment()` function receives a copy of the `number` from `main()`.
Inside the function, the copy of `number` is incremented, but the original `number` in `main()` remains unchanged.
As a result, the value of `number` in `main()` stays at `5`.

In pass-by-reference, instead of passing a copy of the variable, we pass the actual variable itself.
This allows the function to modify the original variable directly, and any changes made to the parameter within the function will affect the original variable outside of the function as well.

To pass a variable by reference, we use the reference operator in the function parameter list. 

```cpp title="main.cpp"
#include <iostream>

void Increment(int& number) {
    number++;
}

int main() {
    int number = 5;

    Increment(number);

    std::cout << "Number: " << number << std::endl;
}
```

``` title="output"
Number: 6
```

In this case, the `Increment()` function receives a reference to `number`, meaning it works directly with the original `number` in `main()`. As a result, after the function call, the value of `number` in `main()` is updated to `6`.

#### When to Pass by Reference

There are four main scenarios where pass-by-reference is preferred over pass-by-value:

1. Modifying Arguments
    - If a function needs to modify its arguments, you should use pass-by-reference or pass-by-pointer.
2. Avoiding Unnecessary Copies (Efficiency)
    - When a function accepts a large object as a parameter, it's better to use pass-by-const-reference.
3. Copy & Move Constructors
    - Copy and move constructors must always take a reference to avoid unnecessary object creation and to ensure proper copying or moving of objects.
4. Working with Polymorphism (Avoiding Object Slicing)
    - When working with polymorphic classes, it's essential to pass objects by reference or pointer rather than by value.
    - Passing by value may lead to object slicing, where the derived class data is lost when copied into a base class object.

## Function Pointer

So far, we have only called functions directly to execute logic.
A function serves as a symbol that we invoke whenever we want to perform a specific action.
We can also pass arguments to a function and retrieve values from it, which allows us to write more dynamic code.

```cpp title="main.cpp"
#include <iostream>

void HelloWorld() {
	std::cout << "Hello World!" << std::endl;
}

int main() {
	HelloWorld();
}
```

``` title="output"
Hello World!
```

In this example, we have a regular function declaration with a simple definition.
However, since functions are stored in memory, we can assign them to pointers.

```cpp title="main.cpp"
#include <iostream>

void HelloWorld() {
  std::cout << "Hello World!";
}

int main() {
	 
	// Declare a function pointer 'function' and assign it the address of the 'HelloWorld' function 
	void(*function)() = HelloWorld;
	
	function();
}
```

``` title="output"
Hello World!
```

In the code above, we use function pointer. The syntax may seem a bit complex at first, so let's break it down:

1. The return type of the function comes first (`void`).
2. Inside the first set of parentheses, we declare the pointer (`*function`).
3. The second set of parentheses defines the function parameters (which are empty in this case).

This declaration can be simplified using the `auto` keyword, which automatically deduces the correct type.

Another way to simplify this syntax is by creating a type alias using `using`, especially when the same type is used repeatedly.

```cpp title="main.cpp"
#include <iostream>

void HelloWorld() {
  std::cout << "Hello World!";
}

int main() {
	using HelloWorldFunction = void(*)();
	
	HelloWorldFunction function = HelloWorld;
	
	function();
}
```

``` title="output"
Hello World!
```

### Where to Use Function Pointers

In this example, we define a function pointer and pass it to another function for use, showcasing how function pointers can add flexibility to code.

```cpp title="main.cpp"
#include <iostream>
#include <vector>

void PrintValue(const int value) {
    std::cout << value << std::endl;
}

void ForEach(const std::vector<int>& values, void(*func)(const int)) {
    for (const int& value : values)
        func(value);
}

int main() {
    std::vector<int> values = {1, 2, 3, 4, 5};

    ForEach(values, PrintValue);
}
```

``` title="output"
1
2
3
4
5
```

In the example above, we pass the `PrintValue` function as a pointer to the `ForEach` function, which then uses the pointer to invoke `PrintValue` for each element in the vector.

While function pointers can still be useful in specific cases, such as when interacting with C libraries, modern C++ prefers lambda expressions and `std::function` for more flexible, type-safe, and readable code.

## Lvalues and Rvalues

In C++, understanding lvalues and rvalues is crucial because they are fundamental concepts that appear frequently in compiler warnings, error messages, and in modern C++ features like move semantics and temporary values.

An lvalue refers to a location value, something that has a persistent memory address. You can think of it as an object or a variable that you can modify or access.

In contrast, an rvalue represents a temporary value, usually something that does not have a lasting memory address. These can be literals or the results of function calls that return a temporary value.

```cpp title="main.cpp"
int main() {
    int a = 10;
}
```

``` title="output"
None
```

In the example above, `a` is an lvalue because it is a variable with a specific memory location where the rvalue `10` is stored.

- An lvalue typically appears on the left side of the `=` (assignment) operator.
- You can assign values to lvalues because they have a defined memory location.

An rvalue, however, can be more than just a literal.
For example, it can be the result of a function call that returns a value.
Rvalues do not refer to objects with persistent memory addresses and are typically used to represent temporary values.

```cpp title="main.cpp"
int GetValue() {
    return 10;
}

int main() {
    int a = GetValue();
}
```

``` title="output"
None
```

In this example, the function `GetValue()` returns an rvalue because the value `10` is a temporary result.
The rvalue cannot be assigned directly to another rvalue but can be assigned to an lvalue like `a`.

### Lvalue Reference

We explained that an rvalue is not limited to just literals; it can also be the result of a function call that returns a value.
Additionally, we can assign a value to the result of a function call.

```cpp title="main.cpp"
#include <iostream>

int PrintValue() {
	std::cout << 10 << std::endl;
}

int main() {
	PrintValue() = 5;
}
```

``` title="output"
Error: expression must be a modifiable lvalue
```

The error happens because a function typically returns an rvalue, meaning a temporary value that cannot be assigned to.
However, this is not always true, when a function contains a static variable, that variable persists across function calls and can be modified.
By returning a reference to a static variable, we allow the function to return an lvalue, making it possible to modify the value directly.

```cpp title="main.cpp"
#include <iostream>

int& GetValue() {
    static int value = 1;
    return value;
}

int main() {
    GetValue() = 5; // Reassing PrintValue() static variable to 5

    std::cout << "Function value: " << GetValue() << std::endl;
}
```

``` title="output"
Function value: 5
```

### Rvalue Reference

We have learned that when passing variables as function arguments, they are copied into a new variable created inside the function.
While passing them as lvalue references can improve performance, it also prevents passing literal values because an lvalue reference cannot bind to an rvalue (a temporary value).
To work around this, we can declare the parameter as `const`, which allows temporary values (rvalues) to be assigned to a temporary variable behind the scenes.
The compiler creates a temporary variable, assigns the literal value to it, and then binds it to the lvalue reference.

```cpp title="main.cpp"
#include <iostream>

void PrintFunction(const int& value) {
    std::cout << value << std::endl;
}

int main() {
    int a = 10;

    PrintFunction(a); // Normal pass by reference
    PrintFunction(5); // Pass rvalue by reference
}
```

That is why most C++ functions declare parameters as `const` lvalue references (`const type&`), allowing them to efficiently accept both lvalues and rvalues.
However, it is also possible to create a function parameter that accepts only temporary values (rvalues) by appending the type with `&&`, which is known as an rvalue reference.

```cpp title="main.cpp"
#include <iostream>

void AcceptLvalueAndRvalue(const int& value) {} // Accepts both
void AcceptOnlyRvalue(int&& value) {}           // Accepts only rvalues

int main() {
    int x = 5;

    AcceptLvalueAndRvalue(x);  
    AcceptLvalueAndRvalue(10);

    AcceptOnlyRvalue(10);
}
```

It is considered good practice to overload a function in modern medium-to-large projects if we want to support both lvalues and rvalues while maximizing performance.
By having an overload for `const lvalue reference` (which can accept both lvalues and rvalues) and another for `rvalue reference`, the compiler will always prefer the more specific overload when an rvalue is passed.
The key difference is that an rvalue reference (`&&`) allows moving resources from the source, as it indicates that the variable is temporary and will not persist for long.
On the other hand, a `const lvalue reference` (`const int&`) signals that the variable is important, cannot be modified, and is passed by reference to avoid unnecessary copying.

## Move Semantics

To fully understand this chapter, it is recommended to first reviewing and familiarizing yourself with [[08 Data Structures#Strings|Strings]] and [[09 User-Defined Types]].

So far, we have been introduced to lvalues and rvalues, but this is where their true purpose becomes clear.
Move semantics allows us to transfer ownership of resources from one object to another, rather than copying or referencing them.
This is particularly useful when we want to avoid the overhead of creating a duplicate and instead reuse existing resources, transferring ownership to a new scope.

!!! note

    The original object is typically left in a moved-from state, meaning it is still valid but its contents are unspecified.

For example, when passing an object into a function that takes ownership, we would normally copy it.
The same applies when returning an object from a function, we first create the object in the current stack frame and then copy it into the caller's scope.
This is not ideal, as we must construct it in one place and copy it to another, leading to unnecessary overhead.

For simple types like numbers or small structs, this overhead is minimal.
However, for complex classes, such as `std::string`, copying becomes expensive due to heap allocations.
Move semantics solves this problem by transferring ownership instead of copying the object, significantly reducing memory operations and improving performance.

```cpp title="main.cpp"
#include <cstdint>
#include <cstring>

#include <iostream>

class String {
public:
    String() : data_(nullptr), size_(0) {
        // remains empty
    }

    // Constructor that creates a new string from a C-string
    String(const char* string) {
        printf("Created!\n");
        size_ = strlen(string);
        data_ = new char[size_ + 1];
        memcpy(data_, string, size_);
        data_[size_] = '\0';
    }

    // Copy constructor
    String(const String& other) {
        printf("Copied!\n");
        size_ = other.size_;
        data_ = new char[size_ + 1];
        memcpy(data_, other.data_, size_);
        data_[size_] = '\0';
    }

    // Move constructor
    String(String&& other) noexcept
        : data_(other.data_), size_(other.size_) {
        printf("Moved!\n");
        other.data_ = nullptr;  // Reset the other object’s data pointer
        other.size_ = 0;        // Reset the other object’s size
    }

    ~String() {
        printf("Destroyed!\n");
        delete[] data_;
    }

    void Print() const {
        printf("%s\n", data_);
    }

    const char* GetData() const {
        return data_;
    }

private:
    char* data_;
    uint32_t size_;
};

class Entity {
public:
    // Copy constructor
    Entity(const String& name)
        : name_(name) {
    }

    // Move constructor
    Entity(String&& name)
        : name_(std::move(name)) {
    }

    void PrintName() const {
        printf("%s\n", name_.GetData());
    }

private:
    String name_;
};

int main() {
    // Creating an entity with an rvalue (temporary String object)
    Entity entity(String("Random String"));
    entity.PrintName();
}
```

``` title="output"
Created!
Moved!
Destroyed!
Random String
Destroyed!
```

This is a large example of implementing a custom string class.
However, it should not be considered a proper way to build a string class for real-world applications.
It uses a lot of C-style memory management, which is not ideal for production code.
The purpose of this example is purely to demonstrate how copying and moving work in C++.

By adding logs inside the copy constructor and move constructor, we can clearly observe the difference between copying and moving an object:

- The copy constructor (`String(const String& other)`) duplicates the resource by allocating new memory and copying the data from the source object.
- The move constructor (`String(String&& other) noexcept`) steals the resource from the temporary object (`other`) by transferring the pointer to the new object, rather than copying the data. After the transfer, the original object is detached by setting its pointer to `nullptr` and its size to `0`.

This behavior makes moving much more efficient than copying, especially for large objects that allocate memory on the heap, such as strings or vectors.
By moving rather than copying, we avoid unnecessary memory allocations and reduce performance overhead.

### Standard Move Function

After understanding rvalue references, move constructors, and their benefits, it’s time to introduce `std::move`, a function that allows us to explicitly move objects instead of copying them.

We’ll refer back to the `String` example from the section [[#Move Semantics|Move Semantics]], where we used `std::move` to transfer ownership of a temporary string into `m_Name` inside the `Entity` class.
To simplify the explanation, we’ll now demonstrate `std::move` using just variables of our custom `String` class.

```cpp title="main.cpp"
int main() {
	String string1 = "Random";
	String string2 = string1; // string1 is copied into string2
}
```

``` title="output"
None
```

In this example, `string1` is copied into `string2` because it is an lvalue (a named variable).
However, if we want to move its resources to `string2` instead of copying them, we need to treat `string1` as a temporary value (rvalue), because the move constructor requires an rvalue reference.

One way to do this is by casting `string1` to an rvalue reference using `(String&&)`.
However, this approach is not ideal and does not work with variables deduced using `auto`.

To solve this, we use `std::move`, which efficiently converts an lvalue into an rvalue, allowing us to move the resource without unnecessary copies.
It also makes the intention clear in the code, signaling that we are intentionally transferring ownership rather than copying.

```cpp title="main.cpp"
int main() {
	// Not recommended: Casting to rvalue reference
	// String string1 = "Random";
	// String string2((String&&)string1);
	
	String string1 = "Random";
	String string2(std::move(string1)); // Proper way to move
}
```

``` title="output"
None
```

### Move Assignment Operator

Using `std::move` inside a constructor is not the same as using it after an assignment operator.
Constructors are responsible for creating objects, while the assignment operator replaces an existing object's contents.
Since operators behave like regular functions, we need to explicitly define a move assignment operator to enable move semantics during assignment.

```cpp title="main.cpp"
class String {
    // Implementation of Regular, Copy, and Move Constructor from before
	
    String& operator=(String&& other) {
        printf("Moved!\n");
		
        if (this != &other) {
            delete[] data_;
			
            data_ = other.data_;
            size_ = other.size_;
			
            other.data_ = nullptr;
            other.size_ = 0;
        }
		
        return *this;
    }
};
```

The self-assignment check (`if (this != &other)`) is crucial. If we mistakenly attempt to move an object into itself.

```cpp 
str = std::move(str); // Dangerous
```

Without this check, the move logic would detach the object’s own data, leaving it in an invalid state.
This could lead to double deletion when the destructor runs, causing undefined behavior.
By verifying that `this` and `other` are different objects, we prevent accidental self-moves.

A move constructor is used when creating a new object from an existing one, while a move assignment operator is used when an existing object is reassigned.

A general rule in C++ is that if you implement a move constructor, you should also provide a move assignment operator, as both serve distinct but complementary purposes.

## Smart Pointers

So far, we have relied on C-style raw pointers with `new` and `delete`, requiring manual memory management.
However, this approach is error-prone, as forgetting to call `delete` can lead to memory leaks, while deleting memory incorrectly can cause undefined behavior.

To address these issues, C++ introduced smart pointers, which automate memory allocation and deallocation.
Smart pointers wrap around raw pointers and, depending on their type, automatically free memory when it is no longer needed.

Instead of using `new` directly, smart pointers provide factory functions (e.g., `std::make_unique`, `std::make_shared`), which should be preferred as they:

- Improve exception safety by ensuring memory is allocated and assigned in one step.
- Simplify code by eliminating explicit calls to `new` and `delete`.

To use smart pointers, include the `<memory>` header from the C++ Standard Library.

### Unique Pointer

A unique pointer is a scoped smart pointer, meaning it automatically deallocates the allocated memory when it goes out of scope.
This eliminates the need for manual `delete` calls and helps prevent memory leaks.

```cpp title="main.cpp"
#include <iostream>
#include <memory>

class Entity {
public:
	Entity() {
        std::cout << "Entity Created!" << std::endl;
    }

	~Entity() {
        std::cout << "Entity Destroyed!" << std::endl;
    }
};

int main() {
    /*
        Not recommended: manual allocation with new is valid but incorrect
        std::unique_ptr<Entity> entity(new Entity());
    */

	// Preferred approach: using std::make_unique
	std::unique_ptr<Entity> entity = std::make_unique<Entity>();

    std::cout << "End of main scope" << std::endl;
}
```

``` title="output"
Entity Created!
End of main scope
Entity Destroyed!
```

Here, the `Entity` class logs its creation and destruction, allowing us to observe when the unique pointer automatically deallocates the object at the end of `main`.

A `std::unique_ptr` cannot be copied, ensuring exclusive ownership of the resource.
Attempting to copy it will result in a compilation error.
However, ownership can be transferred using `std::move()`.

If multiple parts of a program need access to the same resource, consider using `std::shared_ptr` instead.

### Shared Pointer

A `std::shared_ptr` works differently from a `std::unique_ptr` and has more complexity under the hood.
It relies on reference counting, which keeps track of how many `shared_ptr` instances are pointing to the same resource.
When the reference count reaches zero, meaning no more `shared_ptr` instances are using the resource, the allocated memory is automatically freed.

Instances of `std::shared_ptr` are also destroyed at the end of their scope, but only the pointer itself, not the actual object it manages, unless the reference count reaches zero.
If there are other `shared_ptr` instances still referencing the same object, the object will remain alive until the last `shared_ptr` is destroyed or reset.
Therefore, they are also called strong references because they prevent the object from being destroyed.

Additionally, you should not use `new` to create a `shared_ptr`.
Instead, it's recommended to use `std::make_shared()`.
This is because `std::make_shared()` performs a single allocation for both the object and the control block (which stores the reference count), leading to better performance and exception safety compared to manually calling `new`.

```cpp title="main.cpp"
#include <iostream>
#include <memory>

class Entity {
public:
    Entity() {
        std::cout << "Entity Created!" << std::endl;
    }

    ~Entity() {
        std::cout << "Entity Destroyed!" << std::endl;
    }
};

int main() {
    std::shared_ptr<Entity> e;

    {
        std::shared_ptr<Entity> shared_entity = std::make_shared<Entity>();

        e = shared_entity;
        
        std::cout << "End of isoleted scope" << std::endl;
    }

    /* 
        The inner scope ends here, and `shared_entity` goes out of scope
        and is destroyed, but the object is not deleted because `e`
        still holds a reference to it.

        The object will only be deleted when `e` goes out of scope and 
        the reference count reaches zero, i.e., when the last `std::shared_ptr`
        is destroyed.

        The `Entity Destroyed!` message will be printed
        when `e` goes out of `main()` scope.
    */
    
    std::cout << "End of main scope" << std::endl;
}
```

``` title="output"
Entity Created!
End of isoleted scope
End of main scope
Entity Destroyed!
```

In this example, there are two instances of `std::shared_ptr<Entity>`, one in the main scope (`e`) and one inside the inner scope (`sharedEntity`).

Even though the inner scope ends and `sharedEntity` is destroyed, the object is not deleted because `e` still holds a reference to it.
The object will only be destroyed when the last shared pointer managing it is destroyed or reset, which in this case happens when `e` goes out of scope at the end of `main()`.

### Weak Pointer

Weak pointers are used in combination with shared pointers.
They also allow sharing access to a resource, but they do not keep the resource alive because they do not increase the reference count.

This can be useful when you need to observe or manipulate a shared resource (e.g., sorting a list of objects) without taking ownership of it.

```cpp title="main.cpp"
#include <iostream>
#include <memory>

class Entity {
public:
    Entity() {
        std::cout << "Entity Created!" << std::endl;
    }
	
    ~Entity() {
        std::cout << "Entity Destroyed!" << std::endl;
    }
};

int main() {
    std::weak_ptr<Entity> e;
    
    {
        std::shared_ptr<Entity> shared_entity = std::make_shared<Entity>();

        e = shared_entity;

        std::cout << "End of isoleted scope" << std::endl;
    }
	
    /*
        The inner scope ends here, and `sharedEntity` goes out of scope,
        is destroyed and the object is deleted immediately because `e`
        is a weak pointer and does not increase the reference count.

        Since no other `shared_ptr` exists after the inner scope ends,
        the resource is freed immediately, and the `Entity Destroyed!`
        message will be printed at the end of the isolated scope.
    */

    std::cout << "End of main scope" << std::endl;
}
```

``` title="output"
Entity Created!
End of isoleted scope
Entity Destroyed!
End of main scope
```

In this example, the Entity instance will be destroyed at the end of the inner scope, not at the end of `main()`.
This happens because we assigned it to a weak pointer, which does not increase the reference count.

Since no other `shared_ptr` exists after the inner scope ends, the resource is freed immediately.

### When to use Smart Pointers

Smart pointers should be preferred over raw pointers as they provide better memory safety and help prevent memory leaks.
Use `std::unique_ptr` when a heap allocation is necessary.
`std::shared_ptr` should only be used when multiple owners are required, as it comes with additional overhead due to reference counting and internal management.

However, there may be low-level cases where smart pointers are not sufficient, but these are uncommon in most high-level applications.

## Questions

=== "question 1"

    What is a pointer, and how does it conceptually differ from the value it points to?

=== "answer"

    A pointer is a variable that stores a memory address.
    Instead of holding a value directly, it points to the location in memory where that value is stored.

---

=== "question 2"

    Why do pointers in C++ have an associated data type if they ultimately store only a memory address?

=== "answer"

    Pointers have an associated data type so the compiler knows how many bytes to read or write when the pointer is dereferenced.
    The type does not change the address itself, only how the memory at that address is interpreted.

---

=== "question 3"

    What is the difference between stack memory and heap memory in terms of allocation, lifetime, and management?

=== "answer"

    Stack memory is automatically allocated and freed based on scope and has a limited, fixed size.
    Heap memory is manually allocated and deallocated by the programmer and persists until it is explicitly freed.

---

=== "question 4"

    What is a dangling pointer, and name two common ways dangling pointers can occur.

=== "answer"

    A dangling pointer is a pointer that refers to memory that is no longer valid.
    Common causes include accessing memory after it has been deleted and returning pointers to local stack variables that go out of scope.

---

=== "question 5"
    
    Why is returning a pointer to a local stack variable from a function considered undefined behavior?

=== "answer"

    Local stack variables are destroyed when a function exits.
    Returning a pointer to such a variable leaves the pointer referencing memory that no longer exists, resulting in undefined behavior.

---

=== "question 6"
    
    Explain the difference between `const int*`, `int* const`, and `const int* const`.

=== "answer"

    `const int*`       -> is a pointer to a constant value, meaning the value cannot be modified through the pointer.  
    `int* const`       -> is a constant pointer, meaning the pointer cannot be reassigned.  
    `const int* const` -> is a constant pointer to a constant value, meaning neither the pointer nor the value can be modified.

---

=== "question 7"
    
    How does pointer arithmetic work, and why does incrementing a pointer move by more than one byte?

=== "answer"

    Pointer arithmetic moves the pointer by multiples of the size of the type it points to.
    Incrementing a pointer advances it to the next element in memory, not the next byte.

---

=== "question 8"

    What are the key semantic differences between pointers and references in C++?

=== "answer"

    A pointer can be null, reassigned, and explicitly dereferenced.
    A reference must always be bound to a valid object, cannot be null, and cannot be reassigned after initialization.

---

=== "question 9"
    
    Explain the difference between pass-by-value, pass-by-reference, and pass-by-const-reference, and when each should be used.

=== "answer"

    Pass-by-value copies the argument and does not affect the original.  
    Pass-by-reference allows the function to modify the original argument.  
    Pass-by-const-reference avoids copying while preventing modification, making it ideal for large or read-only objects.

---

=== "question 10"
    
    What problem do move semantics solve, and how do rvalue references and `std::move` enable this behavior?

=== "answer"

    Move semantics avoid unnecessary copying by transferring ownership of resources instead of duplicating them.
    Rvalue references identify temporary objects, and `std::move` enables the compiler to invoke move operations rather than copy operations.

## Exercises

=== "exercise 1"

    Write a program that declares an `int` variable, creates a pointer that points to it.  
    Print the variable’s value and memory address by dereferencing and using the pointer.

=== "answer"

    ```cpp
    #include <iostream>

    int main() {
        int value = 10;
        int* value_ptr = &value;

        std::cout << "Value: " << *value_ptr << '\n';
        std::cout << "Pointer: " << value_ptr;
    }
    ```

---

=== "exercise 2"

    Write a program that dynamically allocates an `int` on the heap and assigns it a value.  
    Print the value and memory address of the variable, and then correctly frees the memory.

=== "answer"

    ```cpp
    #include <iostream>

    int main() {
        int* dynamic_value = new int(5);

        std::cout << "Value: " << *dynamic_value << '\n';
        std::cout << "Pointer: " << dynamic_value;

        delete dynamic_value;
    }
    ```

---

=== "exercise 3"

    Write a function that takes an int by reference and doubles its value.  
    Call the function from main and show that the original variable is modified.

=== "answer"

    ```cpp
    #include <iostream>

    void DoubleValue(int& value) {
        value *= 2;
    }

    int main() {
        int value = 10;

        DoubleValue(value);

        std::cout << "Modified value: " << value;
    }
    ```

---

=== "exercise 4"

    Write a program that intentionally creates a dangling pointer.

=== "hint"

    There is more than one way to do this, such as returning the address of a local variable or deleting dynamically allocated memory and keeping the pointer.

=== "answer"

    ```cpp
    #include <iostream>

    int* CreateDanglingPointer() {
        int value;
        return &value;
    }

    int main() {
        int* dangling_pointer = CreateDanglingPointer();
    }
    ```

---

=== "exercise 5"

    Write a function that swaps two integers using pointers instead of references.

=== "answer"

    ```cpp
    #include <iostream>

    void SwapValues(int* value1, int* value2) {
        int temp = *value1;
        *value1 = *value2;
        *value2 = temp;
    }

    int main() {
        int a = 10;
        int b = 20;
        
        SwapValues(&a, &b);
        
        std::cout << "a: " << a << '\n';
        std::cout << "b: " << b;
    }
    ```

---

=== "exercise 6"

    Create a function pointer that points to a function performing a simple arithmetic operation.
    Call the function through the function pointer.

=== "answer"

    ```cpp
    #include <iostream>
    #include <functional>

    int Addition(int augend, int addend) {
        return augend + addend; 
    }

    int main() {
        std::function<int(int, int)> function_ptr = &Addition;
        
        int sum = function_ptr(10, 20);
        
        std::cout << "Sum: " << sum;
    }
    ```

---

=== "exercise 7"

    Create a unique smart pointer for `int` and assign it a value.  
    Then write a function that takes this smart pointer as a parameter and prints the value.
    Move the pointer into the function, and observe what happens to the original pointer after the move.

=== "answer"

    ```cpp
    #include <iostream>
    #include <memory>

    void TakeOwnershipAndPrint(std::unique_ptr<int> ptr) {
        std::cout << "Pointer value: " << *ptr << '\n';
    }

    int main() {
        std::unique_ptr<int> smart_ptr = std::make_unique<int>(10);
        
        TakeOwnershipAndPrint(std::move(smart_ptr));
        
        if (smart_ptr == nullptr) {
            std::cout << "Moved Successfully!";
        }
    }
    ```