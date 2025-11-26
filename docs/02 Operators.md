# 02 Operators

!!! info

    This chapter goes hand in hand with the <strong><a href="/learn-cplusplus/02 Control Flow/"><span class="link">03 Control Flow/</span></a></strong> chapter.
    So keep in mind that the two chapters are closely related, and many concepts work together.
    If something seems skipped here, it's likely because it was better suited for that chapter.

In programming, operators are special symbols or keywords that perform actions on variables and values.
They allow us to manipulate data, perform calculations, and control program logic.
Whether it’s adding numbers, comparing values, or modifying bits directly, operators are fundamental to how code behaves.

At their core, operators resemble mathematical symbols, but they go far beyond basic arithmetic.

## Comparison Operators

The first and most fundamental type of operators are comparison operators, which are commonly used in conditions—such as inside `if` statements or loops.
They are similar to those found in mathematics.
These operators allow us to compare values and control the flow of a program by returning `true` or `false` based on the result.

| Operator | Description                  | Example     |
|----------|------------------------------|-------------|
| `>`      | Greater than                 | `a > b`     |
| `<`      | Less than                    | `a < b`     |
| `>=`     | Greater than or equal to     | `a >= b`    |
| `<=`     | Less than or equal to        | `a <= b`    |
| `==`     | Equal to                     | `a == b`    |
| `!=`     | Not equal to                 | `a != b`    |

```cpp title="main.cpp"
#include <iostream>

int main() {
    int user_age = 10;

    if (user_age < 0) {
        std::cout << "Invalid age. Age cannot be negative.";
	} else if (user_age < 12) {
        std::cout << "Sorry, you are too young to enter.";
	} else {
        std::cout << "Welcome to the game!";
	}
}
```

``` title="output"
Sorry, you are too young to enter.
```

## Logical Operators

Logical operators are used to combine multiple comparison expressions to form complex decision-making conditions.
When used alongside comparison operators, they enable more precise control flow.

| Operator | Name         | Description                                                    |
| -------- | ------------ | -------------------------------------------------------------- |
| `&&`     | Logical AND  | True only if both conditions are true.                         |
| `||`     | Logical OR   | True if at least one condition is true.                        |
| `!`      | Logical NOT  | Inverts the value of a condition (true → false, false → true). |

```cpp title="main.cpp"
#include <iostream>

int main() {
    int x = 5;
    bool is_ready = false;
    
    if (x > 0 && x < 10) {
        std::cout << "x is between 1 and 9\n";
	}

    if (x < 0 || x > 100) {
        std::cout << "x is out of range\n";
	}

    if (!is_ready) {
        std::cout << "Not ready yet!\n";
	}
}
```

``` title="output"
x is between 1 and 9
Not ready yet!
```

## Arithmetic Operators

Next in line are arithmetic operators.
They function just like in mathematics, helping us perform basic mathematical operations such as addition, multiplication, and more.

| Operator | Description                               | Example     |
| -------- | ----------------------------------------- | ----------- |
| `+`      | Adds two values                           | `a + b`     |
| `-`      | Subtracts one value from another          | `a - b`     |
| `*`      | Multiplies two values                     | `a * b`     |
| `/`      | Divides one value by another              | `a / b`     |
| `%`      | Computes the remainder of a division      | `a % b`     |


```cpp title="main.cpp"
#include <iostream>

int main() {
	int a = 10 * 10;
	int b = 20;
	int c = a + b;
	
	std::cout << "Number C: " << c;
}
```

``` title="output"
Number C: 120
```

!!! warning

	If a program tries to divide by zero, it will result in a runtime error (crash) because division by zero is undefined.

!!! note

	The division operator `/` behaves differently depending on the operand types.

	If both operands are integers, the result is also an integer, discarding the decimal part.
	If at least one operand is a floating-point number, the result retains decimals.

```cpp title="main.cpp"
#include <iostream>

int main() {
    std::cout << "Integer division: " << 9 / 2 << '\n';
    std::cout << "One is floating point number: " << 9.0 / 2;
}
```

``` title="output"
Integer division: 4
One is floating point number: 4.5
```

The modulus operator `%` is only valid for integers and gives the remainder of a division.
It is commonly used to check divisibility, such as determining whether a number is even or odd.

