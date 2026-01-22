# 11 Templates and Metaprogramming

!!! info

    From this point forward, we are diving into the advanced features of the language.
    While these concepts form the backbone of modern C++ library design, they are not strictly mandatory for building functional software in the same way our previous chapters were.

    To truly level up as a developer, you should balance this theory with hands-on projects.
    Exploring professional frameworks like Qt, engines like UE5, graphics APIs like OpenGL or Direct3D11, or embedded systems like Arduino will often teach you more about real-world software development than language syntax alone.

As we learned in the previous chapter, macros in modern C++ should be avoided in favor of safer, language-native alternatives.
One of these alternatives is Templates.

Templates allow us to write generic code that works with any data type without duplicating logic.
Instead of overloading functions or rewriting classes for every possible type, we define a template—a single blueprint—and let the compiler handle the rest.

## How Templates Work

The compiler uses these blueprints to generate the actual machine code only when it is needed.
This process is called instantiation.
If a template is never used, it never appears in the final binary, keeping our executable lean.

Since templates are a native C++ concept, they follow the rules of the language.
They participate in type deduction, overload resolution, and respect access modifiers.
This makes them far more predictable and safer than the copy-paste behavior of macros.

In fact, templates form the foundation of the Standard Template Library; all containers rely on templates to store various data types.

One of the most important rules is: Templates should exist entirely in header files.

Unlike regular functions, the compiler needs to see the entire definition of a template to instantiate it for a specific type.

## The Tradeoff

While templates are incredibly powerful, they should be used with care.
It is rarely healthy to "templatize" every part of your codebase just because you can.
Templates have a reputation for producing notoriously long and confusing error messages that can be difficult to debug.
Like any complex feature, overusing them can lead to code that is difficult to read or maintain.
Balance is key.

## Function Templates

Let’s start with the most basic implementation of a template.
One of the clearest ways to see why templates are so vital is by examining a simple function that outputs a value to the console.

```cpp linenums="0"
void Print(int value) {
    std::cout << value << '\n';
}

Print(42);
Print("Hello World!"); // Error: cannot implicitly convert const char* to int
```

This function handles integers perfectly, but if we pass a string, the compiler throws an error due to a type mismatch.
Previously, we might have solved this by overloading the function for every type we wanted to support.
However, this becomes redundant quickly.
All these functions perform the exact same logic; they just use different types.

Instead of writing multiple versions of the same function, we can define one generic version through a template.

```cpp linenums="0"
// 'typename' and 'class' are interchangeable here; both define a type.
// 'T' is a placeholder for a type; we can name it whatever we like.
template<typename T> 
void Print(const T& value) {
    std::cout << value << '\n';
}

Print(42);                 // T is int
Print(3.14);               // T is double
Print("Hello world!");     // T is const char*
Print(std::string("C++")); // T becomes std::string
```

!!! info

    In the template definition, you'll see `typename T`.  
    You might also see developers use `class T` (e.g., `template<class T>`).  
    While they are functionally interchangeable in this context, typename is generally preferred.  
    Using `class` can be misleading, as it suggests the template only works with class types, when it actually works with primitives as well.

When we compile this, the compiler performs a process called instantiation.
It generates a specific version of the function for every type you actually use in your code.
In the final binary, it looks as if you had written:

```cpp linenums="0"
void Print(const int& value) {
    std::cout << value << '\n';
}

void Print(const double& value) {
    std::cout << value << '\n';
}

void Print(const char* const& value) {
    std::cout << value << '\n';
}

void Print(const std::string& value) {
    std::cout << value << '\n';
}
```

### Explicit Template Arguments

In most cases, the compiler can automatically deduce the template types based on the arguments we provide.
This is called Template Argument Deduction.
However, we can manually specify the type using angle brackets.

```cpp linenums="0"
template<typename T>
T GetValue(const T& value) {
    return value;
}

// Manually telling the compiler T is an int
auto value = GetValue<int>(42);
```

