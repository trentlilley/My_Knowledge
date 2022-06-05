# Table of Contents

[Overview](#overview)

[Ordering Collections of Objects](#ordering-collections-of-objects)

[Filtering Lists](#filtering-lists)

[Functional Programming With Streams](#functional-programming-with-streams)
<br></br>

# Overview
These notes cover how to use Java 8 features to more concisely code routine and lengthy tasks in Java such as sorting lists of objects, filtering lists, populating maps, and iterating through collections. These methods are typically not taught in beginning Java courses and are essential for understanding modern Java code. Additional topics covered include modern practices for parallel / concurrent programming, functional programming, optionals, and modules.

**DISCLAIMER**: This file was purely created to be my personal reference and serve as a comprehensive record of the knowledge I gained. As such, it is not intended to be a 100% correct reference on these topics and there may be errors throughout the text. It was written in a way to facilitate learning however, and some examples may come from various  textbooks, courses, videos, discussion forums, and online articles that I came across while studying this subject. 
<br></br>

# Ordering Collections of Objects

## Old Methods
- traditionally you would need to have the class extend the **comparable interface** (implement Comparable`<Object>`). This enables you to define ordering for your objects

- Integer value 1 would be returned by **compareTo()** if the object is greater than o, -1 if less than o, and 0 if equal to o

```Java
// example rectangle class, order by area least to greatest
public class ComparableRectangle extends Rectangle implements Comparable {
    public ComparableRectangle (double width, double height) {
        super(width, height);
    }

    public int compareTo(Object o) {
        if (getArea() > o.getArea()) return 1
        else if (getArea() < o.getArea()) return -1
        else return 0
    }
}

// main method, sort a list of rectangle objects
public static void main(String[] args) {
    List<ComparableRectangle> rectangles = new ArrayList<>();

    rectangles.add(new ComparableRectangle(1.0, 1.0));
    rectangles.add(new ComparableRectangle(1.5, 1.5));

    // error if ComparableRectangle did not implement comparable
    Collections.sort(rectangles)
}
```
- There is also the **comparator** interface which can be implemented as an instance unlike comparable which must be bound to a class. This is a useful alternative to comparable if you cannot add the extends comparable to a class because it is read-only.

- it is also used if you want additional ways to order your objects as you can only implement a single compareTo function with the comparable interface. Instead of only being able to order our rectangles by area, maybe we want to order by perimeter

- in either case, you typically use an anonymous inner class to create an instance of Comparator. You must declare its type and implement the **compare()** function 

```Java
// example rectangle class, now without the comparable interface
public class ComparableRectangle extends Rectangle {
    public Rectangle (double width, double height) {
        super(width, height);
    }
}

// main method, sort a list of rectangle objects
public static void main(String[] args) {
    List<ComparableRectangle> rectangles = new ArrayList<>();

    rectangles.add(new ComparableRectangle(1.0, 1.0));
    rectangles.add(new ComparableRectangle(1.5, 1.5));

    // instance of Comparator that orders by perimeter
    Comparator<ComparableRectangle> comparePerimeter = new Comparator<ComparableRectangle>() {
        public int compare(ComparableRectangle r1, ComparableRectangle r2) {
            if (r1.getPerimeter() > r2.getPerimeter()) return 1;
            else if (r1.getPerimeter() <> r2.getPerimeter()) return -1;
            else return 0
        }
    }

    // sort using the comparator by passing it as a second arg
    Collections.sort(rectangles, comparePerimeter);
}
```

- it is worth noting that by default, Java objects like String and Integer already have a compare() method defined. Similarly, primitives have pre-defined compareTo() methods.

```Java
Rectangle r1 = new Rectangle(10, 20);
Rectangle r2 = new Rectangle(20, 40);

// these equivalent statements print -1 since area r1 < area r2
System.out.println(Double.compare(r1.getArea(), r2.getArea()));
System.out.println(r1.getArea().compareTo(r2.getArea()));
```

- these are often used in place of custom if / else if / else blocks when comparng simple data types. We can rework the Comparable Rectangle's compareTo() in this way below. 

```Java
// new
public int compareTo(Object o) {
    return Double.compare(getArea(), o.getArea());
}

// old
public int compareTo(Object o) {
    if (getArea() > o.getArea()) return 1
    else if (getArea() < o.getArea()) return -1
    else return 0
}
```

## Modern Methods
- Now that we have sufficient background in the traditional ways to compare two instances of the same object in Java, take a look at the two examples below. They are equivalent, but the latter uses more modern Java syntax.

- for context, we have a list of Apple objects stored in a list called inventory. We want to sort the apples in inventory by weight

```Java
// traditional method
Collections.sort(inventory, new Comparator<Apple>() {
  public int compare(Apple a1, Apple a2){
    return a1.getWeight().compareTo(a2.getWeight());
  }
});
```

```Java
// modern method
inventory.sort(comparing(Apple::getWeight));
```

- in modern Java, methods can be first-class citizens like variables. This means that they can be passed around throughout the code in the same way variables can. 

- `Apple::getWeight` can be thought of as a first class method, it represents the result returned from calling getWeight() on an instance of Apple.

- `comparing()` is an example of one of the many new convenience library functions introduced in modern versions of Java and must be used in conjunction with `sort()` when sorting custom objects. 

[Return To Top](#table-of-contents)
<br><br/>

# Filtering Lists

## Java 101 Method
- below is an easy-to-understand but verbose and old-school example of how you may construct a filter to create a list of apples that are green and another filter to create a list of apples that are over 150g. Both of these are processed from a list of Apple objects named inventory.

```Java
/*main method*/
public static void main(String[] args) {
    List<Apple> inventory = new ArrayList<>();

    inventory.add(new Apple(149, "green"));
    inventory.add(new Apple(155, "red"));
    inventory.add(new Apple(134, "green"));
    inventory.add(new Apple(152, "yellow"));
    
    // filters
    List<Apple> greenApples = filterGreenApples(inventory);
    List<Apple> heavyApples = filterHeavyApples(inventory);
}

/*takes a list of apples and returns a list containing only green apples*/
public static List<Apple> filterGreenApples(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>();

    // filter for green apples
    for (Apple apple : inventory) {
        if (apple.getColor().equals("green")) {
            result.add(apple);
        }
    }
    return result;
}

/*takes a list of apples and returns a list containing apples > 150g*/
public static List<Apple> filterHeavyApples(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>();

    // filter for heavy apples
    for (Apple apple : inventory) {
        if (apple.getWeight() > 150) {
            result.add(apple);
        }
    }
    return result;
}
```
## Behavioral Parameterization Method

- notice how in the above example we had to create two entirely separate methods for functions that differed only by a single line in the if conditional.

- to minimize this kind of repetition, we can use a technique called **behavioral parameterization** which modern versions of Java support.

- the line `Apple::isGreenApple` can be seen as a variable representation or a stand-in for the result of an Apple instance being passed into the `isGreenApple()` method

- a **predicate** must be used in place of the line of differing code and it provides a way to "pass" a method into the if statement. A predicate, by definition, is a function that returns a boolean. A predicate interface must be created as shown below.

```Java
/*main method*/
public static void main(String[] args) {
    List<Apple> inventory = new ArrayList<>();

    inventory.add(new Apple(149, "green"));
    inventory.add(new Apple(155, "red"));
    inventory.add(new Apple(134, "green"));
    inventory.add(new Apple(152, "yellow"));

    // filters
    filterApples(inventory, Apple::isGreenApple);
    filterApples(inventory, Apple::isHeavyApple);
    
}

public static boolean isGreenApple(Apple apple) {
    return apple.getColor().equals("green");
}

public static boolean isHeavyApple(Apple apple) {
    return apple.getWeight > 150;
}

public interface Predicate<Test> {
    boolean test(Test t);
}

/*single filter method*/
public static List<Apple> filterApples(List<Apple> inventory, Predicate<Apple> predicate) {
    List<Apple> result = new ArrayList<>();

    for (Apple apple : inventory) {
        if (p.test(apple)) {
            result.add(apple);
        }
    }
    return result;
}
```
## Stream Method

- the above example is still exceedingly verbose and takes up more space than needed requiring the programmer to write several additional methods that can be detrimental to code clarity

- most modern programming languages have some sort of built-in filter feature. In modern Java, we use what are called **streams**. They are similar to collections but are more concerned about computations on data rather than storage of data.

- call the `.stream()` method on a collection and process it using additional functions like `.filter()` which is where you place your conditional logic as a lambda expression and `.collect()` to configure how the data is returned. You no longer need to construct multiple methods just to perform simple filters.

```Java
public static void main(String[] args) {

    List<Apple> inventory = new ArrayList<>();

    inventory.add(new Apple(149, "green"));
    inventory.add(new Apple(155, "red"));
    inventory.add(new Apple(134, "green"));
    inventory.add(new Apple(152, "yellow"));

    // filters
    List<Apple> greenApples = inventory.stream()
        .filter((Apple a) -> a.getColor.equals("green"))
        .collect(toList());

    List<Apple> heavyApples = inventory.stream()
        .filter((Apple a) -> a.getWeight() > 150)
        .collect(toList());
}
```
- streams also make **parallel processing** much simpler. Parallel processing takes advantage of the multiple cores a modern CPU has by dividing computational work between those cores. This is crucial for processing enormous amounts of data in an acceptable time frame.

- below is an example of the heavyApples filter above written in parallel. Simply exchange the `.stream()` method for `.parallelStream()`.

```Java
List<Apple> heavyApples = inventory.parallelStream()
    .filter((Apple a) -> a.getWeight() > 150)
    .collect(toList());
```


<br><br/>

# Streaming

## Stream Basics
- Core functional programming concepts are based around the use of streams. The elements of a stream are computed on-demand, that is, only when they are needed, and this offers significant performance benefits over collections.

- We provide another code example that generates a list of dish names from a restaurant menu that are under 400 calories. Additionally, they are sorted by number of calories.

- Here is a solution without using streams. For brevity, the implementation of the source list `menu` is not included.

```Java
List<Dish> lowCaloricDishes = new ArrayList<>();

// iterate over each dish in the menu and collect the ones under 400 cal
for(Dish dish: menu) {
    if(dish.getCalories() < 400) { 
        lowCaloricDishes.add(dish);
    }
} 

// order the low calorie dishes by number of calories
Collections.sort(lowCaloricDishes, new Comparator<Dish>() { 
    public int compare(Dish dish1, Dish dish2) {
        return Integer.compare(dish1.getCalories(), dish2.getCalories());
    }
}); 

List<String> lowCaloricDishesName = new ArrayList<>();

// collect the names of all low calorie dishes
for(Dish dish: lowCaloricDishes) {
    lowCaloricDishesName.add(dish.getName()); 
}
```

- for a simple query this is a lot of code to type out. Additionally, we have to instantiate many list objects, one of which `lowCaloricDishes` is a garbage variable that serves no purpose other than to temporarily contain our data

- we can reduce this to 6 lines of code and remove the garbage variable by using stream operations.

- `.stream()` is first called on the collection we wish to perform queries on and it enables us to use the other methods.

- `.filter()` takes a lambda expression as an arg where the lambda arg `d` is a dish in menu. This line of code acts like a more concise for each loop with a nested condition.

- `.sorted()` works similarly to Collections.sort with a comparator passed into it. `Dish::getCalories` is the result of calling `.getCalories()` on a Dish object in menu. The dishes that passed the filter are now sorted by caloric value.

- `.map()` takes an argument. This arg defines the operation that should be performed on each item in the filtered, sorted list, in this case we want to get the name of each Dish in our filtered, sorted menu

- `.collect()` completes the query by placing all queried data into a collection of the specified type, in this case, a list.

```Java
List<String> lowCalorieDishesName = menu.stream()
    .filter(d -> d.getCalories() < 400) // get dishes under 400 cals
    .sorted(comparing(Dish::getCalories)) // sort dishes by number of cals
    .map(Dish::getName) // get dish names
    .collect(toList()); // return names as list
```
- there are other operations in addition to the ones seen in the example above

- `.limit()` takes an integer as an argument. This places a limitation on the number of items output.

- `.forEach()` a more general function that operates on each item in the stream applying the supplied function to it. This returns void and terminates a chain, meaning that no other streaming methods can be called after it, kind of like collect().

- `.count()` another terminating operation, this one returns the number of elements remaining in the stream

- `.distinct()` removes all duplicate elements from the stream.

- below is another example which retrieves all of the distinct even numbers from the list and prints them

```Java
List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 3, 2, 4);
numbers.stream()
 .filter(i -> i % 2 == 0) // 2, 2, 4 remaining
 .distinct() // 2, 4 remaining
 .forEach(System.out::println);
```

## Consumed Streams
- It is important to note that when calling .stream() on a collection, that collection is actually converted to a Stream object. This is not seen in the previous examples because it is uncommon to need to capture a stream object before calling methods like filter and map

```Java
List<String> food = Arrays.asList("fish", "steak", "potatoes", "peppers");

Stream<String> foodStream = food.stream();
```

- This stream object can **only be iterated once**. The below code throws an IllegalStateException because we attempt to process the foodStream twice.

```Java
List<String> food = Arrays.asList("fish", "steak", "potatoes", "peppers");

Stream<String> foodStream = food.stream();

List<String> foods1 = foodStream.filter(f -> f.contains("p"));
// stream consumed

// cannot use a consumed stream
// throws IllegalStateException
List<String> foods2 = foodStream.map(f -> f.toUpperCase());
```

- The solution is to simply create an entirely new stream object before the second process as we have been doing in our examples

```Java
List<String> food = Arrays.asList("fish", "steak", "potatoes", "peppers");

// first stream object
List<String> foods1 = food.stream().filter(f -> f.contains("p"));

// new stream object
List<String> foods2 = food.map(f -> f.toUpperCase());
```

## Building Streams
- It is also possible implement streaming for your own custom collection. This can be accomplished by putting a toStream method into the class representing your collection and having it use a stream builder to construct a stream of any type you wish


<br><br/>

# Functional Programming

## Pure Functions

- In functional programming, functions must be **pure**. Pure functions must **not make changes** to any global variables or rather any variables outside of the scope of the function. It may still use out of scope variables, it's just important that they are not mutated in any way.

- Below is an example of a pure function. Only the parameters and variables defined within the function are used.


``` Java
// pure function: prints the first 25 elements in a list

public static List<String> getFirst25(List<String> list) {
    int counter = 0;
    List<String> shorterList = new ArrayList<>();

    while (counter <= 25) {
        shorterList.add(list.get(counter));
        counter++;
    }

    return shorterList;
}
```

- Below is an example of the same function in a non-pure form. This is not a pure function because shorterList is declared as a class field variable which resides outside the scope of the function. Its contents are then changed within the function.

```Java
public class Main {
    private List<String> shorterList = new ArrayList<>();

    public static List<String> getFirst25(List<String> list) {
      int counter = 0;
      List<String> shorterList = new ArrayList<>();

      while (counter <= 25) {
        shorterList.add(list.get(counter));
        counter++;
      }

      return shorterList;
    }
}
```

## Functional Interfaces
- These are simply interfaces which only contain one abstract function. It can still contain other functions if they are static (ie they do not have to be implemented in a class). And of course, they can still contain fields that are final.

- Functional Interfaces are important in functional programming because since they only have one function to be implemented, they can be written as lambda expressions.

- The runnable interface is a great example of this

```Java
// Java's Runnable interface is functional
public interface Runnable {
    public abstract void run();
}
```

- It can then be implemented as follows. Using a lambda to create simple tasks for threads that do not necessitate the creation of an entire runnable class can help keep code more concise. 

```Java
Runnable runnable = () -> {
    // code run by the thread goes here
}

Thread thread = new Thread(runnable);
thread.start();
```

- This example shows how functional interfaces can be used to define first class functions in Java. We can see that runnable, which is a function, can be passed as a parameter to the Thread constructor. This works for normal methods as well (not just constructors).

- It can be annoying to have to create an entire interface for every single time we want a way to pass a function as a parameter. Luckily there is another option

## Java's Function Interface
- A nicer alternative to manually creating your own functional interfaces is to use Java's `Function` interface. T is the type of your function's parameter. R is the return type of your function. Functions created using this interface only support **one parameter**.

``` Java
public interface Function<T,R> {

    public <R> apply(T parameter);
}
```

- To use the Function interface you need to instantiate an instance of Function with the parameter and return types defined and initialize it to your desired code

- To call the function, you need to reference the name of the function and call `apply()` on it, which takes the single parameter.

``` Java
Function<Double, String> getDoubleAsString = (val) -> {
    double result = val * 2;
    return result + "";
}

double a = 5.5;
String aString = getDoubleAsString.apply(a); 
```

- If you need multiple args it is still possible but you will need to explicitly define an interface annotated with `@FunctionalInterface` like so

```Java
@FunctionalInterface
public interface FourParameterFunction<T, U, V, W, R> {
    public R apply(T t, U u, V v, W w);
}

// note that you still add the return type as the last type
public static void main(String[] args) {
    Function<String, String, Integer, Integer, String> fun = (a, b, c, d) ->
       a + " | " + (c + d) + " | " + b
};
```
## Higher Order Functions
- Are simply functions that return a function and/or take one or more functions as parameters.

- You may wonder how is this possible when functions are not first-class citizens like in Javascript. As a sort of workaround you can use lambda functions or functional interfaces

- One of the earliest higher order functions encountered by Java learners is Java's `Collections.sort()`. This is actually a higher order function that can take a function as a second parameter.

```Java
List<String> list = new ArrayList<>();
list.add("A");
list.add("B");
list.add("C");

// second parameter is a lambda function
Collections.sort(list, (String a, String b) -> {
    return a.compareTo(b);
});
```
# Reflection
- When developing code for more advanced software like libraries and plugins, it is very likely that you will need to make use of the Reflection API. This Java library contains many tools for doing things like getting a list of methods or fields that exist in a class, getting their visibility modifiers, and even extracting class informatio of packaged Jar files.

- As an example, writing a testing library like JUnit requires extensive use of reflection. 

## Getting Classes
- Yes, it is actually possible to get another class as an object. This is not the same as making an instance of a class and no constructors are involved.

- The below example gets MyClass as an object of type Class.

```Java
public class MyClass {
    // class code...
}

public class Main {
    public static void main(String[] args) {
        Class c = null
        String name = "MyClass";

        try {
            c = Class.forName(name);
        }
        catch (ClassNotFoundException e) {
            System.out.println("class " + c + " not found");
            e.printStackTrace();
        }
    }
}
```

- You may run into some complications with configuring the right `name` for the class to be passed into the `forName()` method. The name variable must be a fully qualified name. The above example will work if MyClass is within the working directory of your project or within the same java file. Otherwise, you will need to be more specific in order for Java to find the class file you are trying to get.

- For instance, within your Java project, if you want to retrieve the ArrayList class, `name` needs to be initialized to `"java.util.ArrayList"` and not just `"ArrayList"`. Note that fully qualified names are delimited by periods and not slashes like in URLs.

- When working in any development environment that uses **packages**, you will need to prefix the class name with the package directory. In an IDE, your project may come with a default package directory like `com.company`. If this is the case, you cannot just use `"MyClass"` as the name variable, you must use `"com.company.MyClass"`

- In many cases, certain methods you use to retrieve urls defining java file paths will need to be converted to fully qualified class names (FQN). You can use the method below to easily convert from URL to FQN.

```Java
private static String getFQN(String urlName) {
    return urlName.replaceAll("/", ".").substring(0, resourceName.lastIndexOf('.'));
}
```

## Get All Classes in a Jar
- The first step is to retrieve the fully qualified names for all of the classe inside the Jar. Note that we use a function called `getFQN()` to convert the URL path returned by `getName()` into a fully qualified class name. 

``` Java
// gets classes from a Jar
private static List<String> findClassNames(JarFile jar) {
    Stream<JarEntry> stream = jar.stream();

    return stream
      .filter(entry -> entry.getName().endsWith(".class"))
      .map(entry -> getFQN(entry.getName()))
      .sorted()
      .collect(Collectors.toList());
}

// converts URL names to FQN names
private static String getFQN(String urlName) {
    return urlName.replaceAll("/", ".").substring(0, resourceName.lastIndexOf('.'));
}
```

- The next step is to use the class names to retrieve the actual classes themselves. Below is an example of a method that accomplishes this

- Note the parameter passed to the URL constructor. It requires the URL of a Jar which is somewhat unique as Jar files are typically formatted as "jar:file:myjar!/" and remember to prefix this with any directory paths if the jar is located outside of your project's working directory. For example, you may have all your jars in a separate jar folder named `jars`, so you would need to pass `jars/jar:file:myjar!/`).

``` Java
private static List<Class> classesFromJar(JarFile jar, List<String> names) {
    List<Class> classes = new ArrayList<>();
    System.out.println("[check url] " + "jar:file:" + jar.getName() + "!/"); //TODO:

    try (URLClassLoader cl = URLClassLoader.newInstance(
        new URL[] { new URL("jar:file:" + jar.getName() + "!/") })) {
      for (String name : names) {
        Class jarClass = cl.loadClass(name); // load the class
        classes.add(jarClass);
      }
    } catch (ClassNotFoundException | IOException e) {e.printStackTrace();}

    return classes;
  }
```


# Concurrency

## Creating Threads

## Shared Memory Issues

## Thread Pools

## Parallel Processing with Fork Join
## Asyncronous Operations
- What is an asynchronous operation? There are many times in programming where a single process (such as processing a giant file or querying huge amounts of data from a database) will take a very long time. Rather than have our program stop dead in its tracks and not move forward until the result of this computationally intensive work is recieved, we want to allow our program to do other useful work as it waits for this long operation to complete.

- We can tell Java to abide by this behavior by assigning the result of the computationally intensive work as a `Future<>`. This is essentially an object that does not have to be assigned data immediately, it can wait for it to come at a later time.

- We can call the `get()` method of the Future class when we need the data. We can also specify how long we are willing to wait for the data to come. If it doesn't come in the specified time, control will be thrown to a catch block.

- Below is a basic usage example of Future<>. 

```Java
System.out.println("Program start");

ExecutorService pool = Executors.newCachedThreadPool()

Future<Double> result = pool.submit(
    new Callable<Double>() {
        public Double call() {
            // do some long and intensive work...
            // that returns a double
        }
    }
)

System.out.println("Program doesn't wait for result and moves on")
doOtherWork();

System.out.println("Now we need our result, let's try to get it")

try {
    // try to get the data we waited for
    // how long do we wait until we give up
    Double data = result.get(1, TimeUnit.SECONDS);
} 
catch (ExecutionException e) {
    // catches errors in the Callable's call function
}
catch (InterruptedException e) {
    // catches thread interrupts
}
catch (TimeoutException e) {
    // future could not be recieved before
    // the timeout
}
```

- In concurrency examples, we will often simulate computationally intensive tasks using the following doHeavyWork function. This is important since the whole point of asynchronous operations is to write code that can better handle computationally intensive tasks, but such tasks are typically too complicated or large to write in examples.

```Java
/* Simulates computationally intensive work */
public static void doHeavyWork() {
    try {
        Thread.sleep(1000) // 5 second
    }
    catch (InterruptedException e) {
        throw new RuntimeException(e);
    }
}
```

### CompletableFuture

- An alternative way to perform asyncronous tasks is to use the `CompletableFuture`. This method essentially allows us to make a plain old method into an asyncronous one that returns a Future. We no longer need to supply this method's logic as a callable submitted to a thread pool. The thread is instead defined internally in the method via simple lambda.

- In the example below, calculatePrice is a method within a class called Shop. It is our doHeavyWork method. It may take some time for the online store's database to return to us the price

- the `complete()` function simply sets the value returned by the heavy work when once it becomes available. They syntax for catching errors that occur during the heavy work is shown below

- the return function here *does not wait* for the result of calculatePrice. It returns the future object immediately.

- Identically to the previous example, we call the get() method with a timeout to recieve our future data

```Java
// method gets the price of a product from an online shop
// this is a method within a Shop class
public Future<Double> getPriceAsync(String product) {
    CompletableFuture<Double> futurePrice = new CompletableFuture<>();
    new Thread(() -> {
        try {
            double price = calculatePrice(product);
            futurePrice.complete(price);
        }
        catch (Exception e) {
            futurePrice.completeExceptionally(e);
        }
    });

    return futurePrice;
}
```
```Java
// Code that calls getPriceAsync
// Very similar to the first example
Shop shop = new Shop();
Future<Double> futurePrice = shop.getPriceAsync("Armchair");

System.out.println("do other work while we wait for price to come");
doOtherWork();

System.out.println("now time to get the price");
try {
    double price = futurePrice.get(1, TimeUnit.SECONDS);
}
catch (Exception e) {
    // error handling...
}
```

- The entire code within the getPriceAsync method can be simplified to the following one line using CompletableFuture's static method `supplyAsync()`

- It abstracts away the whole process of creating a CompletableFuture object yourself and a new thread

```Java
public Future<Double> getPriceAsync(String product) {
    return CompletableFuture.supplyAsync(() -> calculatePrice(product));
}
```

- To witness the power of this method, we will observe a scenario where we are asked by our company to code a findPrices() method that queries a list of 4 shops for a product and returns a list of price reports (Strings) formatted like `"ShopA: 119.99"`

- Our first solution may be to just use `parallel streams` to stream through the shops and use the map operation to create a report for each shop. It can query each of the four shops in parallel, so it only takes 1 second


```Java
// first solution, 1 second
public List<String> findPrices(String product) {
    return shops.parallelStream()
      .map(shop -> shop.getName() + ": " + shop.getPrice(product))
      .collect(toList());
}
```
>Remember that quering for prices is a heavy work function, we will say that querying a single shop for one product takes **1 second**. So without using parallel streams, this stream operation would take 4 seconds.

- The issue is that once our caller enters this function, it is blocked from doing other things for this entire 1 second. Wouldn't it be great if we could use futures? Then the function could return immediately

- we can do this by wrapping our mapping fuction `shop.getName() + ": " + shop.getPrice(product)` inside of CompletableFuture.supplyAsync(). Note that it is important that our heavy work function, getPrice(), must be inside this declaration, otherwise we get no benefits.

- Also note that we no longer strictly need to use parallelStream() because we are stepping out of this function almost immediately regardless of whether we are getting our result in 1 second or 4 seconds

- Because we are generating a list of Strings and each string in that list is being generated asyncronously we need to add extra logic involving the CompletableFuture's `join()` method to ensure that all of our strings are in before confirming that we have a value.

```Java
// second solution, 2 seconds
public List<String> findPrices(String product) {
    Function<>

    List<CompletableFuture<String>> futures =  shops.stream()
      .map(shop -> CompletableFuture.supplyAsync( () -> shop.getName() + ": " + shop.getPrice(product)))
      .collect(toList());

// wait for all strings in the list to be generated before confirming we have a result
    return futures.stream()
      .map(CompletableFuture::join)
      .collect(toList());
}
```
> Yes, this implementation took longer than the parallel one! We will see why

- From our examples it seems that the parallel stream method was actually faster than the asyncronous method. Which is faster actually depends on many factors, namely the number of cores or threads available to us and the number of shops we are querying.

- If the number of shops to query begins the outstrip the number of cores on our machine, the asyncronous futures method will prevail for two reasons: 1. by default the parallel streams method only allocates one thread per core on your cpu. 2. we can actually configure the internal thread pool used by the CompletableFuture.supplyAsync() method by including an Executor as a second arg.

- This provides for the best scalability as we can directly control the number of threads our program uses to query the list of shops

```Java
private static final int MAX_THREADS = 100;
private static final int DESIRED_THREADS = shops.size();

private final Executor pool = Executors.newFixedThreadPool(Math.min(DESIRED_THREADS, MAX_THREADS))
```