```cpp title="main.cpp"
#include <iostream>

int main() {
	int x = 7;
	
	if (x % 2 == 0) {
		std::cout << "Number is even";
	} else {
		std::cout << "Number is odd";
	}
}
```

``` title="output"
Number is odd
```

### Shortened Notations

There are also shorthand notations that make coding more concise.
Instead of writing full expressions like `a = a + b`, we can use compound assignment operators to simplify the syntax.

For example, if we want to increase or multiply `a` by `b`, we can use the following shorthand notations:

| Operator | Description                | Equivalent Expression  |
| -------- | -------------------------- | ---------------------- |
| `+=`     | Adds and assigns           | `a = a + b`            |
| `-=`     | Subtracts and assigns      | `a = a - b`            |
| `*=`     | Multiplies and assigns     | `a = a * b`            |
| `/=`     | Divides and assigns        | `a = a / b`            |
| `%=`     | Remainder and assigns      | `a = a % b`            |


In addition to these, we have increment and decrement operators.

| Operator | Description                   | Equivalent Expression  |
|----------|-------------------------------|------------------------|
| `++`     | Increases a variable by `1`   | `a = a + 1`            |
| `--`     | Decreases a variable by `1`   | `a = a - 1`            |

Difference between postfix increment and prefix increment:

- `++a` is the prefix increment: the variable is incremented first, then its new value is used in the expression.
- `a++` is the postfix increment: the variable’s current value is used first, and then it is incremented.

For clarity, prefer using the prefix method when the previous value is not needed.
In console outputs or function arguments, postfix can be misleading, as it will display or use the old value before incrementing.

```cpp title="main.cpp"
#include <iostream>

int main() {
	int a = 10;
    int b = 5;
    
    a += b;
    std::cout << "Number A after +=: " << a << '\n'; 
    
    a *= 2;
    std::cout << "Number A after *=: " << a << '\n';
    
    a++;
    std::cout << "Number A after ++: " << a++;
}
```

``` title="output"
Number A after +=: 15
Number A after *=: 30
Number A after ++: 31
```

## Operator Evaluation Order

When working with operators in C++, understanding how they are evaluated is crucial to avoid unexpected behavior.
Operator evaluation determines the order in which operations are performed and how expressions are processed.

C++ follows a set of rules to decide which operators take precedence over others in an expression.
These rules dictate whether multiplication happens before addition, or how logical and comparison operators interact.

| Term                       | Definition                                                                                                     |
| -------------------------- | -------------------------------------------------------------------------------------------------------------- |
| Operator precedence        | Defines which operator is evaluated first in an expression.                                                    |
| Operator associativity     | Determines the order of evaluation when multiple operators of the same precedence appear in an expression.     |

!!! note

	Arithmetic operators follow standard mathematical rules:
	multiplication and division are evaluated before addition and subtraction, and expressions inside parentheses are evaluated always first.

```cpp title="main.cpp"
#include <iostream>

bool first() {
	std::cout << "First\n";
	return false;
}

bool second() {
	std::cout << "Second\n";
	return true;
}

int main() {
    if (first() && second()) {
    }
}
```

``` title="output"
First
```

The output remains unchanged because the `if` statement uses the logical AND operator, which evaluates conditions from left to right.
Since `first()` returns `false`, the entire condition can never be true, so the second function is never called.

!!! note

	This behavior is known as short-circuit evaluation.

## Bitwise Operators

While arithmetic operators work with base-10 numbers, bitwise operators operate on numbers in base-2 (binary), manipulating each individual bit.
A bit is the smallest unit of memory and can be either 0 or 1. This is because they operate on transistors, which have only two states: ON (1) or OFF (0).
These bits are grouped into bytes, where 8 bits = 1 byte. A half byte (4 bits) is referred to as a nibble.

### Binary Representation

As computers store values using binary (base-2), meaning unlike our familiar decimal (base-10) system, where numbers are represented using digits from 0 to 9, they can only use the numbers 0 and 1. 

Below is a comparison of base-10 numbers and their binary (base-2) equivalents.

| Base 10     | Base 2     |
| ----------- | ---------- |
| 0           | 0          |
| 1           | 1          |
| 2           | 10         |
| 3           | 11         |
| 4           | 100        |
| 5           | 101        |

