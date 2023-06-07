# Table of Contents


# Compilation
You need to manually compile C code by installing a compiler that is compatible with your os.

- For windows, use WSL and gcc
- Compile using the following command. The executable name will be the name you give to the compiled file whereas the FileToCompile is your C code

```bash
gcc -o ExecutableName FileToCompile
```

- To run the executable, precede the name of the executable with ./

```bash
./MyProgram
```

# Basics
- Terminate all lines using semicolons
- Variables do not have to be initialized but they will be given junk values

## Print Statements
 - use #include to import additional functionality to C, similar to imports in other programming languages
 - stdio.h enables you to print out to the console
 - Use printf() for easy print formatting. %s accepts chars, %lf accepts doubles, and %d accepts ints

```c
#include <stdio.h>

int main() {
    char name[] = "Brandon";
    double profit = 871.23;
    int days = 26;

    printf("%s made %lf dollars in %d days", name, profit, days);

    return 0;
}
```

## User Input
- Use `scanf()` to accept user input, cuts off at the first whitespace encoutnered. The two args are the datatype and the variable to store the value
- Use `fgets()` if you need to accept **inputs that include whitespace**. The 3 args are the variable, the amount of memory allocated, and stdin. It's important to know that fgets() will append a newline char at the end of the input, so you must strip it manually if you don't want it.
- To manipulate the string input, use string.h, navigate to the final char in the array, which will be the newline character, using `strlen(name) - 1` and replace the newline char with an empty car `\0`
- `%d` lets the program know you want to take an int as input. `%s` is for characters.
- For strings you must allocate the exact number of bytes and use a char array where a single char is one byte. Inputting greater than this will result in buffer overflow

```c
#include <stdio.h>
#include <string.h>

int main(){
    int age;
    char name[25];

    printf("\nHow old are you\n?");
    scanf("%d", &age);
    printf("You are %d years old", age);

    printf("What's your name?\n");
    fgets(name, 25, stdin);
    name[strlen(name) - 1] = `\0`;

    // greeting message
    printf("Hello %s, welcome to my C program\n", name);
    printf("You are %d years old\n", age);

    return 0;
}
```

## Math
- Use the math header file `math.h`
- sqrt() gets square root
- pow(2, 4) is 2 to the power of 4
- round() rounds the  number
- ceil() rounds up
- floor() rounds down
- fabs() absolute value
- log() logarithm
- sin(), cos(), tan() trigonometry

## Booleans
- By default there are no true/false keywords in C. You need the stdbool.h header file in order for your program to support them
- If you do not use the stdbool.h header, you need to use integers to represent true/false status. `0 is false` and every other integer is true.

## If Statements
- There is no fallthrough in if-else statements.
- Use == to check for equality.

```c
#include <stdio.h>

int main(){
    int age;

    printf("\nEnter your age: ");
    scanf("%d", &age);

    if (age >= 18) {
        printf("\nYou're an adult");
    }
    else if (age == 0) {
        printf("\nYou are a newborn");
    }
    else if (age < 0) {
        printf("\nInvalid age");
    }
    else {
        printf("\nYou're not an adult yet.");
    }

    return 0;
}
```

- Ternary operator is formatted as follows `(condition) ? (value if true) : (value if false)`

```c
// returns the maximum of two integers
int findMax(int x, int y){
    return (x > y) ? x : y;
}
```

## Switch Statement
- Terminate each case with a break statement, or else fallthrough will be permitted
- Use default statement to catch any rogue cases

```c
#include <stdio.h>

int main(){
    char grade;
    printf("\nEnter the letter grade the student receieved in your class");
    scanf("%c", &grade);

    switch(){
        case 'A':
            printf("Outstanding");
            break;
        case 'B':
            printf("Good");
            break;
        case 'C':
            printf("Passing");
            break;
        case 'F':
            printf("Failing");
            break;
        default:
            printf("Invalid letter grade");
            break;
    }

    return 0;
}
```

## Logical Operators
- && AND
- || OR
- !  NOT

## Functions
- Functions should be declared **above the main function**
- use `void` to specify a function that does not return any value

```c
void greeting() {
    printf("Hello World");
}

int main() {
    // invoke the function
    greeting();
}
```

- Passing arguments works similarly to most programming languages

```c
void greeting(char n[]) {
    printf("Hello, %s", n);
}

int main() {
    char name[] = "Brandon";

    // invoke the function
    greeting(name);
}
```

- Return a datatype

```c
double add(double x, double y){
    double result = x + y;
    printf("%lf", result);
    return result;
}
```

## Function Prototypes