Explicitly stating the type doesn't change the performance of the code, but it is useful for resolving ambiguity.

### Multiple Template Parameters

Template definitions aren't limited to a single type—we can define as many as needed by separating them with commas.
This is helpful when we want a function to handle different types for different parameters.

```cpp linenums="0"
template <typename T, typename U>
void PrintPair(const T& first, const U& second) {
    std::cout << "First: " << first << ", Second: " << second << '\n';
}

// T becomes int, U becomes const char*
PrintPair(42, "Age");
```

### Non Type Template Parameters

In earlier chapters, we saw that passing a `std::array` into a function can be tricky because the size is part of the type.
For example, a function expecting a `std::array<int, 5>` cannot accept a `std::array<int, 10>`.

Templates solve this beautifully.
We can use a non-type template parameter to capture the size of the array at compile time, making the function completely flexible.

```cpp linenums="0"
// 'T' is the type, 'K' is a non-type template parameter (the size)
// 'K' represents a compile-time constant value
template<typename T, size_t K>
void PrintArray(const std::array<T, K>& arr) {
    for (const auto& element : arr) {
        std::cout << element << ' ';
    }
    std::cout << '\n';
}

std::array<int, 5> int_arr;
std::array<std::string, 3> str_arr;

// T = int, K = 5
PrintArray(int_arr); 

// T = std::string, K = 3
PrintArray(str_arr);
```

### Abbrevieted Function Templates

In C++20, we can write templates without using the `template<typename T>` syntax.
By using `auto` in a function’s parameter list, the compiler automatically treats that function as a template.

```cpp linenums="0"
// This is still a function template
void PrintColl(const auto& coll) {
    for (const auto& elem : coll) {
        std::cout << elem << ' ';
    }
    std::cout << '\n';
}
```

While this is shorter and easier to read, there is a catch: we don't have a name for the type.
In a traditional template, we can use `T` to create a local variable or a return type.
With abbreviated templates, if you suddenly need to know the name of the type (for example, to create a temporary variable of that same type), it becomes a bit tricky.
You have to use `decltype` to extract the type.

```cpp linenums="0"
void SwapFirstAndSecond(auto& coll) {
    // We don't have 'T', so we use decltype to find the type of the elements
    using T = decltype(coll[0]); 
    T temp = coll[0];
    coll[0] = coll[1];
    coll[1] = temp;
}
```

## Concepts

To understand what concepts are and the problems they solve, we must first look at a common challenge we faced in the past.
Templates are incredibly flexible, but they are also blind to the capabilities of the types we pass into them until the moment of instantiation.

Consider a scenario where we want to create a generic `Add` function that works with different types of collections.

```cpp linenums="0"
template<typename CollT, typename T>
void Add(CollT& coll, const T& val) {
    coll.push_back(val);
}

std::vector<int> coll;

Add(coll, 10);
```

This works perfectly for `std::vector`, but a complication arises when we try to use a collection that does not support `.push_back()`, such as for example a `std::set`.
In the past, we could not simply overload this template because the function signature remains the same; only the logic inside the body changes.
If we tried to define two versions of this template, we would face a redefinition error.

Historically, C++ did not have a direct way to express requirements for template parameters.
If we wanted to restrict a function to only work with certain types, we had to rely on a complex paradigm known as SFINAE (Substitution Failure Is Not An Error).

This usually involved using `std::enable_if`, a tool that intentionally breaks a template's substitution to force the compiler to look for a different overload.
While effective, this approach often felt like we were abusing the template system.
It resulted in verbose, "hacky" code and produced notoriously long, cryptic error messages that were difficult to debug.

In Modern C++ (C++20 and beyond), we have moved away from these workarounds in favor of a formal, first-class solution: Concepts.

### Defining and Using Concepts

Concepts allow us to define requirements that a type must meet at compile time.
Instead of hoping a type has a specific method, we can explicitly check for it.