Each new bit (binary digit) doubles the range of representable numbers.

!!! tip

	You can experiment with binary, hexadecimal, and other number representations using the Windows Calculator in Programmer mode.

A 32-bit integer (regular `int`) storing the number `5` would be represented in memory like displayed below.
```
0000 0101 | 0000 0000 | 0000 0000 | 0000 0000 
```

You might wonder why the number doesn’t start from the right, as that might seem more intuitive. This depends on the endianness of your CPU.
Most modern CPUs use little-endian ordering, meaning less significant bytes are stored at lower memory addresses, while more significant bytes are stored at higher addresses.
If we were to compile the same program on a big-endian CPU, the memory layout would be reversed.

```
0000 0000 | 0000 0000 | 0000 0000 | 0000 0101
```

**Endianness affects the order of bytes, not the order of bits within a byte**, so you wouldn’t see something like this for number `5`.

```
0000 0000 | 0000 0000 | 0000 0000 | 0101 0000
```

### Conversion Between Decimal and Binary Systems

To convert a decimal number into binary, we repeatedly divide the number by 2 until 0 is reached and keeping track of the remainders.
The binary representation is formed by reading these remainders from bottom to top.

For example, converting `5` from decimal to binary.

```
5 / 2 = 2 -> reminder 1
2 / 2 = 1 -> reminder 0
1 / 2 = 0 -> reminder 1
```

Reading the remainders from bottom to top, `5` in decimal is `101` in binary.

To convert a binary number into decimal, each position in the binary representation corresponds to a power of 2, starting from 2⁰ at the rightmost bit.
The decimal value is calculated by summing up these powers where 1s appear.

For example, converting `101` from binary to decimal:

```
  1          0          1
1 × 2²  +  0 × 2¹  +  1 × 2⁰
  4     +    0     +    1    =  5
```

### Conversion Between Decimal and Hexadecimal Systems

To convert a decimal number into hexadecimal, we repeatedly divide the number by 16 until 0 is reached and keeping track of the remainders.
The hexadecimal representation is then formed by reading these remainders from bottom to top.

Hexadecimal uses digits `0–9` and letters `A–F` to represent values from `0–15`.

```
0 1 2 3 4 5 6 7 8 9 A B C D E F
```

For example, converting `192` from decimal to hexadecimal:

```
192 / 16 = 12 -> remainder  0 -> 0
12  / 16 =  0 -> remainder 12 -> C
```

Reading the remainders from bottom to top, `192` in decimal is `C0` in hexadecimal.

To convert hexadecimal back into decimal, each digit is multiplied by a power of 16, starting from the rightmost digit (which is multiplied by 16⁰).

For example, converting `C0` from hexadecimal to decimal:

```
   C         0
 C × 16¹ + 0 × 16⁰
12 × 16  + 0 ×  1  = 192
```

### Conversion Between Decimal and Octal Systems

To convert a decimal number into octal, divide the number repeatedly by 8 until 0 is reached and keep track of the remainders.
The octal number is then formed by reading the remainders from bottom to top.

Octal is base-8, so it uses digits from `0` to `7`.

For example, converting `65` from decimal to octal:

```
65 / 8 = 8 remainder 1
8  / 8 = 1 remainder 0
1  / 8 = 0 remainder 1
```

Reading the remainders from bottom to top, `65` in decimal is `101` in octal.

To convert octal back to decimal, multiply each digit by a power of 8, starting from the rightmost digit (8⁰).

For example, converting `101` from octal to decimal:

```
  1        0        1
1 × 8² + 0 × 8¹ + 1 × 8⁰
 64    +   0    +   1    = 65
```

### Examining Memory Representation in Practice

Now that we understand how binary works, we can examine how memory is structured using Visual Studio by writing a simple program.
This examination was done in Visual Studio, but it can be performed in almost any code editor or IDE.

```cpp title="main.cpp" hl_lines="5"
#include <iostream>

int main() {
	int a = 5;
	std::cout << a << '\n';
}
```

When this simple script is pasted into your code editor or IDE, place a breakpoint at the highlighted line by clicking on the empty space to the left of the line number.
This will pause the program at that exact point, right after the variable declaration, allowing us to inspect memory.

