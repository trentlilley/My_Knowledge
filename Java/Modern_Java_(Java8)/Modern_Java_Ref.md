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

# Functional Programming With Streams

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

- we can reduce this to 6 lines of code and remove the garbage variable by using stream methods.

- `.stream()` is first called on the collection we wish to perform queries on and it enables us to use the other methods.

- `.filter()` takes a lambda expression as an arg where the lambda arg `d` is a dish in menu. This line of code acts like a more concise for each loop with a nested condition.

- `.sorted()` works similarly to Collections.sort with a comparator passed into it. `Dish::getCalories` is the result of calling `.getCalories()` on a Dish object in menu. The dishes that passed the filter are now sorted by caloric value.

- `.map()` takes an argument. This arg defines the operation that should be performed on each item in the filtered, sorted list, in this case we want to get the name of each Dish in our filtered, sorted menu

- `.collect()` completes the query by placing all queried data into a list.

```Java
List<String> lowCalorieDishesName = menu.stream()
    .filter(d -> d.getCalories() < 400) // get dishes under 400 cals
    .sorted(comparing(Dish::getCalories)) // sort dishes by number of cals
    .map(Dish::getName) // get dish names
    .collect(toList()); // return names as list
```
- there are other methods in addition to the ones seen in the example above

- `.limit()` takes an integer as an argument. This places a limitation on the number of items output


<br><br/>

# Multi-threading