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

> Note that it is entirely possible to create tests like this without using JUnit or even a separate test class. JUnit just makes everything easier to create, run, revise, and organize so it is a standard in most industries.

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

- `assertArrayEquals` compares the expected and actual array for equality of order and contents.

- `assertNotEquals()` is just the inverse of `assertEquals()`.

- `assertTrue()` and `assertFalse()` takes an expression that returns a boolean and checks to see whether it returns false or true.

- `assertNull()` checks if the object is null and `assertNotNull()` is its inverse.

- `assertSame()` takes the expected object and the actual object as args and checks to see if they refer to the same object. `assertNotSame()` is its inverse

- `fail()` forces the test to fail.


## Complex

- Grouped assertions are all reported together. For example, grouped assertions are ideal for testing if "builder" style classes construct objects as intended.

```Java
@Test
void testFullName() {
  assertAll("person",
    () -> assertEquals("George", person.getFirstName()),
    () -> assertEquals("Washington", person.getLastName())
  );
}
```

## Timing
- Several methods exist to check whether a unit runs in an acceptable amount of time or not. In all cases, you define the maximum allowed time as well as the method to test.

- `assertTimeout()` takes a duration as the first arg and a lambda function containing the operation to time as a second arg. It **waits for the operation to complete** before deciding whether the test passed or failed. So if there is a potential for your operation to take forever, this test will take forever to return a result.

```Java
@Test
void timeoutTest() {
  assertTimeout(ofMillis(1000), () -> {
    sortedHashMap.sort()
  })
}
```

- `assertTimeoutPreemptively()`, unlike assertTimeout() will forcefully stop the operation if it does not finish running by the specified time. This is great for processes that could potentially take forever. A recursive method that calculates fibonacci sequences (which can take 36 million years on an input size of 100) is a good example of when you want to use this assertion.


```Java
    @Test
    public void testFibonacci100() {
        BigInteger expected 
               = new BigInteger("354224848179261915075");
        BigInteger actual 
               = assertTimeoutPreemptively(Duration.ofSeconds(2),
                                           () -> fibonacci(100));
        assertEquals(expected, actual);
    }
```

## Exceptions
- There may be times when you expect a certian input to throw an error. You can test for this using `assertThrows()` which takes the class name for your exception followed by the method you are trying to call on the object

- note that `system::run()` is just the same as system.run().

```Java
private App system = new App();

@Test
@Display("Exception expected")
void testExpectedException() {
  assertThrows(ExceptionName.class, system::run);
}
```
- It is also possible to test if the message thrown by the throwable is the message you were expecting.

```Java
private App system = new App();

@Test
@Display("Exception expected")
void testExpectedException() {
  Throwable throwable = assertThrows(ExceptionName.class, () -> system.run(1000));

  assertEquals("an exception message was thrown", throwable.getMessage());
}
```

- These are some outdated (before JUnit5) methods to test for exceptions.

```
@Test(expected = Exception.class)
```

# Lifecycle Methods

- So far we have seen test methods that are annotated with `@Test` but another commonly encountered type of method in JUnit test classes are lifecycle methods. These methods are annotated with `@BeforeAll`, `@AfterAll`, `@BeforeEach`, and `@AfterEach`

- a `@BeforeAll` **static** method is used to contain code that needs to be executed before any tests are run. This method is useful for any initialization tasks. You may want to intialize a new instance of the class to test here, but this **should only be done if the instance is not modified in any way by any of the tests**.

- an `@AfterAll` **static** method is used to run code to be executed after all tests have completed running. It is also only run once.

- a `@BeforeEach` method is used to run code that needs to execute before _each_ test. This is useful when you **need a new instance of the class to test before each test**

- an `@AfterEach` method is used to run code that needs to execute after _each_ test

> You may also simply use the `@Before` annotation instead of `@BeforeEach` and `@After` instead of `@AfterEach`.

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

# Other Annotations

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

- `@Tag` is used to assign categories to your tests. You can filter tests by tag. In intellij, you can do this by editing your run configurations. Find them by navigating Run > Run > Edit Configurations > Tags (JUnit 5).

# Nested Testing

- You can incorporate nested classes into your tests whenever appropriate, that is when the inner class is closely related to the outer class.

```Java
public class NestedTestsTest {
 private static final String FIRST_NAME = "John";
 private static final String LAST_NAME = "Smith";
 @Nested
 class BuilderTest {
 private String MIDDLE_NAME = "Michael";
 @Test
 void customerBuilder() throws ParseException {
 SimpleDateFormat simpleDateFormat =
 new SimpleDateFormat("MM-dd-yyyy");
 Date customerDate = simpleDateFormat.parse("04-21-2019");
 Customer customer = new Customer.Builder(
 Gender.MALE, FIRST_NAME, LAST_NAME)
 .withMiddleName(MIDDLE_NAME)
 .withBecomeCustomer(customerDate)
 .build();
 assertAll(() -> {
 assertEquals(Gender.MALE, customer.getGende_NAME, customer.getFirstName());
 assertEquals(LASTr());
 assertEquals(FIRST_NAME, customer.getLastName());
 assertEquals(MIDDLE_NAME, customer.getMiddleName());
 assertEquals(customerDate,
 customer.getBecomeCustomer());
 });
 }
 }
}
```

# Paramterized Testing
- Instead of creating multiple test methods to test different parameters for a single function, you should use a parameterized test. You can enter all the inputs you want to test as "input,expected" pairs in a CsvSource annotation. Running this unit test will execute the test for all csv inputs.

```Java
// comma delimiter (default)
@ParameterizedTest
@CsvSource({"1016,true", "2000,true", "1500,false"})
public void testLeapYear(int year, boolean expected) {
  LeapYear ly = new LeapYear();
  assertEquals(expected, ly.isLeapYear(year));
}
```
```Java
// custom delimeter
@ParameterizedTest
@CsvSource(value = {"1016:true", "2000:true", "1500"false"}, delimiter = ',')
public void testLeapYear(int year, boolean expected) {
  LeapYear ly = new LeapYear();
  assertEquals(expected, ly.isLeapYear(year));
}
```

- Without this optimization, you would be writing something unnecessarily lengthy like this.

```Java
@BeforeEach
void setup() {
  LeapYear ly = new LeapYear();
}

@Test
public void leapYearsTest1 {
  assertTrue(ly.isLeapYear(2016));
}

@Test
public void leapYearsTest2 {
  assertTrue(ly.isLeapYear(2000));
}

@Test
public void leapYearsTest3 {
  assertFalse(ly.isLeapYear(1500));
}

```
- If you want to test an especially large number of inputs, you may want to store the parameters locally as a csv file instead.

```Java
// using external file
@ParameterizedTest
@CsvFileSource(resources = "/data.csv", numLinesToSkip = 1)
public void testLeapYear(int year, boolean expected) {
  LeapYear ly = new LeapYear();
  assertEquals(expected, ly.isLeapYear(year));
}
```