Run the program, and you’ll see it pause.
Then, in the top utility bar, navigate to Debug → Windows → Memory → Memory 1.
This will open a memory panel where you’ll find an address search field.
Enter `&a` to jump to the memory address where the variable is stored.

At this memory location, you’ll see our number 5, but instead of just 0s and 1s, you’ll also notice characters like `F` and `B`.
That’s because memory is displayed using hexadecimal system instead of binary.

Using pure binary would be inefficient for reading and searching, as long sequences of 0s and 1s are hard to interpret.
Hexadecimal is useful because it aligns perfectly with binary, each hex digit represents exactly 4 bits, meaning two hex digits make up one byte.
This significantly shortens binary representation.

```
   0    5 |    0    0 |    0    0 |    0    0
0000 0101 | 0000 0000 | 0000 0000 | 0000 0000 
```

For comparison, in decimal, 8 bits can represent values up to 255, requiring three digits.
In hexadecimal, however, `255` is simply `FF`, retaining the one hex digit per 4 bits rule.
This makes hexadecimal more efficient for representing memory values.

While these concepts primarily apply to integers, they can also be used with floating-point numbers.
However, working with floats at the binary level requires specifying the correct data type to interpret the stored bits correctly.
In most cases, though, bitwise operations are used with integers only.

### Bitwise Shift Operators

Shift operators are simple and intuitive they move the bits within a number by one to the left or the right, effectively doubling or halving the value.

```cpp title="main.cpp"
#include <iostream>

int main() {
	int a = 5;
	int b = 7;
	
	a <<= 2;
	b >>= 2;
	
	std::cout << "Number A: " << a << '\n';
	std::cout << "Number B: " << b;
}
```

``` title="output"
Number A: 20
Number B: 1
```

``` title="explanation"
<<2 0101 = 5     
--------
  010100 = 20


>>2 0111 = 7
--------
      01 = 1
```

In the examples, we can see that when a bit shifts out of scope, it is replaced with a zero on the opposite side of the shift direction.

!!! note

	It is not necessary to use bit shifting instead of multiplying normally because the compiler is smart.
	When normal multiplication is involved and optimization is possible, the compiler will use the bit-shifting method automatically.
	Therefore, it is better to keep the code simple, understandable, and readable.

### Bitwise Logic Operators

Similar to regular logical operators that compare two variables, bitwise logic operators operate directly on individual bits and compare them between each other.
Each logical operation can be described using a truth table.

#### Bitwise AND

Bitwise AND (`&`) is used for masking and isolating specific bits.
This is exactly what happens, for example, in IPv4 networking when masks are used to separate the network and host portions of an address.

|  A  |  B  | AND |
|-----|-----|-----|
|  0  |  0  |  0  |
|  0  |  1  |  0  |
|  1  |  0  |  0  |
|  1  |  1  |  1  |

```cpp title="main.cpp"
#include <iostream>

int main() {
	int a = 5;
	int b = 13;
	a &= b;
	
	std::cout << "Number A: " << a;
}
```

``` title="output"
Number A: 5
```

``` title="explanation"
  0101   = 5
& 1101   = 13
--------
  0101   = 5
```

Let’s go through how masking works using the bitwise AND operator.
We’ll use the example of a private IPv4 address: `192.168.0.10` with a `/24` prefix. 

If you have no background in networking, don’t worry — we’ll briefly explain what’s going on.

The address is a 32-bit number, divided into 4 octets separated by dots.
It represents the address of a host device, such as a laptop.

The prefix (in this case `/24`) indicates how many bits belong to the network part of the address.

``` title="explanation"
  1100 0000 . 1010 1000 . 0000 0000 . 0000 1010 = 192.168.0.10  -> host address
& 1111 1111 . 1111 1111 . 1111 1111 . 0000 0000 = 255.255.255.0 -> prefix /24
  ---------------------------------------------
  1100 0000 . 1010 1000 . 0000 0000 . 0000 0000 = 192.168.0.0   -> network address
```

This way, we can determine which network the IP address belongs to and how many bits remain for identifying host devices within that network.

In our example, the `/24` prefix means that the first 24 bits (3 full octets) are used to identify the network, while the remaining 8 bits are reserved for host addresses.
That gives us:

