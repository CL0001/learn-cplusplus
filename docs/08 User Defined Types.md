# 08 User Defined Types

In the previous chapter, we explored how to organize data using C-style arrays, strings and STL containers.
These are great when you’re working with a list of similar things—like numbers, coordinates, or game items.
But they only work when all the elements are of the same type.

In real-world programs, you often need to combine different kinds of values into one thing.
Think of a game character: you might need to track their name, score, and health—all of which are different types of data.
Handling each one separately quickly becomes awkward and error-prone.

That’s why programming languages like C++ let us create our own complex data types—custom structures that bundle different kinds of information together under a single name.
Instead of juggling multiple variables for one concept, you can wrap them into a single, unified object that better reflects what you’re actually working with.

In this chapter, we’ll explore how to build and use these custom structures to keep your programs clean, organized, and scalable.
We’ll start with the simplest and most practical form: grouping variables into one meaningful unit.

## Structs

A struct lets you group related data under a single name.
Its members can be of different types, and you access them using the dot operator.
Structs are great for organizing simple, related pieces of information—like integers, floating-point numbers, or strings—into a single unit.
Like arrays, the members of a struct are stored in contiguous memory.
If you remember why this matters, it’s the reason we can perform type punning with structs.

When a struct contains only simple data types without extra behavior, it’s often called a Plain Old Data (POD) type.

```cpp title="main.cpp"
#include <iostream>
#include <string>

struct Player {
    std::string username;
    unsigned int score;
    double health;
};

int main() {
    Player player;
    player.username = "Unknown";
    player.score = 45.0;

    std::cout << "Player username: " <<  player.username << std::endl;
}
```

``` title="output"
Player username: Unknown
```

In this example, the `struct` name `Player` defines a custom data type, allowing us to create variables of that type.

Notice that the `struct` like other structure we will be covering is defined outside the main function, which gives it global scope.
This isn’t strictly necessary—if you only need to use it within a single function or other scope, you can define it there.
However, in most cases, the entire translation unit needs access to it, so defining it globally is the usual approach.

Also, the `struct` definition must end with a semicolon, forgetting this will cause a compilation error.

Beyond just holding data, a struct can also include functions that can manipulate that data.
These are called member functions or methods, and they can operate on the struct’s members because they’re defined inside the structs scope.

```cpp title="main.cpp"
#include <iostream>
#include <string>

struct Player {
    std::string username;
    unsigned int score;
    double health;

    void ReduceHealth() {
        health -= 10;
    }
};

int main() {
    // Initialize all members with an initializer list
    Player player{"Random", 0, 100};

    player.ReduceHealth();

    std::cout << "Player health: " << player.health << std::endl;
}
```

``` title="output"
Player health: 90
```

!!! note

    Adding methods to structs is a feature unique to C++.
    This is important to note because structs originate from C, where attaching methods is not possible.

We can also initialize all member variables at once using curly braces with values — this is known as aggregate initialization.
However, the values must be provided in the same order as the member variables are declared in the `struct`, since they are assigned from top to bottom.

C++ again offers a clearer alternative called designated initializers, where you explicitly specify which value corresponds to each member by name.
This improves readability, especially when the `struct` definition is far from its usage or when dealing with many members.

```cpp title="main.cpp"
#include <iostream>
#include <string>

struct Player {
    std::string username;
    unsigned int score;
    double health;

    void ReduceHealth() {
        health -= 10;
    }
};

int main() {
    // Designated initializers
    Player player{
        .username = "Random",
        .score = 0,
        .health = 100
    };

    player.ReduceHealth();

    std::cout << "Player health: " << player.health << std::endl;
}
```

``` title="output"
Player health: 90
```

## Unions

A `union` is similar to a `struct` in that it groups related variables under a single name.
However, unlike a `struct`, a `union` shares the same memory space among all its members.
This means a `union` can only store one value at a time, and its total size is determined by the largest member it contains.

For example, if a union holds four `float` members named `a`, `b`, `c` and `d`, it will only occupy the size of one float—4 bytes—instead of the combined size of all four, which would total 16 bytes.
Since all members share the same memory location, changing one member will affect the others.

Unions are useful for type punning, which means interpreting the same chunk of memory in different ways.
They also enable aliasing, where the same data can be accessed under different names.
For instance, a `union` might let you treat a 3D vector (x, y, z) as an RGB color (r, g, b) without duplicating memory.

Also unions can be declared on their own or embedded anonymously inside other structures for convenience.
Unlike structs, unions do not support member functions, except for constructors and destructors.

```cpp title="main.cpp"
#include <iostream>

union Vector3 {
    struct {
        double x;
        double y;
        double z;
    };

    struct {
        double r;
        double g;
        double b;
    };
};

int main() {
    Vector3 vec3{14.3, 22.78, 237.4};

    // Accessed as coordinates
    std::cout << " x: " << vec3.x;
    std::cout << " y: " << vec3.y;
    std::cout << " z: " << vec3.z << std::endl;

    // Accessed as color components
    std::cout << " r: " << vec3.r;
    std::cout << " g: " << vec3.g;
    std::cout << " b: " << vec3.b << std::endl;
}
```

``` title="output"
 x: 14.3 y: 22.78 z: 237.4
 r: 14.3 g: 22.78 b: 237.4
```

In this example, we define a union called `Vector3` that allows accessing its data in two different ways:

- As coordinates (x, y, z) when used as a 3D vector.
- As color components (r, g, b) when used in a color palette.

Notice that these variables are grouped inside anonymous structs within the union.
Without the structs, the union would only allow one active variable at a time, so you couldn’t store all three values simultaneously.

Anonymous structs act like regular structs but inherit their member names directly into the union.
This lets us write `vec3.x` instead of something longer like `vec3.color.r`, keeping the code clean.

## Enums

Enums, short for enumerations, allow us to define a set of named constants that are represented by integer values.
They’re useful for giving meaningful names to integer values, making the code easier to read and less prone to errors.

One big advantage of enums is that they limit the possible values a variable can hold.
Instead of using plain integers to represent different states, enums ensure only predefined values are allowed.

For example, say we want to represent different screen states in a program, like a Start Screen and an End Screen.
Without enums, we might use integers like `1` for the start screen and `2` for the end screen—but this can cause bugs if invalid numbers are used accidentally.
Enums prevent that by defining a fixed set of allowed values.

