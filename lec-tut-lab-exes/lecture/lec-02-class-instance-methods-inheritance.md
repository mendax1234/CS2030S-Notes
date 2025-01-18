# Lec 02 - Class Instance/Methods, Inheritance

## Information Hiding

### A Problem

In the last lecture, we have introduced the idea of [_abstraction barrier_](lec-01-compiler-types-classes-objects/#abstraction-barrier)_._ Above the abstraction barrier is the **client**, and as usual, the client shouldn't modify any content below the _abstraction barrier_ since it belongs to the **implementer** unless the implementer allows to do so.

For example, we have a `Circle` class, an object `c` initialized from this class, and the **client** wants to set the `r` (one of the fields) to a certain number, let's say 10. (`c.r = 10`). But now, the **implementer** says the `r` field in the class will be changed to `d` (diameter). Then, bad things happen since calling `c.r = 10` will generate a **compilation error!** To fix it, the client have change every `c.r` to `c.d` ! This is tedious!

This **problem** is caused exactly by the **client**'s breaking the _abstract barrier_ to access the field, which should belongs to the **implementer**.

### `public` and `private`

To solve this problem, many Object-oriented languages allow programmers to **explicitly** specify if a **field** or a **method** can be accessed from outside the abstraction barrier. Java, for instance, supports `private` and `public` access modifiers. Their difference are summarized in the table below

|   Accessed from   | private | public |
| :---------------: | :-----: | :----: |
|  Inside the class |   Yes   |   Yes  |
| Outside the class |    No   |   Yes  |

Such a mechanism to protect the abstraction barrier from being broken is called _data hiding_ or _information hiding_. This protection is enforced by the _compiler_ at compile time.

### A new problem?

But now, a new problem emerges. If we define all our fields in the class to be `private`, then how we can initialize them? :joy: To solve this problem, it is common for a class to provide methods to **initialise these internal fields**.

> A method that initializes an object is called a _**constructor**._

A constructor method is a special method within the class. It **cannot be called directly** but **is invoked automatically when an object is instantiated**. In Java, a constructor method _has **the same name as the class**_ and _has **no return type**_. A constructor can take in arguments just like other functions. Let's add a constructor to our `Circle` class:

{% code lineNumbers="true" %}
```java
class Circle {
  private double x;
  private double y;
  private double r;

  public Circle(double x, double y, double r) {
    this.x = x;
    this.y = y;
    this.r = r;
  }

  public double getArea() {
    return 3.141592653589793 * this.r * this.r;
  }
}
```
{% endcode %}

#### The `this` keyword

As you have noticed, the code above introduces the `this` keyword. `this` is a **reference variable** that [refers back to the **calling object itself**](#user-content-fn-1)[^1]. It can be used to distinguish between two variables of the same name. In the example above, `this.x = x` means we want to set the field `x` of this object to the parameter `x` passed into the constructor.

{% hint style="success" %}
Notice that in the `getArea()`, we have replaced `r` with `this.r`. Usually, it is a good practice to refer to fields through `this` because it differs the field from local variables or parameters, thus making our code easier to understand.
{% endhint %}

#### Default Constructor

If have class has **no** explicit constructor, then a default constructor will be added automatically at **compile time**. The default constructor has no parameter and has no code written for the body.

{% hint style="info" %}
Note that in a class, there may exist many constructors, but:

1. They have the same method name, which is the same as the class name
2. They have different number of parameters
{% endhint %}

[^1]: This is because, usually, we use `this` keyword inside the methods. And usually, methods are called using `object.method()`, so here "**the calling object itself"** refers to the "object" in front of the `.`.