| Type                    | Address                          |
| ----------------------- | -------------------------------- |
| Network address         | `192.168.0.0`                    |
| Range of host addresses | `192.168.0.1` to `192.168.0.254` |
| Broadcast address       | `192.168.0.255`                  |


This technique of using a bitmask (in this case, `255.255.255.0`) allows computers and routers to efficiently determine whether two IP addresses are part of the same network, which is essential for routing traffic.

#### Bitwise OR

Bitwise OR (`|`) is used for setting and combining bits.
It’s helpful when you want to ensure that certain bits are turned on (set to 1) without affecting the others.

|  A  |  B  | OR  |
|-----|-----|-----|
|  0  |  0  |  0  |
|  0  |  1  |  1  |
|  1  |  0  |  1  |
|  1  |  1  |  1  |

```cpp title="main.cpp"
#include <iostream>

int main() {
	int a = 5;
	int b = 13;
	a |= b;
	
	std::cout << "Number A: " << a;
}
```

``` title="output"
Number A: 13
```

``` title="explanation"
  0101   = 5
| 1101   = 13
--------
  1101   = 13
```

With bitwise OR in combination with a bitwise left shift (`<<`), we can reconstruct a number after part of it was masked using a bitwise AND.
This is particularly useful in low-level programming tasks like restoring structured binary data, combining partial values, or decoding packed data fields.

The idea is that if you previously split a number into two parts — for example, by masking the lower bits and extracting the upper bits —
you can later restore the original by placing the upper part back into its correct position (via shifting) and using `|` to merge both parts.

``` title="explanation"
      00000101             -> Lower 4 bits (e.g., extracted via AND)
<< 4  10110000             -> Shift upper 4 bits back into place
--------------
      10110000
|     00000101             -> Combine both with OR
--------------
      10110101             -> Reconstructed original value
```

#### Bitwise XOR

Bitwise XOR (`^`) is commonly used in low-level programming for toggling bits, clearing flags, and also in more advanced applications like encryption, checksums, and hashing.

|  A  |  B  | XOR |
|-----|-----|-----|
|  0  |  0  |  0  |
|  0  |  1  |  1  |
|  1  |  0  |  1  |
|  1  |  1  |  0  |

```cpp title="main.cpp"
#include <iostream>

int main() {
	int a = 5;
	int b = 13;
	a ^= b;
	
	std::cout << "Number A: " << a; 
}
```

``` title="output"
Number A: 8
```

``` title="explanation"
  0101  = 5
^ 1101  = 13
------
  1000  = 8
```

An interesting property of XOR is that when a number is XORed with itself, the result is always zero.

Another powerful aspect of XOR is its reversibility, if you XOR a number with another value and then XOR the result with the same value again, you get back the original number.
This enables swapping values without a temporary variable.

```cpp title="main.cpp"
#include <iostream>

int main() {
    int a = 5;
    int b = 13;
	
    std::cout << "Before swap: a = " << a << ", b = " << b << '\n';
	
    a ^= b;
    b ^= a;
    a ^= b;
	
    std::cout << "After swap:  a = " << a << ", b = " << b;
}
```

``` title="output"
Before swap: a = 5, b = 13
After swap:  a = 13, b = 5
```

``` title="explanation"
  0101  = 5
^ 1101  = 13
------
  1000  = 8   -> new value of 'a'
^ 1101  = 13
------
  0101  = 5   -> new value of 'b'
^ 1000  = 8
------
  1101  = 13  -> new value of 'a'
```

#### Bitwise NOT

Bitwise NOT (`~`) is used to invert all bits in a number.

|  A  | NOT |
|-----|-----|
|  0  |  1  |
|  1  |  0  |

```cpp title="main.cpp"
#include <iostream>

int main() {
	int a = 5;
	std::cout << "Original: " << std::bitset<4>(a) << '\n';
	std::cout << "Inverted: " << std::bitset<4>(~a);
}
```

``` title="output"
Original: 0101
Inverted: 1010
```

``` title="explanation"
~ 0101  = 5
------
  1010  = 10
```

### Writing Binary, Hexadecimal and Octal Values in C++