```cpp title="main.cpp"
#include <iostream>

enum Screen {
	StartScreen,
	EndScreen
};

int main() {
	Screen screen = StartScreen;

	std::cout << "Screen state: " << screen << std::endl;
	std::cout << "Enum byte size: " << sizeof(screen) << std::endl;
}
```

``` title="output"
Screen state: 0
Enum byte size: 4
```

In this example, the enum members start at `0`, which is the default behavior in C++, and each subsequent member increases by `1`.
However, you can specify a custom starting value, and any subsequent members without an explicit value will continue incrementing from the last assigned number.
This continues until another manually assigned value appears, which resets the counting from that new number.

By default, an enum uses int (typically 4 bytes) as the underlying type to store its values.
But if that's more memory than necessary, you can choose a smaller integer type by using a colon after the enum name, followed by the type you want.

```cpp title="main.cpp"
#include <iostream>

enum Screen : char {
    StartScreen = 1,
    EndScreen,       // Becomes 2
    SomeScreen = 10,
    AnotherScreen    // Becomes 11
};

int main() {
    Screen screen = AnotherScreen;

    std::cout << "Screen state: " << static_cast<int>(screen) << std::endl;
    std::cout << "Enum byte size: " << sizeof(screen) << " byte" << std::endl;
}
```

``` title="output"
Screen state: 11
Enum byte size: 1 byte
```

!!! warning

    C++ allows you to print regular enums and use them in arithmetic expressions because they implicitly convert to integers.

    But this doesn't apply to all enums.
    If you use `enum class` (scoped enum), or if you manually specify an underlying type (like `char`, `short`, or `unsigned int`), you lose automatic conversion in many contexts — especially with `enum class`.

    This means you'll need to explicitly cast the value to an integer type before printing it or doing math with it.

As you’ve seen, regular enums in C++ are a handy way to assign names to integer or other numeric values.
They make code easier to read and help avoid using magic numbers — literal numbers in code without clear meaning.

But traditional enums have some downsides that become more noticeable as your code grows in size and complexity.
One of the main issues is that all the `enum` values share the same global scope, which can easily lead to naming conflicts.
Also, regular enums implicitly convert to integers, which can result in unexpected behavior if you're not careful.
To address these problems, C++ introduced scoped enums using `enum class`.

Scoped enums behave more strictly — and that's a good thing.
Here's what makes them different:

- No global clutter: Enum members no longer leak into the global scope.
- Type safety: You can’t accidentally treat them like integers without a cast.
- Explicit usage: You must qualify enum members with their enum name, which makes the code easier to follow.

```cpp title="main.cpp"
#include <iostream>


/*
    This approach cluters global scope

    enum GameState {
        Start,
        Playing,
        End
    };
*/

enum class MenuOption : char {
    Start = 1,
    Settings,
    Exit = 10,
    Credits
};

int main() {
    /*
        OK — enum members are globally visible

        GameState game = Start;
    */

    // Must use the prefix
    MenuOption menu = MenuOption::Settings; 

    /*
        OK — Implicit conversion works

        std::cout << "GameState (Start) as int: " << game << std::endl;
    */
    
    // Must cast — scoped enums don't convert automatically
    std::cout << "Screen state: " << static_cast<int>(menu) << std::endl;

    // std::cout << "Size of GameState: " << sizeof(game) << " bytes" << std::endl;
    std::cout << "Enum byte size: " << sizeof(menu) << std::endl;
}

```

``` title="output"
Screen state: 2
Enum byte size: 1
```

## Classes

By now, we've looked at different ways to group related pieces of data — using structs, unions, and even basic enums.
These approaches all come from the C side of things.
While they don't behave identically in C++, they still follow the same procedural mindset.

But as your programs begin to model real-world entities — for instance, in medical software where you might represent a patient, track their medical history, and perform operations like scheduling appointments or updating test results — a more powerful abstraction becomes useful.
That’s where classes come in.

This isn’t to say that classes are mandatory.
Many developers stick with procedural programming, which avoids classes and sticks to functions and data structures.
It's perfectly valid and often simpler for smaller or tightly focused programs.

However, another widely used approach is Object-Oriented Programming (OOP) — and classes are at the heart of it.

A class is a blueprint for building objects that combine data and behavior.
Classes form the foundation of OOP, a style of programming that models things as self-contained units called objects.
Each object holds both state (data) and functionality (methods).

Unlike Java or C#, C++ doesn’t force you into OOP.
It leaves the choice to you.
But when your project calls for organizing logic around real-world concepts, classes give you the right tools.

Just like a struct, a class lets you group related member variables under a single name.
But with classes, you also get access control, encapsulation, and structured member functions — also known as methods — that act on the internal state of the object.

```cpp title="main.cpp"
#include <iostream>
#include <string>

class Patient {
public:
    std::string name;
    int age;

    void PrintInfo() {
        std::cout << "Patient: " << name << ", Age: " << age << std::endl;
    }
};

int main() {
    Patient patient;
    patient.name = "Unknown Patient";
    patient.age = 42;

    patient.PrintInfo();
}
```

``` title="output"
Patient: Unknown Patient, Age: 42
```

With the class implementation, we can see some key differences compared to structs.
The most notable one is access control, made possible through access specifiers.
In C++, classes support three kinds of access.

| Access Specifier | Description                                                                                                                                                                                                                  |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `public`         | Members can be accessed from anywhere in the program.                                                                                                                                                                        |
| `private`        | Members can only be accessed from within the class. This is the default access level in classes. They cannot be accessed directly from outside the class, but can be accessed indirectly through public methods.             |
| `protected`      | Like `private`, but members are also accessible by derived classes (i.e., subclasses in inheritance).                                                                                                                        |

!!! note

    In C++, the only difference between a `struct` and a `class` is their default access level: members of a `struct` are public by default, while members of a `class` are private by default.

    In C, `structs` cannot contain methods, but in C++, they can.
    However, it’s generally best to use structs for simple data grouping—types that hold multiple variables without associated functionality—to keep design intentions clear and maintain a clean separation between plain data and more complex behavior.

In the example above, the `name` and `age` members were declared as public, which allows us to set and access them directly in `main()` using the dot operator.
If they had been marked private, we wouldn’t be able to access them directly from outside the class.
Instead, we’d need to use getter and setter methods (or another kind of public interface) to interact with those values — something we’ll cover a bit later.