```cpp linenums="0"
// We define a concept to check if a type has a push_back method
template<typename CollT>
concept HasPushBack = requires(CollT c, CollT::value_type v) {
    c.push_back(v);
};

// This version is only used if the collection matches our concept
template<typename CollT, typename T>
requires HasPushBack<CollT>
void Add(CollT& coll, const T& val) {
    coll.push_back(val);
}

// This version acts as a fallback for other collections like std::set
template<typename CollT, typename T>
void Add(CollT& coll, const T& val) {
    coll.insert(val);
}

std::vector<int> v;
std::set<int> s;

Add(v, 10); // Uses the push_back version
Add(s, 99); // Uses the insert version
```

By studying this approach, we can see how we now have the power to set clear requirements for our templates.
In the example above, the compiler evaluates the constraints: if the type satisfies `HasPushBack`, it uses the first implementation.
If not, it falls back to the second.
This results in much cleaner code and, importantly, much clearer error messages if we pass a type that meets neither requirement.

For better readability, we can use a more concise syntax.
Instead of using a separate requires clause, we can replace the `typename` keyword directly with our concept name.
Both of these approaches are functionally identical.

```cpp linenums="0"
// Version 1
template<typename CollT, typename T>
requires HasPushBack<CollT>

// Version 2
template<HasPushBack CollT, typename T>
```

### Concept Validation

While Concepts are a powerful addition to the language, they are not immune to human error.
Because Concepts are essentially compile-time predicates (logical true/false checks), a simple typo can lead to unexpected behavior that is often difficult to track down.

The most common issue we encounter is a misspelling within the requires block.
Consider the following mistake.

```cpp linenums="0"
template<typename CollT>
concept HasPushBack = requires(CollT c, CollT::value_type v) {
    c.pushback(v); // We accidentally omitted the underscore
};

std::vector<int> v;

Add(v, 10); // ERROR: can't call insert()
```

In this scenario, the compiler checks `std::vector` against our `HasPushBack` concept.
Because `std::vector` does not have a method named `pushback` (missing the underscore), the concept evaluates to false.

Rather than giving us a compiler error about the typo, the compiler assumes the requirement was simply not met and moves on to the next available `Add` overload—the one that uses `.insert()`.
This results in a confusing error message claiming that `std::vector` has no member named `insert`.

Because concepts evaluate to boolean values at compile time, we can—and should—verify them using `static_assert`.
This allows us to catch typos and logic errors immediately without needing to instantiate complex function templates first.

```cpp linenums="0"
template<typename CollT>
concept HasPushBack = requires(CollT c, CollT::value_type v) {
    c.push_back(v);
};

static_assert(HasPushBack<std::vector<int>>, "Vector should be supported");
static_assert(!HasPushBack<std::set<int>>, "Set should not be supported");
```

### Concepts in Abbreviated Templates

In modern C++, we often use `auto` in function parameters to create Abbreviated Function Templates.
This is a cleaner shorthand for writing `template<typename T>`.
However, we encounter the same redefinition problem if we try to overload them without constraints.

```cpp linenums="0"
// This will cause a redefinition error because both signatures look identical
void Add(auto& coll, const auto& val) {
    coll.push_back(val);
}

void Add(auto& coll, const auto& val) {
    coll.insert(val);
}
```

We can solve this by qualifying the `auto` keyword with a concept.
This tells the compiler: "This function only accepts an object that satisfies `HasPushBack`.

```cpp linenums="0"
void Add(HasPushBack auto& coll, const auto& val) {
    coll.push_back(val);
}

void Add(auto& coll, const auto& val) {
    coll.insert(val);
}
```

Sometimes, we may prefer using a `requires` clause with `decltype(collection)` for better control.
However, we must be careful.
If our function takes a reference like in this case `auto&`, `decltype(collection)` will return a reference type such as `std::vector<int>&`.