We covered an introduction to binary number representation and how bitwise operators manipulate individual bits.
So far, we’ve used integers assigned through the decimal system, but it’s also possible to assign values directly using binary, hexadecimal, or even octal notation.
This is done by prefixing the number with `0b` for binary, `0x` for hexadecimal, or a leading `0` for octal, followed by the appropriate value.

```cpp title="main.cpp"
#include <iostream>

int main() {
	int a = 0b0101;
	int b = 0xff;
	int c = 015;
	
	std::cout << "Number A: " << a << '\n';
	std::cout << "Number B: " << b << '\n';
	std::cout << "Number C: " << c;
}
```

``` title="output"
Number A: 5
Number B: 255
Number C: 13
```

This can also be done in reverse, we can assign values using decimal numbers and output their binary, hexadecimal, and octal representations.
However, for the binary representation, we will need the `<bitset>` header file.

```cpp title="main.cpp"
#include <iostream>
#include <bitset>

int main() {
	int a = 5;
	int b = 13;
	
	std::cout << "Decimal: "   << a
	          << " | Binary: " << std::bitset<4>(a)
			  << " | Hex: "    << std::hex << a
			  << " | Octal: "  << std::oct << a << std::dec << '\n';
	std::cout << "Decimal: "   << b
			  << " | Binary: " << std::bitset<4>(b)
			  << " | Hex: "    << std::hex << b
			  << " | Octal: "  << std::oct << b << std::dec;
}
```

``` title="output"
Decimal: 5 | Binary: 0101 | Hex: 5 | Octal: 5
Decimal: 13 | Binary: 1101 | Hex: d | Octal: 15
```

The new function used here is `std::bitset<T>(int)`, which converts an integer into its binary representation as a string.
Replace `T` with the number of bits you want to display, and provide the integer value as the argument.

For hexadecimal output, we use `std::hex`, which tells the stream to display numbers in base 16.
Similarly, `std::oct` is used for base 8 representation.

!!! warning

	Remember to reset the output stream back to decimal using `std::dec`, as the formatting change is persistent.
	If you don’t reset it, the program will continue printing numbers in the last format you set.

## Ternary Operator

The ternary operator is a shorthand for an `if-else` statement, often called an inline if statement.
It provides a more concise way to express simple conditional expressions.

It uses the `?` operator to check a condition and the `:` operator to separate the two possible results (true case : false case).

```cpp title="main.cpp"
#include <iostream>

int main() {
	int number = 5;
	
	if (number % 2 == 0) {
		std::cout << "number is even\n";
	} else {
		std::cout << "number is odd\n";
	}

	number % 2 == 0 ? std::cout << "number is even\n"
	                : std::cout << "number is odd\n";
}
```

``` title="output"
number is odd
number is odd
```

## Operator Overloading

!!! info

	This section is considered more advanced at this point, as it requires knowledge of functions and user-defined types.
	If you're not yet comfortable with those concepts, feel free to skip this section and return to it later once you're more confident.

Behind the scenes, operators are just functions.
Much like functions, they can also be overloaded—that is, given custom behavior for specific types.

This can be useful when working with custom data structures like vectors, matrices, or complex numbers.
For instance, if you create a class representing a 3D vector, it would be convenient to use `+` to add two vectors together, rather than calling a member function like `add()` manually.

```cpp title="main.cpp"
#include <iostream>

class Vector3 {
public:
    Vector3(double x, double y, double z)
        : x_(x), y_(y), z_(z) {}

    Vector3 operator+(const Vector3& other) const {
        return Vector3(x_ + other.x_, y_ + other.y_, z_ + other.z_);
    }

    friend std::ostream& operator<<(std::ostream& os, const Vector3& v) {
        os << "(" << v.x_ << ", " << v.y_ << ", " << v.z_ << ")";
        return os;
    }

private:
    double x_;
    double y_;
    double z_;
};

int main() {
    Vector3 position(13.22, 51.79, 73.46);
    Vector3 speed(54.87, 91.37, 65.87);

    Vector3 result = position + speed;

    std::cout << "Resulting vector: " << result << '\n';
}
```
 
``` title="output"
Resulting vector: (68.09, 143.16, 139.33)
```

Operator overloading can make code more intuitive and expressive, but it should be used with care.