Another common convention is to prefix private member variables with `m_` or suffix them with `_` (e.g., `m_name` or `age_`).
Here, we’ll use the underscore suffix style as it tends to be cleaner and easier to read.
This naming convention helps clearly distinguish member variables from local variables or function parameters, especially in larger codebases.
It also improves readability, reduces naming conflicts, and eliminates the need to use the this pointer explicitly in most cases — something we’ll explore a bit later as well.

#### Static Inside Class

So far, we’ve seen how classes let each object maintain its own set of member variables with data unique to that particular object, created from the blueprint of a class like `Patient`.
But sometimes, you want a variable or function to belong to the class itself, rather than to any single object.

This can be achived by placing `static` keyword before a member variable or method’s data type, you make it shared across all instances of the class.
Instead of belonging to one object, the member belongs to the class as a whole.

For example, a static variable can count how many objects have been created — like tracking how many patients have been registered.
This shared state is useful for managing common data or resources that all objects of the class need to access.

```cpp title="main.cpp"
#include <iostream>

class Counter {
public:
    static int value;

    static void Increment() {
        ++value;
    }
};

// Static variables have to be defined outside the class
int Counter::value = 0;

int main() {
    Counter::Increment();
    Counter::Increment();

    std::cout << "Counter value: " << Counter::value << std::endl;

    /*
        You can also access static members
        via an object (though it's not preferred)
    */
    Counter c;
    c.Increment();

    std::cout << "Counter value after object call: " << c.value << std::endl;
}
```

``` title="output"
Counter value: 2
Counter value after object call: 3
```

We can see that the `value` is shared across all instances of the class.
However, since it's a static member, it must be defined outside the class—this step is required to allocate memory for it.
If you forget to do this, you'll get a linker error.

#### Static Outside Class

The `static` keyword in C++ has two distinct meanings depending on where it's used.
One use, as we saw in the previous section, is inside a class—where it makes a member variable or method shared across all instances.
But `static` can also be used outside of a class, and in that context, it means something different.

When applied at file scope, `static` makes a variable or function local to the current translation unit—in other words, visible only within the file in which it's declared.
This limits external access, prevents unintended usage from other files, and helps avoid name conflicts across a project.

If a variable is declared as `static` at the top level of a file (not inside any function or class), it exists for the lifetime of the program but remains completely invisible to other source files.
This allows the same variable name to safely exist in multiple translation units without clashing.

```cpp title="static.cpp"
int value = 20;
```

```cpp title="main.cpp"
#include <iostream>

int value = 10;

int main() {
	std::cout << "Value: " << value << std::endl;
}
```

``` title="output"
error: one or more multiply defined symbols found
```

If we declare a global variable like `value` in `static.cpp` and then declare another global `value` in `main.cpp`, the compiler produces a linker error due to multiple definitions of the same symbol.
Global variables without the static keyword have external linkage by default, meaning their names are visible across translation units.
That’s what causes the conflict here.

To avoid this problem, we can mark the variable in `main.cpp` as `static`.
This limits its visibility to just that file, preventing it from colliding with value in `static.cpp`.

```cpp title="static.cpp"
int value = 20;
```

```cpp title="main.cpp"
#include <iostream>

static int value = 10;

int main() {
    std::cout << "Value: " << value << std::endl;
}
```

``` title="output"
Value: 10
```

Now both files can define a variable named `value` without conflict, because `value` in `main.cpp` is local to that translation unit thanks to the `static` keyword.

If we actually want to share the variable from `static.cpp` with `main.cpp`, we need to remove `static` and use the extern keyword to declare the variable in `main.cpp`.
This way, `main.cpp` refers to the definition from `static.cpp`, and there's no redefinition.

```cpp title="static.cpp"
int value = 20;
```

```cpp title="main.cpp"
#include <iostream>

extern int value;

int main() {
    std::cout << "Value: " << value << std::endl;
}
```

``` title="output"
Value: 20
```

By using `extern`, we give `main.cpp` access to the variable defined in another file.
This is known as external linkage, and it allows multiple files to share global variables while keeping a single definition in one place.

#### Getters and Setters

In general, member variables should not be exposed directly outside of a class.
This encapsulation helps prevent unintended modification or misuse.
Instead, we provide getter and setter functions—public member functions designed specifically for reading or writing private data.

This approach allows us to maintain control over how internal state is accessed or changed.
For example, setters can enforce validation rules before assigning values, adding a layer of protection and integrity to our data.

```cpp title="main.cpp"
#include <iostream>
#include <string>

class Person {
public:
    std::string GetName() const {
        return name_;
    }

    /*
        Example of how a controlled assignment with validation might look:
        if the name is too long, it gets truncated before being stored.
    */
    void SetName(const std::string& name) {
        if (name.length() > 10) {
            name_ = name.substr(0, 9);
        } else {
            name_ = name;
        }
    }

private:
    std::string name_;
};

int main() {
    Person person;
    person.SetName("Random Name");

    std::cout << "Person name: " << person.GetName() << std::endl;
}
```

``` title="output"
Person name: Random Na
```

### Constant Member Functions

In the previous section, we can see a const qualifier behind the class method declaration.
When designing a class, it’s important to distinguish between member functions that modify the object’s internal state and those that don’t.
Functions that do not change the object should be explicitly marked as `const`.

That is because doing so allows those functions to be safely called on constant instances of your class—objects that have been declared immutable and are not supposed to be changed.

```cpp title="main.cpp"
#include <iostream>

class Player {
private:
    std::string name;

public:
    Player(std::string name) 
        : name(name) {}

    /*
        Notice that it is marked with const at the end.
        This tells the compiler that calling this method won’t
        change the internal state of the object. Because of that,
        we can safely call it even when the player object is const.

        Now, if we forget to add const to a method that doesn’t actually
        modify anything, we’ll run into problems—because the method can’t
        be called on const objects, even though it logically should be.
    */

    std::string GetName() const {
        return name;
    }

    void SetName(std::string newName) {
        name = newName;
    }
};

int main() {
    const Player player("Alex");

    std::cout << player.GetName() << std::endl;

    // player.SetName("Chris"); Error: can't call non-const method on const object
}
```

``` title="output"
Alex
```

### Constructors

A constructor is a special kind of method that runs automatically when an object of a class is created.
Unlike regular methods, a constructor has no return type—not even void—and its name must match the class exactly.

They are typically declared public so that objects can be created from outside the class.
However, this isn’t always the case—when inheritance is involved, constructors might be protected or private, allowing them to be called only by derived classes or within the class itself.
This is often used to enforce controlled object creation or to support specific inheritance patterns, which we’ll explore later.

