# Table of Contents

# Overview

# Console

## Printing to Console

- Requires importing `iostream`

```cpp
/* Printing */
#include <iostream>

int main(int argc, char **argv) {

  // no newline
  std::cout << "Hello World";

  // newline
  std::cout << "Hello World" << std::endl;

  // value
  int val = 7;
  std::cout << "value: " << val << std::endl;

  // newline only
  std::cout << std::endl;

  return 0
}
```

- We can also use the following two options to be more specific about what we are printing

- `std::cerr` prints error to the console

- `std::clog` prints log message to the console

- You can forego having to type std:: by adding `using namespace std` to the top of your c++ file

```cpp
#include<iostream>
using namespace std;

int main() {
  cout << "Hello World\n";
  return 0;
}
```

<br>

## Taking User Input From Terminal

- `std::cin` reads data from the terminal

- you need to import `<string>` in order to allow c++ to store strings

- prefix String values with `std::` to specify that the value will be read from the terminal and stored

```cpp
/* User Input From Terminal */
int age;
std::string name;

std::cout << "Enter your name: ";
std::cin >> name;

std::cout << "Enter your age: ";
std::cin >> age;

std::cout << "Hi, " << name << "you are " << age << " years old." << std::endl;
```
- Multiple inputs can be taken in one line

```cpp
/* Multiple Inputs, Single Line */
int age;
std::string name;

std::cout << "Enter name and age: " << std::endl;
std::cin >> name >> age;
```
- Any **whitespace** in a string will result in unexpected behavior

- Resolve by using the iostream's `getline()` function. First param specifies that we are taking in a value, second param is the variable we are storing to

```cpp
/* Handling Whitespace */
std::string full_name;
int age;

std::cout << "Type your full name and age" << std::endl;

std::getline(std::cin, full_name);
std::cin >> age;
```

## Output Formatting
- Must import `<iomanip>` and `<ios>` to ensure that these work

- Fix the width of the line to print using `setw()`, useful for clean formatting

- By default, the text is justified right of the given space

```cpp
/* Fix Text Width */

std::cout << std::setw(10) << "Ken" << std::setw(5) << "50" << std::endl;
std::cout << std::setw(10) << "Shinya" << std::setw(5) << "34" << std::endl;
```

```cpp
/* Justify Left Instead */
std::cout << std::left;
```

```cpp
/* Set Fill Character */
std::cout << std::setfill('-');
```

```cpp
/* Show or Hide Positive Signs */
std::cout << std::showpos;
std::cout << std::noshowpos;
```

```cpp
/* Specify Base For Integer */
int var {256}
 
std::cout << std::hex << var
std::cout << std::dec << var

```

<br>

# Variables

## Types

- The types in C++ are `int`, `double`, `float`, `char`, `bool`, `void`, and `auto`

## Principles
- Variable names are **case sensitive** in c++

- All variable names should start with a **lowercase** and should never use whitespace

- In cases where you may typically use a space character, use an **underscore** character instead

## Intialization

- Various ways to initialize integers

- If you are coming from a language like Java, note that the first method in the example below actually does initialize the variable by giving it a dummy value. 

```cpp
/* Integer Initialization */

// warning, garbage value assigned to variable
int count;

// initialized to zero
int count{} 

// brace initialization to give specific value
int count{10}

// functional initialization
int count(10)

// assignment initialization
int count = 10

// throws compile time error
int count{2.9}

// loses the fractoinal data
int count(2.9)
int count = 2.9
```

## Memory Usage

- Find out how much memory a variable uses with `sizeof`

```cpp
int count = 10;

// print size of our variable
std::cout << "size: " << sizeof(count) << std::endl;

// print size of a datatype
std::cout << "size: " << sizeof(int) << std::endl;
std::cout << "size: " << sizeof(const char [20]) << std::endl;
```

## Integers
- Can also be represented in octal, hex, and binary

- They take up 4 bytes in memory

```cpp
/* Number Formats */

int num1 = 15
int num2 = 0x0f
int num3 = 0b00001111
```

- Integers are **signed by default**, to specify unsigned you must use the `unsigned` modifier

- Unsigned range: 0 to about 4 billion

- Signed range: -2billion to about +2billion

```cpp
/* Declaring an Unsigned Integer */
unsigned int {4000000000}
```

- Integers are 4 bytes by default, the number of bytes allocated can be increased to 8 by adding the `long` modifier or decreased to 2 by adding the `short` modifier.

- Note that just using a single long modifier does not guarantee an 8 byte size, if the numeric value can be stored in just 4 bytes, only 4 bytes will be allocated to the variable instead even if long is declared

- Use two long modifiers to force 8 bytes of memory allocation

```cpp
/* Integer Modifiers */

// int type implied when using short or long modifier
short int num {-456}
short num {-456}

// combining sign and size modifiers
unsigned long int {3000000000}

// 4 or 8 bytes used depending on size of value
long int {44} // 4
long int {9000000000} // 8

// forcing 8 bytes of memory
long long int {44} // 8
```