!!! warning

	Only overload operators when it makes logical sense.
	For example, overloading `+` for vectors or `==` for custom types is clear and intuitive.
	Overloading `++` or `!` for unrelated classes, on the other hand, can make code confusing.


## Questions

=== "question 1"

	Name the six basic comparison operators in C++.

=== "answer"

	`>` , `<` , `>=` , `<=` , `==` , `!=`

---

=== "question 2"

	What is the difference between the `&&` and `||` logical operators?

=== "answer"

	`&&` (AND) is true only if both conditions are true.  
	`||` (OR) is true if at least one condition is true.

---

=== "question 3"

	How does integer division differ from floating-point division in C++?

=== "answer"

	Integer division discards any decimal part, while floating-point division retains decimals if at least one operand is a floating-point number.

---

=== "question 4"

	Which operator is used to find the remainder of an integer division?

=== "answer"

	The modulus operator

---

=== "question 5"

	What is the difference between `a++` and `++a`?

=== "answer"

	`a++` is the postfix increment: the current value of `a` is used first, then it is incremented.  
	`++a` is the prefix increment: `a` is incremented first, then the new value is used.

---

=== "question 6"

	What is operator precedence?

=== "answer"

	Operator precedence defines the order in which operators are evaluated in an expression.

---

=== "question 7"

	What does short-circuit evaluation mean?

=== "answer"

	In logical expressions, evaluation stops as soon as the result is determined.  
	For example, in `false && expr`, `expr` is never evaluated.

---


=== "question 8"

	Name the six main bitwise operators in C++.

=== "answer"

	AND (`&`), OR (`|`), XOR (`^`), NOT (`~`), left shift (`<<`), right shift (`>>`)

---

=== "question 9"

	What is the ternary operator and how does it work?

=== "answer"

	The ternary operator `? :` is a shorthand for `if-else`.  
	Syntax: `condition ? expression_if_true : expression_if_false`.


## Exercises

=== "exercise 1"

	Write a program that asks the user for two doubles and prints their sum, difference, product, and quotient.

=== "answer"

	```cpp
	#include <iostream>

	int main() {
		double a;
		double b;
		
		std::cout << "Enter first number: ";
		std::cin >> a;
		
		std::cout << "Enter second number: ";
		std::cin >> b;
		
		std::cout << "Their sum is: " << a + b << '\n';
		std::cout << "Their difference is: " << a - b << '\n'; 
		std::cout << "Their product is: " << a * b << '\n';
		
		if (b != 0) {
			std::cout << "Their quotient is: " << a / b;
		} else {
			std::cout << "Cannot divide by zero!";
		}
	}
	```

---

=== "exercise 2"

	Write a program that asks the user for an integer and prints whether it is divisible by both 3 and 5.

=== "answer"

	```cpp
	#include <iostream>

	int main() {
		int number;
		
		std::cout << "Enter a number: ";
		std::cin >> number;
		
		if (number % 3 == 0 && number % 5 == 0) {
			std::cout << "Number is divisible by both 3 and 5.";
		} else {
			std::cout << "Number is not divisible by both 3 and 5.";
		}
	}
	```

---

=== "exercise 3"

	Write a program that reads an integer and prints its square and cube using arithmetic operators.

=== "answer"

	```cpp
	#include <iostream>

	int main() {
		int number;
		
		std::cout << "Enter a number: ";
		std::cin >> number;
		
		std::cout << "Square of the number is " << number * number << '\n';
		std::cout << "Cube of the number is " << number * number * number;
	}
	```

---

=== "question 4"

	Write a program that reads two integers and uses bitwise operators to print their AND, OR, XOR, and NOT results.

=== "answer"

	```cpp
	#include <iostream>

	int main() {
		int a;
		int b;
		
		std::cout << "Enter first number: ";
		std::cin >> a;
		
		std::cout << "Enter second number: ";
		std::cin >> b;
		
		std::cout << "Bitwise AND: " << (a & b) << '\n';
		std::cout << "Bitwise OR: " << (a | b) << '\n';
		std::cout << "Bitwise XOR: " << (a ^ b) << '\n';
		std::cout << "Bitwise NOT of a: " << ~a << '\n';
		std::cout << "Bitwise NOT of b: " << ~b;
	}
	```