The primary purpose of a constructor is to initialize member variables and set up the object so it’s ready to use immediately.
This makes code cleaner and more expressive by reducing the need for separate assignment statements after object creation.

Constructors also play a critical role in how C++ manages memory and resources.
They are involved not only in basic initialization but also in how objects are copied or moved, which can have performance implications.

If you don’t explicitly define a constructor, compiler will automatically generate a default one—a no-argument constructor that initializes the object with default values.
However, these defaults may not be meaningful unless you define them yourself.
By writing your own constructors, you gain control over how your objects are initialized and what their initial state is.
It’s also important to note that the default constructor is only implicitly generated if no other constructors are defined.
If you define any constructor, such as one that takes parameters, the compiler will not automatically generate a default constructor unless you explicitly tell it to.

!!! note

    In these examples, we’ll use `struct` instead of `class`.
    As mentioned earlier, in C++, the only difference between a `struct` and a `class` is that `struct` members are public by default, whereas `class` members are private.

    Using `struct` helps us keep the examples simple and avoids cluttering the code with access modifiers, getters and setters.

    However, in real-world programs, use `class` for better clarity, encapsulation, and adherence to object-oriented design practices.

```cpp title="main.cpp"
#include <iostream>
#include <string>

struct Human {
    std::string name_;

    // Explicitly declare the default constructor
    Human() = default;

    // Constructor with parameters
    Human(std::string name) {
        name_ = name;
    }

    std::string GetName() {
        return name_;
    }
};

int main() {
    Human default_human;   // Calls default constructor
    Human human("Random"); // Calls constructor with parameter

    std::cout << "Default human name: " << default_human.GetName() << std::endl;
    std::cout << "Named human: " << human.GetName() << std::endl;
}
```

``` title="output"
Default human name: empty string 
Named human: Random
```

#### Member Initializer List

In addition C++ provides a efficient way to write parameterized constructors using member initializer lists.
This isn’t just a stylistic choice—it can significantly improve performance by initializing members in place, rather than first default-initializing them and then reassigning values inside the constructor body.
In other words, member initializer lists construct the members directly with the given values, similar to how `emplace_back` works with vectors by constructing objects directly where they belong.

```cpp title="main.cpp"
#include <iostream>

class Vector2 {
public:
    float x;
    float y;

    // Custom default constructor using member initializer list
    Vector2()
        : x(0.0f), y(0.0f) {
    }

    // Parameterized constructor using member initializer list
    Vector2(float x, float y)
        : x(x), y(y) {
    }
};

int main() {
    Vector2 vec2(10.5f, 20.8f);

    std::cout << "x: " << vec2.x << " y: " << vec2.y << std::endl;
}
```

``` title="output"
x: 10.5 y: 20.8
```

Using a member initializer list initializes members directly with the provided values.
In contrast, assigning values inside the constructor body causes members to be default-initialized first and then assigned new values, which can be less efficient—especially for complex types.

Moreover, member initializer lists are required to initialize const members, reference members, and base classes, since these cannot be assigned inside the constructor body.

Beyond the performance benefits, member initializer lists make the constructor’s intent clearer and the code more concise by eliminating unnecessary syntax.
For these reasons, using member initializer lists should be preferred whenever possible.

#### Copy Constructor

Data copying in programming is a lot like copying a chunk of text—it duplicates the contents of one instance into another.
For primitive types, copying is straightforward and typically has little impact on performance.
But when it comes to user-defined types or containers like `std::vector` or `std::map`, copying can involve a lot more behind the scenes.

These complex types may hold large amounts of data or even manage resources like memory or file handles.
Copying such objects isn't just a matter of duplicating values—it can be expensive in terms of performance and memory usage.

That's why it's important to understand how copying works in C++, and what mechanisms are involved—especially when your types grow in complexity.

```cpp title="main.cpp"
#include <iostream>

struct Vector2 {
    double x;
    double y;
};

int main() {
    Vector2 a{
        .x = 22.33,
        .y = 55.66
    };

    // Value of 'a' was copied into 'b'
    Vector2 b = a;

    b.x = 10;

    std::cout << "Coordinate x of object a: " << a.x << std::endl;
}
```

``` title="output"
Coordinate x of object a: 22.33
```

In the example above, `b` is a copy of `a`.
It holds its own separate memory, so changes to `b.x` have no effect on `a.x`.
This is value-based copying, and it's the default behavior in C++ for objects that aren’t using pointers.

However, when pointers are involved, copying behaves differently.
Instead of copying the data being pointed to, only the address to the data is copied.
That means both variables will point to the same memory location.

```cpp title="main.cpp"
#include <iostream>

struct Vector2 {
    double x;
    double y;
};

int main() {
    Vector2* a = new Vector2{
        .x = 22.33,
        .y = 55.66
    };

    Vector2* b = a;

    b->x = 10;

    std::cout << a->x;
}
```

This kind of copying is handled by a special constructor called the copy constructor.
C++ compiler automatically provides one for your class if you don’t explicitly define it.
The default copy constructor performs a member-wise copy—copying each member field from one object to another.

If you want to customize what happens when an object is copied (for example, to deeply copy a pointer or to log when copying happens), you can define your own.

```cpp title="main.cpp"
#include <iostream>

struct Vector2 {
    double x;
    double y;

    Vector2() = default;

    Vector2(float x, float y) 
        : x(x), y(y) {
        std::cout << "Parameterized init constructor called!" << std::endl;
    }

    // Custom copy constructor signiture
    Vector2(const Vector2& other)
        : x(other.x), y(other.y) {
        std::cout << "Copy constructor called!" << std::endl;
    }
};

int main() {
    Vector2 a(22.33, 55.66); // This line will trigger the init constructor 

    Vector2 b = a;           // This line will trigger our copy constructor

    b.x = 10.0;

    std::cout << "a.x = " << a.x << std::endl;
    std::cout << "b.x = " << b.x << std::endl;
}
```

``` title="output"
Parameterized init constructor called!
Copy constructor called!
a.x = 22.33
b.x = 10
```

#### Copy Assignment Operator

The copy assignment operator is closely related to the copy constructor, but it comes into play after an object has already been constructed.

Whereas the copy constructor creates a new object from an existing one, the copy assignment operator replaces the contents of an already existing object with those from another.

C++ provides a default version of this operator if you don't write your own.
Like the default copy constructor, it performs a member-wise copy of each field.

But if your type manages resources manually, or you just want to log or tweak the behavior, you can define your own.

