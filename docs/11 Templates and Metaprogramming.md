# 11 Templates and Metaprogramming

As we learned in the previous chapter, macros in modern C++ should generally be avoided in favor of safer alternatives.
One of the most powerful of these alternatives is templates.

Templates allow us to write code that works with any data type without duplicating logic for each one.
Instead of overloading functions or rewriting classes for every possible data type, we define a template—a single "blueprint" that handles them all.

## How Templates Work

The compiler uses these blueprints to generate the actual code only when it is needed.
This process is called instantiation.
If a template is never used, it never appears in the final binary, keeping your executable lean.

Since templates are a native C++ concept (unlike C-style macros), they follow the rules of the language.
They participate in overload resolution, type deduction, and access control.
This makes them far more predictable and safer than the "copy-paste" behavior of macros.
In fact, templates form the foundation of the Standard Template Library (STL); containers like `std::vector`, `std::map`, and `std::array` all rely on templates to store any data type you throw at them.

## The Tradeoff

While templates are incredibly powerful, they should be used with care.
It is rarely healthy to "templatize" every part of your codebase just because you can.
Templates have a reputation for producing notoriously long and confusing error messages that can be difficult to debug.
Like any complex feature, overusing them can lead to code that is difficult to read or maintain.
Balance is key.

## About Metaprogramming

Templates are also our gateway to a broader technique known as metaprogramming.
This refers to writing code that generates or adapts other code to solve a problem, often at compile time.

If you are coming from languages like Java or C#, templates may look like Generics at first glance.
However, C++ templates go much further.
While generics focus primarily on type abstraction, C++ templates support complex compile-time logic, type traits, and "specialization" (creating specific behavior for certain types).
This extra power makes templates incredibly expressive, but also—at times—challenging to master.

## Function Templates

Let’s start with the most basic implementation of a template.
One of the clearest ways to see the "why" behind templates is with a simple function that prints a value to the terminal.

```cpp title="main.cpp"
#include <iostream>

void Print(int value)
{
    std::cout << value << '\n';
}

int main()
{
    Print(42);
    Print("Hello World!"); // Error: cannot implicitly convert const char* to int
}
```

This function handles integers perfectly, but if you try to pass a string, the compiler throws an error due to a type mismatch.
Up to this point, the common solution would be to overload the function for every type you want to support.
However, this becomes messy and redundant very quickly.
All these functions perform the exact same logic—they just use different types.

This is where function templates become useful.
Instead of writing multiple versions of the same function, we can define one generic version.

```cpp title="main.cpp"
#include <iostream>
#include <string>

// 'T' is a placeholder for a type; you can name it whatever you like.
// 'typename' and 'class' are interchangeable here; both define a type.
template<typename T> 
void Print(const T& value)
{
    std::cout << value << '\n';
}

int main()
{
    // The compiler deduces the type automatically (Type Deduction)
    Print(42);                  // T becomes int
    Print(3.14);                // T becomes double
    Print("Hello world!");      // T becomes const char*
    Print(std::string("C++"));  // T becomes std::string

    // We can also specify the type explicitly
    Print<int>(42);
    Print<std::string>(std::string("C++"));
}
```

In the code above, we define the template type using `typename`.
You might see some developers use `class` (e.g., `template<class T>`), and while they are functionally interchangeable in this context, typename is generally preferred.
Using `class` can be misleading, as it suggests the template only works with class types, when it actually works with primitives too.

### Explicit Template Arguments

While the compiler is usually smart enough to figure out the type on its own, we can manually specify it using angle brackets `< >`.
This doesn't change the performance of the code, but it can improve clarity or solve "ambiguity" issues.

Explicitly stating the type is especially helpful when using `auto` or when a function returns a template type, as it makes it clear exactly what type is being handled.

```cpp title="main.cpp"
template<typename T>
T GetValue(const T& value)
{
    return value;
}

int main()
{
    // Clarifies what type 'auto' captures
    auto value = GetValue<int>(42); 
}
```

Template definitions aren't limited to a single type—we can define as many as needed.
For example, we can create a function that accepts two different types.

```cpp title="main.cpp"
template <typename T, typename K>
void PrintPair(const T& first, const K& second)
{
    std::cout << "First: " << first << ", Second: " << second << 'n';
}

int main()
{
    // T is int, K is const char*
    PrintPair(42, "Age"); 
}
```

### Handling Std Array of Any Type and Size

In the Collections of Data chapter, we saw that passing an array into a function can be annoying because the size is part of the type.
If we want a function to print an array, we usually have to hardcode the size, making the function inflexible.

Templates solve this beautifully.
We can use a non-type template parameter to capture the size of the array at compile time.

```cpp title="main.cpp"
#include <iostream>
#include <array>

// 'T' is the type, 'K' is a non-type template parameter (the size)
template<typename T, size_t K>
void PrintArray(const std::array<T, K>& arr)
{
    for (const auto& element : arr)
        std::cout << element << '\t';
    
    std::cout << std::endl;
}

int main()
{
    std::array<int, 5> integerArray{1, 2, 3, 4, 5};
    std::array<std::string, 3> stringArray{"Hello", "Templates", "World!"};
    
    // T=int, K=5
    PrintArray(integerArray); 
    
    // T=std::string, K=3
    PrintArray(stringArray);  
}
```

A non-type template parameter (like K in the example above) represents a compile-time constant value rather than a type.
This allows the compiler to generate a specific version of the function for every unique size and type combination we use.

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

## Template Specialization

Sometimes, the "one size fits all" approach of a general template doesn't work.
We might need a specific type to behave differently—perhaps for performance reasons or to format output differently.

This is where Template Specialization comes in.
It allows us to define a specific implementation for a particular data type.