Concepts often fail when we pass them a reference because a reference itself doesn't have member functions or nested types like `value_type`; only the underlying type does.
To fix this, we must strip away the extras like references and const qualifiers.

We can do this using `std::remove_cvref_t` (introduced in C++20).
This is preferred over the older `std::decay`, as it can accidentally turn arrays into pointers, which might not be our intention.

We have two ways to apply this fix.
We can apply it at the call site.

```cpp linenums="0"
void Add(auto& coll, const auto& val) 
requires HasPushBack<std::remove_cvref_t<decltype(coll)>> {
    coll.push_back(val);
}
```

However, to keep our code clean, it is often better to handle this cleaning inside the concept definition itself.
This ensures that anyone using the concept doesn't have to worry about whether they are passing a reference or a value.

```cpp linenums="0"
template<typename CollT>
concept HasPushBack = requires(std::remove_cvref_t<CollT> c,
                               std::remove_cvref_t<CollT>::value_type v) {
    c.push_back(v);
};

void Add(auto& collection, const auto& value) 
requires HasPushBack<decltype(collection)> {
    collection.push_back(value);
}
```

### Concepts for Multiple Prameters

We are not limited to validating a single type at a time.
Often, the logic of our code depends on how two different types interact.
In these cases, we can define concepts that accept multiple parameters.

For example, it isn't enough to know that a collection has a `push_back()` method; we must also ensure that the specific value we are passing is compatible with that collection.

```cpp linenums="0"
template<typename CollT, typename T>
concept CanPushBack = requires (CollT c, T v) {
    c.push_back(v);
};

template<typename CollT, typename T>
requires CanPushBack<CollT, T>
void Add(CollT& coll, const T& val) {
    coll.push_back(val);
}

std::vector<int> numbers;
Add(numbers, 10);   // OK: Can push int into vector<int>

Add(numbers, "Hi"); // ERROR: vector<int> cannot push_back a const char*
```

We can also apply this to abbreviated function templates using `auto`.
However, as we noticed in previous examples, this requires us to be very explicit with decltype and type-cleaning traits to ensure we are passing the correct types to the concept.

```cpp linenums="0"
void Add(auto& coll, const auto& val)
requires CanPushBack<std::remove_cvref_t<decltype(coll)>, 
                     std::remove_cvref_t<decltype(val)>> {
    coll.push_back(val);
}
```

As we begin to use concepts more frequently, we must be careful not to fall into the template rabbit hole.
It is possible to create concepts that are too granular or specific, which can add unnecessary overhead to our mental model of the code.

If a concept is only ever used for one specific function and one specific pair of types, we might ask ourselves if a standard function overload would be simpler and more readable.
The true power of concepts lies in their ability to define generic requirements that apply to broad categories of types.

### Requires and Compile Time If

Sometimes we don't need a full-blown named concept.
We just want to check if a specific line of code would work before we try to compile it.
This is where the requires expression and if constexpr work together to create branching logic at compile time.

In the example below, we create a function that adds an item to a collection.
It doesn't care if the collection uses `.push_back()` or `.insert()`, it checks for the right method automatically.

```cpp linenums="0"
void Add(auto& coll, const auto& val) {
    if consexpr (requires { coll.push_back(val); }) {
        coll.push_back(val);
    } else {
        coll.insert(val);
    }
}

std::vector<int> v;
std::set<int> s;

Add(v, 10); // Compiler sees push_back works, uses the first branch
Add(s, 20); // Compiler sees push_back fails, uses the second branch
```

### Constrain Ambiguities

Concepts are powerful, but they can lead to ambiguity errors if two different functions match the same type equally well.

In the example below, a `std::vector` supports both `.size()` and the index operator.
Because it satisfies both requirements, the compiler doesn't know which version of foo to choose.

