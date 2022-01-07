# Table of Contents
[Overview](#overview)

[Setup](#setup)
<br>  

# Overview
This document covers the basics of unit testing in Java using Junit5

**DISCLAIMER**: This file was purely created to be my personal reference and serve as a comprehensive record of the knowledge I gained. As such, it is not intended to be a 100% correct reference on these topics and there may be errors throughout the text. It was written in a way to facilitate learning however, and some code examples may come from various textbooks, courses, videos, discussion forums, and online articles that I came across while studying this subject.
<br>

# Setup

## Intellij IDEA (No Maven)
- You do not even have to set up a maven or gradle project to get started. Simply create a new Java project as normal using default configurations.

- Create any class. You can even use Main if you want to quickly test on a new project. Right click on the classname within your code and click "create test".

- In the next window, be sure to select JUnit5 as your testing library. If you see a notification below that saying "JUnit5 is not found in the module" click on the "fix" button to add it. Now click "ok".

- A new test class will be automatically generated with some import errors. Resolve them by hovering over the red text and selecting add JUnit5 to classpath.

- To run a single test, click within the method and hit `ctrl+shft+F10` on Wndows

- To run all the tests in the test class, click on the class and hit `ctrl+shft+F10` on Windows
<br></br>

# Testing Basics

## Simple Test
- Create a very simple calculator class like this with a method that adds two doubles

```Java
// example calculator class
public class Calculator {
  public double add(double num1, double num2) {
    return num1 + num2;
  }
}
```
- Create a JUnit test file for this class using the steps specified in [Setup](#setup). You should now have a class named CalculatorTest. We will write code in this class to test the add function in our Calculator class.

```Java
// the test class for the calculator class
public class CalculatorTest { 

  @Test
  @DisplayName("testing add()")
  public void testAdd() { 
    Calculator calculator = new Calculator(); 
    assertEquals(60, calculator.add(10, 50), 0.01); 
  }
}
```
- You can see that unit tests are nothing more than methods we create in a test-class to test methods in a class. Annotate tests using `@Test`. Describe the test using `@DisplayName`. This will also configure the text seen in the results window of your console or IDE.

- before running tests, **always create a new instance** of the class you are testing. Depending on the nature of your test class, you may need to create a new instance before every single test or just once before every single run. When in doubt, just default to creating a new instance before every single test. See more in the lifecycle methods subsection below.

- `assertEquals` is just one of the many 'assert' methods provided byt he JUnit framework. The 1st arg is the value you are expecting based on the numbers you passed into `add()`. The 2nd value is the actual result you get from `add()`. The 3rd arg is of particular relevance to floats and doubles as this is where you specify the precision (delta). Specifying this arg as 0.01 means unit test will pass even if the answer is off by plus or minus 0.01

- with `assertEquals` the test will pass if the expected and actual values are equal (given the specified delta).

>Note that it is entirely possible to create tests like this without using JUnit or even a separate test class. JUnit just makes everything easier to create, run, revise, and organize so it is a standard in most industries.

- Alternatively you may see an `assertThat()` method followed by `isEqualTo()` instead of `assertEquals()`. The syntax looks as follows. 

```Java
// the test class for the calculator class
public class CalculatorTest { 

  @Test
  @DisplayName("testing add()")
  public void testAdd() { 
    Calculator calculator = new Calculator(); 
    assertThat(calculator.add(10, 50)).isEqualTo(60)
  }
}
```

## Test Class Constraints
- When creating a test class, the class must be top-level and cannot be abstract. It should have only a single constructor with no args.

- The test class can be a static member class or a non-static inner class. The inner class must be annotated with `@nested`.

- Test methods cannot be abstract and must have a return type **void**.

<br></br>

# Assertions

## Common

- Besides `assertEquals()` there are a couple other useful assertions. All assertions can optionally accept a message as a final arg.

- `assertNotEquals()` is just the inverse of `assertEquals()`.

- `assertTrue()` and `assertFalse()` takes an expression that returns a boolean and checks to see whether it returns false or true.

- `assertNull()` checks if the object is null and `assertNotNull()` is its inverse.

- `assertSame()` takes the expected object and the actual object as args and checks to see if they refer to the same object. `assertNotSame()` is its inverse

- `fail()` forces the test to fail.

# Lifecycle Methods
- So far we have seen test methods that are annotated with `@Test` but another commonly encountered type of method in JUnit test classes are lifecycle methods. These methods are annotated with `@BeforeAll`, `@AfterAll`, `@BeforeEach`, and `@AfterEach`

- a `@BeforeAll` **static** method is used to contain code that needs to be executed before any tests are run. This method is useful for any initialization tasks. You may want to intialize a new instance of the class to test here, but this **should only be done if the instance is not modified in any way by any of the tests**.


-  an `@AfterAll` **static** method is used to run code to be executed after all tests have completed running. It is also only run once.

- a `@BeforeEach` method is used to run code that needs to execute before *each* test. This is useful when you **need a new instance of the class to test before each test**

- an `@AfterEach` method is used to run code that needs to execute after *each* test

>You may also simply use the `@Before` annotation instead of `@BeforeEach` and `@After` instead of `@AfterEach`.

```Java
// A sample class from the textbook "JUnit in Action" showing possible usages for the lifecycle functions
class SUTTest {
    private static ResourceForAllTests resourceForAllTests;
    private SUT systemUnderTest;

    @BeforeAll
    static void setUpClass() {
        resourceForAllTests = new ResourceForAllTests("Our resource for all tests");
    }

    @AfterAll
    static void tearDownClass() {
        resourceForAllTests.close();
    }

    @BeforeEach
    void setUp() {
        systemUnderTest = new SUT("Our system under test");
    }

    @AfterEach
    void tearDown() {
        systemUnderTest.close();
    }

    @Test
    void testRegularWork() {
        boolean canReceiveRegularWork = systemUnderTest.canReceiveRegularWork();

        assertTrue(canReceiveRegularWork);
    }

    @Test
    void testAdditionalWork() {
        boolean canReceiveAdditionalWork = systemUnderTest.canReceiveAdditionalWork();

        assertFalse(canReceiveAdditionalWork);
    }
}
```
- Here is another example, this time of a class that uses `@Before` to initialize an instance of a doubly linked list before running the tests.

- It is crucial to use `@Before` to instantiate the class to be tested rather than `@BeforeAll` in this case because some tests, like an testAddFirst() or testAddLast() test will make modifications to the linked list without resetting it to the original empty state.

- You also get a sneak peek at how to test for exceptions.

```Java
Public class LinkedListTest {
  
  DoublyLinkedList<Integer> list;

  @Before
  public void setup() {
    list = new DoublyLinkedList<>();
  }

  @Test
  public void testEmptyList() {
    assertThat(list.isEmpty()).isTrue();
    assertThat(list.size()).isEqualTo(0);
  }

  @Test(expected = Exception.class)
  public void testRemoveFirstOfEmpty() {
    list.removeFirst();
  }

  @Test(expected = Exception.class)
  public void testRemoveLastOfEmpty() {
    list.removeLast();
  }

  @Test
  public void testAddFirst() {
    list.addFirst(3);
    assertThat(list.size()).isEqualTo(1);
    list.addFirst(5);
    assertThat(list.size()).isEqualTo(2);
  }

  @Test
  public void testAddLast() {
    list.addLast(3);
    assertThat(list.size()).isEqualTo(1);
    list.addLast(5);
    assertThat(list.size()).isEqualTo(2);
  }
```

## Other Annotations
- `@Disabled` can be used to disable the entire test class or individual methods to indicate that the feature is still under construction. You can add a message to this tag.

```Java
@Disabled("Feature not completed")
class MyClassTest {

  @Test
  void test1() {}

  @Test
  void test2() {}
}
```

# Advanced Assertions
## Grouped Assertions

```Java

```Java
@Test
void testFullName() {
  assertAll("person", 
    () -> assertEquals("George", person.getFirstName()),
    () -> assertEquals("Washington", person.getLastName())
  );
}
```