## Floating Point Types
- Include `float` which is 4 bytes, and `double` which is 8 bytes, and `long double` which is 12 bytes

- `float` has 7 digits of precision

- `double` has 15 digits of precision

- `long double` has a precision equal to or greater than `double` depending on the compiler being used

- doubles and floats support scientific notation, for example `1.56e-11`

```cpp
/* Importance of Suffixes */

// default is to assume double
double num {1.123456789123456789}

// suffix with f to specify float
float num {1.12345678912345f}

// suffix with L to specify long double
long double num {1.123456789123456789L}

// suffixes have greatest control over the type
double num{12321.1223f} // is a float, not a double
float num{1234.12343} // is double, not a float
```
> Although float in this example has more than 7 digits, the compiler will truncate the assigned value to 7 digits


## Restricting Precision Displayed
- In console print statements, force the number of digits to be displayed by using `setprecision()`

- If you pass a number that is larger than the floating point value's precision, innacurate junk values will be generated to fill the extra places.

- You must import `<ionmanip>`

```cpp
#include <iomanip>
float num {0.00068574889393f}

std::cout << std::setprecision(10);
std::cout << num << std::endl;

// RESULT: 0.000685945

// only 7 digits are precise (0.000685)
// the last 3 are junk numbers (945)

```

## Booleans
- Initialized as either `true` or `false` and take 1 byte in memory

- In the console, `true` values are printed as `1` and `false` values are printed as `0`

- We can have the console print 'true' and 'false' instead by using `boolalpha`

```cpp
/* Printing Booleans */

bool running {true}

// prints 1
std::cout << running << std::endl;

// prints "true"
std::cout << std::boolalpha
std::cout << running << std::endl;
```

## Characters and Text
- Characters are single values and must be enclosed in **single quotes**

- They are given 1 byte in memory

- Chars in c++ have ascii mappings

```cpp
/* Initializing and Printing Chars */

char character {'A'}
char character {65}

// print the char
std::cout << character << std::endl

// print the char's ascii value
std::cout << static_cast<int>(character) << std::endl
```

## Auto
- Lets the compiler guess the type, especially useful when types have long names that are cumbersome to type out

- Suffixes should be used to help the compiler deduce the type

```cpp
/* Compiler's Guesses */

auto var {1234}  // int
auto var {1234u} // unsigned int
auto var {1234ll} // long long
auto var {1234ul} // unsigned long

auto var {1.23}  // double
auto var {1.23f} // float
auto var {1.23l} // long double

auto var {'c'}  // char
```
<br>

# Math Operators

- Order of operations (PEMDAS) is always followed


## Increments and Decrements
- C++ supports the many common shorthands for manipulating integer values

```cpp
int val = 5;

val++ // 6
val-- // 5
val -=4 // 1
val *= 9 // 9
```

## Divison

- Dividing two integers will return an integer value

- For example 31/10 returns 3, not 3.1

## Comparators

- C++ supports `>`, `<`, `>=`, `<=`, `==`, and `!=`

- All comparators must be wrapped in parentheses, otherwise the compiler will complain

## Logical Operators

- C++ supports `!`, `&&`, and `||` logical operators

<br>

# Conditionals
- If statement

```cpp
/* If Statement */
int num = 6;

if (num > 5) {
  cout << "Is larger than 5\n"
}
```

- If, Else if, Else statement

```cpp
/* If, Else if, Else */
int num = 6;

if (num > 5) {
  cout << "Is larger than 5\n"
}
else if (num < 5) {
  cout << "Is smaller than 5\n"
}
else {
  cout << "Is equal to 5\n"
}
```

- Switch
```cpp
/* Switch Statement */
#include <iostream>
using namespace std;
 
int main () {
   char grade = 'D';

   switch(grade) {
      case 'A' :
         cout << "Excellent!" << endl; 
         break;
      case 'B' :
         cout << "Great!" << endl;
         break;
      case 'C' :
         cout << "Well done" << endl;
         break;
      case 'D' :
         cout << "You passed" << endl;
         break;
      case 'F' :
         cout << "Better try again" << endl;
         break;
      default :
         cout << "Invalid grade" << endl;
   }
   cout << "Your grade is " << grade << endl;
 
   return 0;
}
```

- Ternary operator, `(condition) ? if_true : if_false`

```cpp
/* "Normal" */

if (running) {
  running = false;
}
else {
  running = true;
}

/* Ternary Equivalent */
running = (running) ? false : true;
```

# Loops

- For Loop

```cpp
for (int i = 1; i <= 5; ++i) {
  cout << i << endl;
}
```

- While loop

```cpp
int count = 10;
while (count != 0) {
  count--
}
```

- Infinite while loop

```cpp
while (true) {
  //...
}
```