```cpp linenums="0"
template<typename CollT>
concept HasSize = requires (CollT c) {
    { c.size() } -> std::converitble_to<int>;
};

template<typename CollT>
concept HasIndexOp = requires (CollT c) {
    c[0];
};

template<typename CollT>
requires HasSize<CollT>               // Has to support size()
void foo(const CollT& coll) {
    std::cout << coll.size() << '\n';
}

template<typename CollT>
requires HasIndexOp<CollT>            // Has to support []
void foo(const CollT& coll) {
    std::cout << "[]\n";
}

foo(std::list<int>{0, 8, 15});   // OK: calls first foo()
foo(std::vector<int>{0, 8, 15}); // ERROR: ambiguous
```

To fix this, we need to tell the compiler that one version is more specialized than the other.
C++ uses a rule called subsumption: if one function's requirements include all the requirements of another plus something extra, the compiler will pick the more specific one.

We can fix the ambiguity by combining the concepts in the requires clause.

```cpp linenums="0"
// This version is now "more constrained" than Version A
template<typename CollT>
requires HasSize<CollT> && HasIndexOp<CollT> 
void foo(const CollT& coll) {
    std::cout << "[] (More specific)\n";
}

foo(std::vector<int>{1, 2}); // OK: Picks the most constrained version.
}
```

### Constrains for Non Type Template Parameters

Concepts are most commonly used to check types, but they can also be used to validate Non-Type Template Parameters like integers.
This allows you to catch logic errors at compile time before the program even runs.

Suppose we want a class or function that only accepts a prime number as a template argument.
First, we define a constexpr function to do the math, then we wrap it in a concept.

```cpp linenums="0"
// A constexpr function that can be evaluated at compile-time
constexpr bool IsPrime(int val) {
    if (val <= 1) {
        return false;
    }

    for (int i = 2; i * i <= val; ++i) {
        if (val % i == 0) {
            return false;
        }
    }
     
    return true; 
}

// A concept that uses that function
template<int N>
concept Prime = IsPrime(N);

// Applying the concept to a Non-Type Template Parameter
template<int N>
requires Prime<N>
void ProcessPrime() {
    std::cout << N << " is a valid prime number!\n";
}

ProcessPrime<7>();  // OK: 7 is prime
ProcessPrime<10>(); // ERROR: 10 is not prime (fails at compile time)
```

## Template Specialization

Sometimes, the one-size-fits-all approach of a general template isn't enough.
We might need a specific data type to behave differently—perhaps to optimize performance, handle a unique edge case, or format the output differently.

Template specialization allows us to override the general template with a custom implementation for one specific type.
To do this, we use the `template<>` syntax.
This tells the compiler: "I am still using a template, but I am providing a specific version where the types are already filled in."

```cpp linenums="0"
// The Primary (General) Template
template<typename T>
void Print(const T& value) {
    std::cout << "General: " << value << '\n';
}

// Full Specialization for std::string
template<>
void Print<std::string>(const std::string& value) {
    std::cout << "Specialized String: '" << value << "'\n";
}

Print(100);                        // Uses General
Print(std::string("Specialized")); // Uses Specialization
```

!!! note

    A `const char*` (string literal) is NOT a `std::string`.
    Therefore it will use the General template unless we specialize for `const char*` too.

## Class templates 

Class templates apply the same "blueprint" logic to entire classes.
As mentioned earlier, the Standard Template Library (STL) is built on this; when you use `std::vector<int>`, you are using a class template.

A class template allows you to define the member variables and methods of a class using placeholder types.

```cpp title="main.cpp"
#include <iostream>

template<typename T, size_t K>
class Array {
public:
    // We can use K for loop boundaries and T for parameters
    void Populate(T value) {
        for (size_t i = 0; i < K; i++) {
            m_array[i] = value;
        }
    }

    void Print() const {
        for (size_t i = 0; i < K; i++) {
            std::cout << m_array[i] << " ";
        }
        std::cout << '\n';
    }

    void SetElement(size_t index, T value) {
        if (index < K) {
            m_array[index] = value;
        }
    }

private:
    T m_array[K]; // The underlying array uses our template parameters
};

int main()
{
    // We specify the type and the size in angle brackets
    Array<int, 5> numbers;
    numbers.Populate(1);
    numbers.SetElement(2, 50);
    numbers.Print();

    Array<std::string, 2> words;
    words.SetElement(0, "Hello");
    words.SetElement(1, "Templates");
    words.Print();
}
```

