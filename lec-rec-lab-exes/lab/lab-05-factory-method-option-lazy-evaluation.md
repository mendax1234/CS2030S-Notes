# Lab 05 - Factory Method, Option, Lazy Evaluation

## Factory method

The motivation of using factory method is that

1. A **single** method (or a very few number of methods) that   &#x20;is (are) flexible enough to return us the desired instance   &#x20;based on the argument used.
2. A way to detect and handle invalid argument during object   &#x20;instantiation.

For example, below is an example about the **factory method** in `Task`&#x20;

{% code lineNumbers="true" %}
```java
abstract class Task {
    private int amount;

    public static Task of(int type, int amount) {
        switch (type) {
            case 0:
                return new DepositTask(amount);
            case 1:
                return new WithdrawalTask(amount);
            case 2:
                return new CurrencyExchangeTask(amount);
            case 3:
                return new TransferTask(amount);
            case 4:
                return new LoanTask(amount);
            default:
                throw new IllegalArgumentException("No such task");
        }
    }
}
```
{% endcode %}

Now the task creation logic has been **unified** to `Task.Of(type, amount)`.

### Singleton

**Singleton** is a class that has **at most one active instance**&#x20;at any point of time. There are three ways to handle the singleton instantiation:

1. **Destroy** or **deactivate** the current active instance by   &#x20;overwriting the static reference.
2. **Reset** the current active instance and discard the new   &#x20;instance.
3. **Block** the creation of new instances by failing silently.

The following code serves as an example for **singleton pattern**.

{% code lineNumbers="true" %}
```java
class A {
    private static A activeInstance = null;

    private A() {
    }

    public static A construct() {
        if (A.activeInstance == null) {
            A instance = new A();
            A.activeInstance = instance;
            return instance;
        }
        return A.activeInstance;
    }
}
```
{% endcode %}

### Caching

**Caching** is to store the **unique/special values** as **static fields** in the class. For the caching to work, the variable should hold a constant value (by adding `final` keyword).

For example, we want to cache the **special value** `ORIGIN` in a 2-D plane in our Factory method.

{% code lineNumbers="true" %}
```java
class Point2D {
    private double x;
    private double y;
    private static Point2D origin = new Point2D(0, 0);

    private Point2D(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public static Point2D at(double x, double y) {
        return x == 0 && y == 0 ? Point2D.origin : new Point2D(x, y);
    }
}
```
{% endcode %}

### Error-Handling

Throwing exceptions is not recommended, this is because:

1. We need to write lots of `try-catch` blocks if there are many potential errors.
2. Throwing exceptions is an overkill for Minor errors that can be   &#x20;easily mitigated.
3. Unhandled exceptions can crash the program, but we may want   &#x20;to salvage some partially correct output (example: you need to   &#x20;multiply one million pairs of $$n\times n$$ matrices and the 999999-th   &#x20;operation crashes your program :O).

Thus, the idea to handle errors without using exception it that: **overwritting the invalid data with harmless and useless values**. More specifically, in our factory method, we can deal with these by again returning instances with special values (**the cached values**).

For example,

{% code lineNumbers="true" %}
```java
class Circle {
    private Point2D c;
    private double r;

    private Circle(Point2D c, double r) {
        this.c = c;
        this.r = r;
    }

    public static Circle of(Point2D c, double r) {
        // Return a degenerate Circle if the arguments are invalid
        return (c == null || r < 0)
                ? new Circle(Point2D.at(0, 0), 0)
                : new Circle(c, r);
    }
}
```
{% endcode %}

## Exercise 5 Review

1. Choose the most flexible type to declare the special meaning variables. For example, `NONE` should be declared using unbounded wildcard.

### Maybe

What on earth is **Maybe**?

* **Context**: We wish to **search** for something **but we are not sure if it exists**.
* How to indicate that the item we are looking for does not exist?
  * "Classic" approach: set `foundItem = null`&#x20;
  * However, `null` has two **meanings**!
    * the target isntance **does not exist**, or
    * the target isntance **does exist** but has a default (undefined) value, e.g., in this case, `null` **is a valid value for an actual item**.
    * **Analogy**: $$\varnothing$$ — a set containing no elements; $$\{\varnothing\}$$ — a set containing $$\varnothing$$ as an element.
  * **Self-contradiction**: `null` can be used to indicate "not found", but what if we are really looking for `null`?
  * Here it comes! `Maybe<T>`: a wrapper for an item that may or may not exist.&#x20;
  * **Contradiction-free** now:
    * `None` — item not found;
    * `Some(null)` — target item `null` and **is found**
    * What if we are searching for `None`?
    * Return `None` if not found, `Some(None)` otherwise
  * In Java Standard, `Maybe<T>` is called `Optional<T>`.

#### map and flatMap

1. Draw the pipeline to help you visualize the boxing process.
2. To define the wildcard to the parameter, try to relax it!

## Lab Sheet

{% stepper %}
{% step %}
Q2

{% code lineNumbers="true" %}
```java
void bar(String s) {
  if (s != null) {
    System.out.println(s);
  }
}
```
{% endcode %}

After rewriting

{% code lineNumbers="true" %}
```java
void bar(String s) {
  Maybe.of(s).ifPresent(x -> System.out.println(x));
}
```
{% endcode %}
{% endstep %}

{% step %}
Q3

{% code lineNumbers="true" %}
```java
String foo(Maybe<Object> m) {
  if (m.equals(Maybe.none()) { 
    return "?";
  } 
  return String.valueOf(m.get());
}
```
{% endcode %}

The code after modifying is:

{% code lineNumbers="true" %}
```java
String foo(Maybe<Object> m) {
  return m.map(obj -> String.valueOf(obj)).orElse("?");
}
```
{% endcode %}
{% endstep %}

{% step %}
Q5

{% code lineNumbers="true" %}
```java
Maybe<String> foo(Maybe<String> s1, Maybe<String> s2) {
  if (s1.equals(Maybe.none()) || s2.equals(Maybe.none())) {
    return Maybe.none();
  }
  return Maybe.of(s1.get() + s2.get());
}
```
{% endcode %}

The code after modifying is:

{% code lineNumbers="true" %}
```java
Maybe<String> foo(Maybe<String> s1, Maybe<String> s2) {  
   return s1.flatMap(s -> s2.map(t -> s + t));
}
```
{% endcode %}
{% endstep %}
{% endstepper %}

### Tips

1. When doing `flatMap` and `map` questions, always think about the situations when your arguments are `Maybe.none()`, will your final return only `Maybe.NONE` as expected?