To specialize a template, we use the `template<>` syntax.
This tells the compiler: "I am still using a template, but I am providing a specific version where all the types are already filled in."

```cpp title="main.cpp"
#include <iostream>
#include <string>

// The Primary (General) Template
template<typename T>
void Print(const T& value) {
    std::cout << "General: " << value << '\n';
}

// Full Specialization for std::string
// We leave the brackets empty because there are no "generic" types left.
template<>
void Print<std::string>(const std::string& value) {
    std::cout << "Specialized String: '" << value << "'\n";
}

int main() {
    Print(100);                      // Uses General
    Print(std::string("C++ Rules")); // Uses Specialization
}
```

!!! note

    A `const char*` (string literal) is NOT a `std::string`.
    Therefore it will use the General template unless we specialize for `const char*` too.

## Concepts

To understand what concepts are and the problems they solve, we must first look at a common challenge we faced in the past.
Templates are incredibly flexible, but they are also blind to the capabilities of the types we pass into them until the moment of instantiation.

Consider a scenario where we want to create a generic `Add` function that works with different types of collections.

```cpp title="main.cpp"
#include <vector>

template<typename CollT, typename T>
void Add(CollT& coll, const T& val) {
    coll.push_back(val);
}

int main() {
    std::vector<int> coll;

    Add(coll, 10);
}
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

```cpp title="main.cpp"
#include <vector>
#include <set>

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

int main() {
    std::vector<int> v;
    std::set<int> s;

    Add(v, 10); // Uses the push_back version
    Add(s, 99); // Uses the insert version
}
```

By studying this approach, we can see how we now have the power to set clear requirements for our templates.
In the example above, the compiler evaluates the constraints: if the type satisfies `HasPushBack`, it uses the first implementation.
If not, it falls back to the second.
This results in much cleaner code and, importantly, much clearer error messages if we pass a type that meets neither requirement.

For better readability, we can use a more concise syntax.
Instead of using a separate requires clause, we can replace the `typename` keyword directly with our concept name.
Both of these approaches are functionally identical.

```cpp title="example"
template<typename CollT, typename T>
requires HasPushBack<CollT>

template<HasPushBack CollT, typename T>
```

### Concept Validation

While Concepts are a powerful addition to the language, they are not immune to human error.
Because Concepts are essentially compile-time predicates (logical true/false checks), a simple typo can lead to unexpected behavior that is often difficult to track down.

The most common issue we encounter is a misspelling within the requires block.
Consider the following mistake.

```cpp title="example"
// We accidentally omitted the underscore in push_back
template<typename CollT>
concept HasPushBack = requires(CollT c, CollT::value_type v) {
    c.pushback(v); 
};

/* ... using the same Add overloads from the previous example ... */

int main() {
    std::vector<int> v;
    
    // ERROR: The compiler tries to call the 'insert' version
    Add(v, 10); 
}
```

In this scenario, the compiler checks `std::vector` against our `HasPushBack` concept.
Because `std::vector` does not have a method named `pushback` (missing the underscore), the concept evaluates to false.

Rather than giving us a compiler error about the typo, the compiler assumes the requirement was simply not met and moves on to the next available `Add` overload—the one that uses `.insert()`.
This results in a confusing error message claiming that `std::vector` has no member named `insert`.

Because concepts evaluate to boolean values at compile time, we can—and should—verify them using `static_assert`.
This allows us to catch typos and logic errors immediately without needing to instantiate complex function templates first.

```cpp title="example"
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

```cpp title="example"
/*
    This will cause a redefinition error because
    both signatures look identical
*/
void Add(auto& coll, const auto& val) {
    coll.push_back(val);
}

void Add(auto& coll, const auto& val) {
    coll.insert(val);
}
```

We can solve this by qualifying the `auto` keyword with a concept.
This tells the compiler: "This function only accepts an object that satisfies `HasPushBack`.

```cpp title="example"
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

```cpp title="example"
void Add(auto& coll, const auto& val) 
requires HasPushBack<std::remove_cvref_t<decltype(coll)>> {
    coll.push_back(val);
}
```

However, to keep our code clean, it is often better to handle this cleaning inside the concept definition itself.
This ensures that anyone using the concept doesn't have to worry about whether they are passing a reference or a value.

```cpp title="example"
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

```cpp title="example"
template<typename CollT, typename T>
concept CanPushBack = requires (CollT c, T v) {
    c.push_back(v);
};

template<typename CollT, typename T>
requires CanPushBack<CollT, T>
void Add(CollT& coll, const T& val) {
    coll.push_back(val);
}

int main() {
    std::vector<int> numbers;
    Add(numbers, 10);      // OK: Can push int into vector<int>
    
    // Add(numbers, "Hi"); // ERROR: Concept fails because vector<int> 
                           // cannot push_back a const char*
}
```

We can also apply this to abbreviated function templates using `auto`.
However, as we noticed in previous examples, this requires us to be very explicit with decltype and type-cleaning traits to ensure we are passing the correct types to the concept.

```cpp title="example"
void Add(auto& coll, const auto& val)
requires CanPushBack<std::remove_cvref_t<decltype(coll)>, 
                     std::remove_cvref_t<decltype(val)>> {
    coll.push_back(val);
}
```

As we begin to use concepts more frequently, we must be careful not to fall into the "template rabbit hole."
It is possible to create concepts that are too granular or specific, which can add unnecessary overhead to our mental model of the code.

If a concept is only ever used for one specific function and one specific pair of types, we might ask ourselves if a standard function overload would be simpler and more readable.
The true power of concepts lies in their ability to define generic requirements that apply to broad categories of types.

## Variadic Templates

## Alias Templates

## Variable Templates

## Type Traits

## Metaprogramming