``` title="output"
1 1 50 1 1 
Hello Templates 
```

### Class Template Argument Deduction

Since templates were introduced, the compiler has become even smarter.
In many cases, you no longer need to explicitly list the template types if the compiler can figure them out from the constructor arguments.
This is called CTAD.

```cpp title="main.cpp"
#include <iostream>
#include <typeinfo>

template<typename T, typename U>
struct Pair {
    T first;
    U second;

    Pair(T f, U s) : first(f), second(s) {}
};

int main() {
    Pair p(10, 5.5); 

    std::cout << "First type: " << typeid(p.first).name() << '\n';
    std::cout << "Second type: " << typeid(p.second).name();
}
```

``` title="output"
First type: i
Second type: d
```

## Variadic Templates

While standard templates allow us to be generic with types, Variadic Templates allow us to be generic with the number of arguments.
Sometimes, we need a function that can accept an unknown number of arguments, each potentially being a different type.

The syntax utilizes the ellipsis operator `(...)`, which defines what is known as a Parameter Pack.

```cpp linenums="0"
// Base case: To stop the recursion when no arguments are left
void Print() {}

template<typename T, typename... Types>
void Print(const T& first_arg, const Types&... args) {
    std::cout << first_arg << '\n';
    Print(args...); // Recursive call
}

std::string str = "string";

Print("Hello", 43.71, str);
```

A variadic template typically works through a functional pattern of recursion.
It peels off the first argument, processes it, and then passes the remaining pack back into itself.

When you call Print("Hello", 43.71, str), the compiler instantiates a chain of functions that looks like this:

```cpp linenums="0"
// 1. first_arg is "Hello", args is {43.71, str}
Print(const char* first_arg, double arg1, std::string arg2) {
    std::cout << "Hello" << '\n';
    Print(43.71, str); 
}

// 2. first_arg is 43.71, args is {str}
Print(double first_arg, std::string arg1) {
    std::cout << 43.71 << '\n';
    Print(str);
}

// 3. first_arg is str, args is empty
Print(std::string first_arg) {
    std::cout << "string" << '\n';
    Print(); // Calls the base case
}

// 4. Base case
void Print() {}
```

We can query the number of elements inside a parameter pack using the `sizeof...` operator.
This is useful for debugging or logic branching within the template.

In modern C++ (C++17 and later), we can often avoid writing a separate base case function by using `if constexpr`.
This allows the compiler to conditionally discard the recursive call if the pack is empty, preventing a compilation error when the arguments run out.

```cpp linenums="0"
template<typename T, typename... Types>
void Print(const T& first_arg, const Types&... args) {
    std::cout << first_arg << '\n';

    if constexpr (sizeof...(args) > 0) { // Only compiled if there are more args
        Print(args...);
    }
}
```

!!! info

    Variadic templates are a cornerstone of the Standard Template Library.
    While the syntax may seem specialized at first, these templates are incredibly versatile.
    Even if you don't find yourself writing them daily, understanding how they work is essential for navigating modern C++ and understanding how many standard library features operate under the hood.

## Alias Templates

## Variable Templates



## Type Traits

## Questions

## Exercises

=== "exercise 1"

Create a template

=== "answer"

    ```cpp title="main.cpp"
    template<typename CollT>
    void Print(const CollT& coll) {
        for (const auto& val : coll) {
            std::cout << val << ' ';
        }
        std::cout << '\n';
    }

    int 
    ```

---