```cpp title="main.cpp"
#include <iostream>

struct Vector2 {
    double x;
    double y;

    Vector2() = default;

    Vector2(double x, double y)
        : x(x), y(y) {
        std::cout << "Init constructor called!" << std::endl;
    }

    Vector2(const Vector2& other)
        : x(other.x), y(other.y) {
        std::cout << "Copy constructor called!" << std::endl;
    }

    // Custom copy assignment operator
    Vector2& operator=(const Vector2& other) {
        std::cout << "Copy assignment operator called!" << std::endl;

        /*
            Self-assignment check

            Without this check, you might accidentally overwrite
            your own data unnecessarily—or worse, in more complex types,
            cause memory corruption.
        */
        if (this == &other)
            return *this;

        x = other.x;
        y = other.y;

        return *this;
    }
};

int main() {
    Vector2 a(22.33, 55.66);
    Vector2 b;

    b = a; // This triggers the copy assignment operator
    b.x = 10.0;

    std::cout << "a.x = " << a.x << std::endl;
    std::cout << "b.x = " << b.x << std::endl;
}
```

``` title="output"
Init constructor called!
Copy assignment operator called!
a.x = 22.33
b.x = 10
```

#### Move Constructor

!!! info

    We’ve already covered move semantics in detail in earlier chapters.  
    Here, we focus on how to implement a move constructor for your custom types, enabling them to support move semantics properly.

Earlier in the book, we introduced move semantics—a technique where an object’s resources are transferred instead of copied.
Now, let's see how this idea applies when working with classes and structs, especially when you're managing resources like heap-allocated memory.

Moving in programming is like reusing an object’s existing resources instead of duplicating them.
While copying creates a new version of the data, moving transfers ownership from one object to another.
This is especially useful when working with large objects, heap-allocated data, or temporary values, where performance matters.

!!! warning

    After moving from an object, that object remains in a valid but unspecified state.
    In practice, this usually means its internal pointers are null or empty, but you shouldn't make assumptions beyond that unless you reset or reassign the object yourself.

```cpp title="main.cpp"
#include <iostream>
#include <string>

int main() {
    std::string a = "Hello World!";

    std::string b = std::move(a);

    std::cout << b << std::endl;

    if (a.empty())
        std::cout << "a has been moved from" << std::endl;
}
```

``` title="output"
Hello World!
a has been moved from
```

In this example, `b` takes over the internal resources that were originally held by `a`.
After the move, `a` is left in a valid but unspecified state.
While many implementations leave moved-from strings empty, the C++ standard only guarantees that the object can still be safely destroyed or assigned to.
You shouldn't rely on its contents unless you explicitly assign a new value.

This is one of the main reasons move constructors are so useful—they allow efficient transfer of ownership without unnecessary copying, while keeping resource management safe.
But they're not really needed in classes that don't manage resources manually, such as those that only contain primitive types or rely entirely on STL containers like `std::vector` or `std::string`.

In those cases, the default move constructor generated by the compiler is usually sufficient, because all the member types already know how to move themselves efficiently.
You only need to define a custom move constructor when your class holds raw resources—like heap memory, file handles, or pointers—that require careful cleanup and transfer logic.

When your class manages raw resources—like heap memory—you'll often want to take control of how those resources are transferred between objects.
In such cases, defining a custom move constructor lets you explicitly steal ownership and prevent expensive deep copies.

```cpp title="main.cpp"
#include <iostream>

struct Vector2 {
    double* data;

    Vector2(double x, double y) {
        data = new double[2]{x, y};
    }

    // Move constructor signiture
    Vector2(Vector2&& other) {
        std::cout << "Move constructor called!" << std::endl;
        data = other.data;
        other.data = nullptr; // Mark the source as "moved-from"
    }

    void print() const {
        if (data)
            std::cout << "x: " << data[0] << " y: " << data[1] << std::endl;
        else
            std::cout << "Vector2 is in moved-from state" << std::endl;
    }
};

int main() {
    Vector2 a(22.33f, 55.66f);

    Vector2 b = std::move(a);

    a.print();
    b.print();
}
```

``` title="output"
Move constructor called!
Vector2 is in moved-from state
x: 22.33 y: 55.66
```

This pattern is essential whenever your class directly manages memory or other system resources.
It gives you precise control over how resources are moved, helping ensure both correctness and performance.

#### Move Assignment Operator

Just like how the copy assignment operator complements the copy constructor, the move assignment operator is the companion to the move constructor.

Where the move constructor initializes a new object by stealing resources, the move assignment operator replaces the contents of an already existing object by taking ownership of another object’s resources.

This is especially important when working with resource-owning types, like classes that manage dynamic memory or handles.

```cpp title="main.cpp"
#include <iostream>

struct Vector2 {
    double* data;

    Vector2(double x, double y) {
        data = new double[2]{x, y};
    }

    Vector2(Vector2&& other) {
        std::cout << "Move constructor called!" << std::endl;
        data = other.data;
        other.data = nullptr;
    }

    // Move assignment operator
    Vector2& operator=(Vector2&& other) {
        std::cout << "Move assignment operator called!" << std::endl;

        // Self-assignment check
        if (this == &other)
            return *this;

        // Clean up existing resource
        delete[] data;

        // Steal the resource
        data = other.data;
        other.data = nullptr;

        return *this;
    }

    void print() const {
        if (data)
            std::cout << "x: " << data[0] << " y: " << data[1] << std::endl;
        else
            std::cout << "Vector2 is in moved-from state" << std::endl;
    }

    ~Vector2() {
        delete[] data;
    }
};

int main() {
    Vector2 a(11.11f, 22.22f);
    Vector2 b(0.0f, 0.0f);

    b = std::move(a); // Triggers move assignment

    a.print();
    b.print();
}
```

!!! tip

    Just like with the move constructor, if your type doesn't manage raw resources manually, you probably don’t need to write a custom move assignment operator.
    The compiler-generated one is often sufficient.

    Together, the move constructor and move assignment operator complete your class's ability to participate in move semantics safely and efficiently.

#### Explicit Constructor

By default, if a constructor takes a single argument (or a set of arguments that could be matched in a single call), C++ allows implicit conversions from those argument types to the class type.
This means the compiler might automatically create an object—even if you didn’t explicitly write a constructor call—which can sometimes lead to unintended or surprising behavior.

To prevent this, you can use the `explicit` keyword to disable automatic conversions.
This makes your code safer and more predictable, especially when conversions shouldn’t happen silently.

