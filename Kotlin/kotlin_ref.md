# Table of Contents
[Arrays](#arrays)


# Starting Out

## Development Environment

- For a quick online editing environment, you can go to the [Kotlin Playground](https://play.kotlinlang.org/). If you need to write a project where multiple files are involved however, you should use an IDE. [IntelliJ IDEA](https://www.jetbrains.com/idea/) is the IDE of choice for Kotlin.

- To start a Kotlin project in IntelliJ, create new project and remember to select Kotlin in the first project setup window you encounter (by default, Java will be selected). For most beginner purposes, you should choose command line program as the template and ensure that you choose IntelliJ as your build system (the default is KotlinGradle and as of the time of this writing, it has been known to produce build errors).

- If you are using Kotlin for Android development, [Android Studio](https://developer.android.com/studio) is your quickest and easiest option. More information on Android can be found in the Android reference.

## Main Method
- Every Kotlin application's starting point is going to be `fun main()`. fun is the reserved word to create a function.

```Kotlin
fun main() {
  println("Hello, word!");
}
``` 

 ## Comments
 - Kotlin supports single line and multi-line comments

```Kotlin
// This is a comment

/*
This is a multi-line
comment
*/
```

## Printing
- println() prints the message and adds a newline
- to print without the newline, use print()

```Kotlin
println("Welcome");
print("Welcome");
```

# Data

## Variables and Values
- In Kotlin, you must declare variables as either a `var` or a `val`.

- a `var` can be reassigned. It should be used to declare any data that you plant to alter throughout your program.

```Kotlin
var a = 32;
a = 43; // reassignment allowed

print(a);  // outputs 43
```

- in contrast, a `val` cannot be reassigned. It should be used to declare data that you don't want to change accidentally.

```Kotlin
val a = 32;
a = 32 // illegal
```

## Types
- As you might have been able to guess from the examples above, Kotlin supports **implicit typing** so you don't need to explicitly declare a type for every variable like in Java.

- Sometimes type inference can be ambiguous (ex. does Kotlin infer 1.22 as a double or a float?). Here are some examples to disambiguate these potentially less obvious cases.

```Kotlin
val a = 1.22     // double inferred
val b = 2        // int inferred
val c = 'J'      // char inferred
val d = "J"      // string inferred
val e = 1L       // long inferred
val f = 1.1F     // float inferred
val g = 1u       // int unsigned inferred
```
> Any number greater than the max integer value will be automatically inferred as long.
> You an also use lowercase f for float, but not lowercase l for long

- You can explicitly define types if you want. This space will be used to showcase all the supported data types in Kotlin. All types must be capitalized.

- First we start with the **primitives**, namely numbers. The max positive or negative value for byte, short, and integer types are shown below. Since you cannot include commas in numbers like in most programming languages, you can use underscores to get an easier visibility of the scale instead.

```Kotlin
val a: Byte = 127;               // 8bits
val b: Short = 32767;            // 16bits
val c: Int = 2_147_483_647       // 32bits 
val d: Long = 2_000_000_000_000  // 64bits

val e: Float = 0.5F          // precise to 6 digits
val f: Double = 0.456        // precise to 15 digits
```
> `val num: Double = 1` will throw a type mismatch error. Doubles and Floats must have a decimal somewhere.

> Doubles can also be represented using the scientific notation, for example: `123.5e11`

> Unsigned versions of all integer types also exist. These do not permit negative values and thus their maximum positive value is doubled. For example: `val a: UShort = 65535`

- These are the other datatypes. Chars are encased in single quotes wheras strings are encased in double quotes. You must always use double quotes for strings. They are not interchangeable with single quotes like in Javascript.

```Kotlin
val g: Boolean = false;
val h: Char = 'c';
val i: String = "Hello World";
```
> Unlike in some languages, char cannot be initialized to an ascii numerical value.

## Converting Types
- Kotlin does not have much support for automatically converting between types. Types must be converted explicitly.

# Math

## Division
- Dividing two integers types (byte, short, int, long) will always return an integer. The decimal part will be stripped (essentially the value will be rounded down).

```Kotlin
val x = 3 / 2;
print(x);  // prints 1
```

- To get a decimal out of this result, you must use an explicit conversion for double.

```Kotlin
val x = 3 / 2.toDouble();
print(x); // prints 1.5
```


# Strings
- In Kotlin, strings are immutable although this does not mean they cannot be overwritten if they are declared with var.

## Methods
- `lowercase()` and `uppercase()` changes case

- `length` returns the int length of the string


## Concatenation
- Strings can be concatenated using the + operator;

```Kotlin
val name = "expeditious";
val text = "The length of " + name + " is " + name.length;
```

- It may be more convenient to use string templates to concatenate.

```Kotlin
val name = "expeditious";
val text = "The length of $name is ${name.length}";
// The length of expeditious is 11
```


## Spanning Multiple Lines
- You must use triple quotes to specify a string that spans multiple lines of code.

- Additionally, doing triple quotes enables you to not have to use escapes. You can still use newlines.

- Sometimes, to make the string appear more orgainzed in code, you may add additional leading whitespace after the opening triple quotes. You can remove this by calling `trimMargin()` on the string.

```Kotlin
val text: String = """This is a very long piece of text that
will require us to use triple quotes
because it is very long"""
```

## Common Escapes
- Certain characters are reserved characters with special syntactic meaning. These must all be escaped with a forward slash. Unicode sequences must also be escaped. 

- \n and \t are newline and tab chars respectively.

```
\n \t \\ \' \" \$ 
```
``` Kotlin
print("\" Hey, I'm escaping quotes \"\n") // example
println('\uFF00') // unicode escape
```

## Getting Chars
- Navigating through characters in a string is simple in Kotlin.

```Kotlin
for (char in str) {
  print(char);
}
```

- Accessing a char

```Kotlin
val text = "Hello World"
val lastChar = text[text.length() - 1]; // gets 'd'
```



# Arrays

## Instantiating
- There are an incredibly large number of ways to instantiate an array in Kotlin. 

- The below example provides different methods for creating an array of four int values 0 - 3 followed by other userful array initializing techniques.

- The empty array will likely be the most useful in real applications. The concise constructor and implicit type are nice to have when you need to quickly serve up an array for testing purposes.

- When creating an array of primitives, there is a slight performance advantage to using the primitve type where the array is specified as an `IntArray`.

```Kotlin
// pre-filled arrays
var nums = arrayOf(0, 1, 2, 3); // implicit type
var nums = intArrayOf(0, 1, 2, 3); // primitve type
var nums = arrayOf<Int>(0, 1, 2, 3); // explicit type
var nums: IntArray = intArrayOf(0, 1, 2, 3) // alt explicit
var nums = Array(4) {i -> i*1}; // constructor type
var nums = IntArray(4) {i -> i*1}; // primitive constructor
var nums = IntArray(4) { it * 1 }; // concise constructor

// populates an array with four 7s
var nums = IntArray(4){7}

// init an empty array with specified capacity
var nums = IntArray(10)
```

# Functions

- Declared in Kotlin with the fun keyword. In this example, we call the multiplyByTwo function from within our main function and assign the result of the call to a val.

```Kotlin
fun multiplyByTwo(x: Int): Int { // with return type + param
  return x * 2
}
fun main() {
  val r = multiplyByTwo(5) // call the function
  println(r)
}
```
- Functions with no return value implicitly return a value called Unit.

```Kotlin
fun hello(): Unit {
  console.log("Hello");
}
fun world() {
  console.log("World");
}
fun main() {
  hello();
  world();
}
```

- Functions that only consist of a single expression can be simplified slightly. The brackets can be omitted.

```Kotlin
fun addThree(x: Int) = x + 3;
fun main() {
  console.log(addThree(10));
}
```