An explicit constructor can only be called when written directly in code. The compiler won’t use it for implicit type conversions.

```cpp title="main.cpp"
#include <iostream>

struct Point {
    int x;
    int y;

    Point(int x, int y)
        : x(x), y(y) {    
    }
};

void PrintPoint(Point p) {
    std::cout << "x: " << p.x << " y: " << p.y << std::endl;
}

int main() {
    /*
        Implicit conversion: allowed because
        the constructor is not marked explicit
    */
    PrintPoint({5, 10});
}
```

``` title="output"
x: 5 y: 10
```

In this example, the compiler automatically creates a temporary `Point` from the `{5, 10}` initializer.
That’s possible because the constructor accepts two `int` values, and it isn’t marked `explicit`.

However, this might not always be desirable—especially if you want to avoid accidental conversions.
To make construction explicit-only, mark the constructor with the `explicit` keyword.

```cpp title="main.cpp"
#include <iostream>

struct Point {
    int x;
    int y;
    
    explicit Point(int x, int y)
        : x(x), y(y) {
    }
};

void PrintPoint(Point p) {
    std::cout << "x: " << p.x << " y: " << p.y << std::endl;
}

int main() {
    /*
        Error! Cannot implicitly convert {5, 10} to a Point

        printPoint({5, 10});
    */
    
    Point p(5, 10); // Explicit construction
    PrintPoint(p);  // Passing the object explicitly
}
```

``` title="output"
x: 5 y: 10
```

With explicit, the compiler no longer allows the conversion from `{5, 10}` to a `Point` automatically.
You must create the object yourself before passing it into the function—making your intention clear and preventing accidental misuse.

#### Rule of Zero and Five

In C++, the Rule of Five is an important guideline when you're working with classes that manage resources manually—like dynamic memory, file handles, or network sockets.
If you define any one of the following special member functions, you should usually define all five to ensure consistent, predictable behavior. This is the Rule of Five.

| Special Member Function      | When You Might Need It                                                                                               |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| Destructor                   | When your class manages resources like heap memory, file handles, or network sockets that must be manually released. |
| Copy Constructor             | When you need to define how your object is copied (deep vs. shallow copy).                                           |
| Copy Assignment Operator     | When your object needs proper behavior during copy assignment (e.g., `a = b`).                                       |
| Move Constructor             | When you want to efficiently transfer resources from temporaries (e.g., `MyClass a = std::move(b)`).                 |
| Move Assignment Operator     | When your object should efficiently take over resources during assignment from an rvalue (e.g., `a = std::move(b)`). |

However, if your class doesn't manage resources directly—and instead relies only on built-in types or STL containers like `std::vector` or `std::string`—then you don't need to define any of these functions yourself.
This is known as the Rule of Zero: let the compiler generate the special functions for you automatically.

### Destructors

!!! info

    This section is fairly short, as the core concept is straightforward.
    However, we’ll return to the topic later—particularly when we cover inheritance—where destructors become a bit more important.

Similar to a constructor, a destructor is a special method, but it's called automatically when an object goes out of scope or is explicitly deleted.
Its job is to clean up—releasing resources the object may have acquired during its lifetime, such as dynamic memory, file handles, or network connections.

Just like constructors, destructors have a unique syntax:

- They have no return type (not even void).
- Their name matches the class, but with a tilde `~` prefix.
- A class can have only one destructor, and it cannot take any parameters.

Destructors are declared public in most cases, especially when objects are created on the stack or dynamically via new.
However, in more advanced designs (like factories or polymorphic hierarchies), destructors may be protected or virtual. We’ll explore those patterns later.

The most common use case for a destructor is to free dynamically allocated memory or close resources.

```cpp title="main.cpp"
#include <iostream>

struct FileHandle {
    FileHandle() {
        std::cout << "File opened" << std::endl;
    }

    ~FileHandle() {
        std::cout << "File closed" << std::endl;
    }
};

int main() {
    {
        FileHandle file;
        std::cout << "Working with file..." << std::endl;
    } // file goes out of scope here — destructor is called automatically

    std::cout << "Program ending" << std::endl;
}
```

``` title="output"
File opened
Working with file...
File closed
Program ending
```

!!! note

    If your class acquires resources using `new`, `malloc`, or similar raw APIs, you should always define a destructor to release them.
    For polymorphic classes—those meant to be used via base pointers—you must make the destructor virtual to ensure the correct one is called.
    We’ll cover that in detail when we get to inheritance.

### Arrow Operator

Up until now, we've been using the dot operator to access members of structs and classes.
But that only works when we have a direct instance of the object.

When we have a pointer to an object, things change.
We can’t use the dot operator anymore — because the pointer itself doesn’t have those members.
Instead, we use the arrow operator (`->`) to access them through the pointer.

```cpp title="main.cpp"
#include <iostream>

struct Point {
    double x;
    double y;

    void Print() const {
        std::cout << "x: " << x << " y: " << y << std::endl;
    }
};

int main() {
    Point* ptr_point = new Point{41.32, 22.87};

    /*
        This won't work because ptr_point is a pointer, not an object:

        ptr_point.Print();
    */

    // But this will:
    ptr_point->Print();
}
```

``` title="output"
x: 41.32 y: 22.87
```

!!! note

    If you're thinking, "Wait, couldn't we just dereference the pointer and call the method like this: `(*ptr_point).Print()`?" — you're absolutely right.  
    However, that syntax is a bit messy to read.
    That’s exactly why the arrow operator exists: it's just a cleaner, more readable shortcut for `(*pointer).member`.

The arrow operator can even be used in clever low-level tricks — like calculating the byte offset of a struct member from the beginning of the structure.

!!! info

    This section covers advanced memory management concepts, including how data is organized in memory and how to optimize it.
    This knowledge is generally not required unless you are working in performance-critical or low-level systems.

```cpp title="main.cpp"
#include <iostream>
#include <cstdint>

struct Point {
    double x;
    double y;
    double z;
};

int main() {
    // Use the arrow operator on a null pointer to compute member offsets
    uintptr_t offset_x = (uintptr_t)&((Point*)nullptr)->x;
    uintptr_t offset_y = (uintptr_t)&((Point*)nullptr)->y;
    uintptr_t offset_z = (uintptr_t)&((Point*)nullptr)->z;

    /*
        We could technically use a regular int here, but using uintptr_t is more correct,
        as it is specifically designed to safely store pointer values as integers.
    */

    std::cout << "Byte Offset of x: " << offset_x << std::endl;
    std::cout << "Byte Offset of y: " << offset_y << std::endl;
    std::cout << "Byte Offset of z: " << offset_z << std::endl;
}
```

``` title="output"
Byte Offset of x: 0
Byte Offset of y: 8
Byte Offset of z: 16
```

### Current Instance Pointer

In C++, we have access to a special keyword called `this`.
It is only available inside member functions, and it points to the current instance of the class or object the method is acting on.
While using `this` is often optional, it becomes especially useful when there is a name clash—for example, between constructor parameters and member variables.

```cpp title="main.cpp"
struct Point {
    double x;
    double y;
    double z;

    // This works fine: the member variables are initialized using an initializer list
    Point(double x, double y, double z) 
        : x(x), y(y), z(z) {
    }

    /* 
        This will not work as expected:
        The assignments just assign the parameters to themselves,
        leaving the member variables unchanged.

        Point(double x, double y, double z) {
            x = x;
            y = y;
            z = z;
        }
    */
};
```

To resolve this ambiguity, we can use the `this` pointer to explicitly refer to the member variables.
Since `this` is a pointer, we access members using the arrow operator.

```cpp title="example"
struct Point {
    double x;
    double y;
    double z;

    Point(double x, double y, double z) {
        this->x = x;
        this->y = y;
        this->z = z;
    }
};
```

!!! note

    Remember that in many C++ style guides—like Google’s or others—it’s common to suffix member variables with `_` (e.g., `name_`) or prefix them with `m_` (e.g., `m_name`).  
    This naming convention helps avoid name clashes entirely and improves readability without needing to use `this->`.

## Friend Functions

In C++, we can grant non-member functions access to a class’s private and protected members by declaring them as friend.
Although these functions are not part of the class itself, they are treated as trusted and can interact with the internal state of the class directly.

This approach is useful when a function needs to work closely with a class—such as when overloading operators—but doesn’t logically belong as a member, helping keep responsibilities separated while still allowing close access.

```cpp title="main.cpp"
#include <iostream>

class Box {
private:
    double edge_length_;

public:
    Box(double edge_length_)
        : edge_length_(edge_length_) {
    }

    // Declare a friend function inside public access specifier
    friend void PrintEdgeLength(const Box& box);
};

// Define friend function outside the class
void PrintEdgeLength(const Box& box) {
    std::cout << "Edge length: " << box.edge_length_ << std::endl;
}

int main() {
    Box box(42.5);
    PrintEdgeLength(box);  // Can access private width directly
}
```

``` title="output"
Edge length: 42.5
```

## Inheritance

In more complex programs—especially games—we often need to structure our code in a way that’s both reusable and maintainable.
Imagine you have a `Player` class and an `Enemy` class.
At first glance, they may seem different, but both share several common traits: they might have a `name`, `hitpoints`, a `level`, or a `position` in the game world.

Rather than duplicating those shared members in every class, we can extract them into a more general-purpose class—say, `Entity`.
Both `Player` and `Enemy` can then be built on top of `Entity`, inheriting its functionality and enriching it with their own unique behavior.
This mechanism is called inheritance.

Inheritance lets us write cleaner, more modular code.
It allows us to group shared functionality in one place and extend or override it as needed in specialized types.

```cpp title="main.cpp"
#include <iostream>
#include <string>

class Entity {
protected:
    std::string name_;
    double hitpoints_;
    unsigned int level_;

public:
    /*
        'name' is passed by value (copied into this constructor),
        so to avoid copying it again into name_, we move it instead.

        This technique works with all STL containers
        like std::vector, std::map as well as std::string.
        Just remember: after moving a variable, 
        you should not use it again unless you reassign a valid value.

        In many cases this optimization isn’t strictly necessary,
        but it serves as a good example of where move semantics—
        which we introduced earlier—can be applied in real code.
    */
    Entity(std::string name, double hitpoints, unsigned int level)
        : name_(std::move(name)), hitpoints_(hitpoints), level_(level) {
    }

    void PrintInfo() const {
        std::cout << "name: " << name_ << std::endl;
        std::cout << "hitpoints: " << hitpoints_ << std::endl;
        std::cout << "level: " << level_ << std::endl;
    }
};


/*
    You will almost always want to use the public inheritance
    access specifier. See the detailed table below for why
    that’s usually the right choice.
*/
struct Player : public Entity {
    /*
        We're using the constructor of the parent class (Entity),
        which is one of the main benefits of inheritance:
        reusing shared initialization logic.
    */
    Player(std::string name)
        : Entity(std::move(name), 100.0, 1) {
    }

    void Attack() {
        std::cout << name_ << " attacks with a sword!" << std::endl;
    }
};

struct Enemy : public Entity {
    Enemy(std::string name, double hp, unsigned int lvl)
        : Entity(std::move(name), hp, lvl) {
    }

    void Taunt() {
        std::cout << name_ << " growls!" << std::endl;
    }
};

int main() {
    Player player("Archer");
    Enemy entity("Goblin", 50.0, 2);

    std::cout << "Player info" << std::endl;
    player.PrintInfo();
    player.Attack();

    std::cout << std::endl;

    std::cout << "Enemy info" << std::endl;
    entity.PrintInfo();
    entity.Taunt();
}
```

``` title="output"
Player info
name: Archer
hitpoints: 100
level: 1
Archer attacks with a sword!

Enemy info
name: Goblin
hitpoints: 50
level: 2
Goblin growls!
```

| Inheritance Type | `public` Members in Base   | `protected` Members in Base   | `private` Members in Base |
| ---------------- | -------------------------- | ----------------------------- | ------------------------- |
| `public`         | Remain `public` in derived | Remain `protected` in derived | Not accessible            |
| `protected`      | Become `protected`         | Remain `protected`            | Not accessible            |
| `private`        | Become `private`           | Become `private`              | Not accessible            |

### Polymorphism

Polymorphism refers to the ability of different classes to respond to the same function call in their own way.
Unlike regular function overloading (where multiple functions share a name but differ in parameters), polymorphism usually involves methods of a base class that are overridden in derived classes to provide specialized behavior.

```cpp title="main.cpp"
#include <iostream>

class Entity {
public:
    virtual void Speak() { // Must be marked virtual
        std::cout << "Entity speaks." << std::endl;
    }
};

class Player : public Entity {
public:
    void Speak() override {
        std::cout << "Player speaks." << std::endl;
    }
};

int main() {
    Entity* entity = new Player(); // Base class pointer to derived object
    entity->Speak();               // Calls Player::Speak() thanks to polymorphism

    delete entity;
}
```

``` title="output"
Player speaks.
```

### Virtual Methods

In the previous section, we used the virtual keyword to enable polymorphism.
Let’s now take a moment to focus on what virtual methods actually are.

A virtual method is a member function that can be overridden in a derived class.
When you call it through a base class pointer or reference, the version that's selected is based on the actual type of the object — not the type of the pointer or reference.

This mechanism is called dynamic dispatch, and it only happens when you're using a base class pointer or reference and the method is marked as virtual.

| Keyword    | Purpose                                                               | Where it's used              | Notes                                                                  |
| ---------- | --------------------------------------------------------------------- | ---------------------------- | ---------------------------------------------------------------------- |
| `virtual`  | Marks a method as overridable in derived classes                      | In base class declarations   | Enables runtime polymorphism.                                          |
| `override` | Indicates that a method intentionally overrides a base virtual method | In derived class definitions | Helps catch bugs caused by mismatched method signatures.               |
| `final`    | Prevents a virtual method from being overridden further               | In derived class definitions | Can also be used to seal entire classes: `class MyFinalClass final {}` |

!!! tip 

    Always use override when overriding virtual methods.
    It makes your intent clear and helps the compiler catch mistakes.

```cpp title="example"
class Entity {
public:
    virtual void Speak() {
        std::cout << "Entity speaks." << std::endl;
    }
};

class Player : public Entity {
public:
    void Speak() override final { // overrides and seals the method
        std::cout << "Player speaks." << std::endl;
    }
};

/*
    This would cause a compile error:

    class Boss : public Player {
        void Speak() override { ... } // Not allowed as it was marked as final
    };
*/
```

### Interfaces 

Now that we've seen how virtual methods work, let's talk about a special case: interfaces.

In C++, an interface is typically just a class that contains only pure virtual functions — methods that must be implemented by any derived class.
They have no body and are declared using `= 0`.

Interfaces are especially useful for enforcing consistent behavior across many unrelated types — for example, in plugins, simulation systems, or game objects.
You can build systems that interact with any class that implements the interface, without caring what kind of object it is.

You can think of an interface as an abstract base class made up entirely of pure virtual methods.
And yes — in C++, a class can inherit from multiple interfaces.

!!! note

    A class that contains at least one pure virtual method becomes an abstract class, and it cannot be instantiated directly.

```cpp title="main.cpp"
#include <iostream>

class Updatable {
public:
    virtual void Update() = 0; // Pure virtual function
};

class Player : public Updatable {
public:
    void Update() override {
        std::cout << "Player updates." << std::endl;
    }
};

int main() {
    Player player;
    player.Update(); // Calls Player's implementation
}
```

``` title="output"
Player updates.
```

## Singletons

Sometimes, we only ever want a single instance of a class to exist across an entire program.
Maybe it's a logger, a configuration manager, or a global game state—whatever the case, the Singleton design pattern is exactly for this pupose.
Instead of letting code freely create new objects, a Singleton class controls its own instantiation and ensures only one object is ever made.
The typical approach involves a static method that hands out a reference to the sole instance, creating it the first time it's needed.

```cpp title="example"
class Game {
private:
    Game() {
    }

public:
    Game(const Game&) = delete;
    Game& operator=(const Game&) = delete;
    Game(Game&&) = delete;
    Game& operator=(Game&&) = delete;

    static Game& Instance() {
        static Game instance;
        return instance;
    }

    void Run() {
        // Game loop would go here
    }
};

int main() {
    Game::Instance().Run(); // Only one Game ever exists
}
```

## Exercises

=== "task 1"

    ```
    Define a `Book` struct with the following fields: `title`, `author`,
    `year`, and `price`. Then, write a function that takes a `Book`
    as a parameter and prints its contents in a human-readable format.
    ```

=== "answer"

    ```cpp
    #include <iostream>
    #include <string>

    struct Book {
        std::string title;
        std::string author;
        unsigned int year;
        double price; 
    };

    void PrintBookContent(const Book& book) {
        std::cout << "Title: " << book.title << std::endl;
        std::cout << "Author: " << book.author << std::endl;
        std::cout << "Year: " << book.year << std::endl;
        std::cout << "Price: " << book.price << std::endl;
    }

    int main() {
        Book book{
            .title = "Learn CPlusPlus",
            .author = "CL0001/ElGordoOfficial",
            .year = 2025,
            .price = 0.0
        };
        
        PrintBookContent(book);
    }
    ```

---

=== "task 2"

    ```
    Create a Rectangle class that stores width and height.

    - Provide a constructor.
    - Add getter and setter methods for both members.
    - Add a method area() that returns the area of the rectangle.
    ```

=== "answer"

    ```cpp
    #include <iostream>

    class Rectangle {
    public:
        Rectangle(double width, double height)
            : width_(width), height_(height) {
        }

        double GetWidth() const {
            return width_;
        }

        double GetHeight() const {
            return height_;
        }

        void SetWidth(double width) {
            width_ = width;
        }

        void SetHeight(double height) {
            height_ = height;
        }

        double CalculateArea() const {
            return width_ * height_;
        }

    private:
        double width_;
        double height_;
    };

    int main() {
        Rectangle rectangle(10.32, 20.85);

        rectangle.SetWidth(10.0);
        rectangle.SetHeight(20.0);

        std::cout << "Rectangle area: " << rectangle.CalculateArea() << std::endl;
    }
    ```

---

=== "task 3"

    ```
    Define a class Logger that writes custom user defined messages
    into terminal when:

    - An object is constructed
    - An object is destructed

    Create several `Logger` instances inside a `CreateLoggers` function
    and observe the output order.
    ```

=== "answer"

    ```cpp
    #include <iostream>
    #include <string>

    class Logger {
    public:
        Logger(const std::string& name)
            : name_(name) {
            std::cout << "Logger " << name_ << " constructed." << std::endl;
        }

        ~Logger() {
            std::cout << "Logger " << name_ << " destructed." << std::endl;
        }

    private:
        std::string name_;
    };

    void CreateLoggers() {
        Logger log1("First");
        Logger log2("Second");
        Logger log3("Third");
        std::cout << "Exiting CreateLoggers function..." << std::endl;
    }

    int main() {
        CreateLoggers();
        std::cout << "Back in main." << std::endl;
    }
    ```

---

More to come ...