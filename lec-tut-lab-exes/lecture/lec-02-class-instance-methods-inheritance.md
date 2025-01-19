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

### Constructor

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

## Tell, Don't Ask

### Accessors and Mutators

Similar to providing [constructors](lec-02-class-instance-methods-inheritance.md#constructor), a class can also provide methods to **retrieve or modify** the properties of the object. These methods are called the _accessor_ (or _getter_) or _mutator_ (or _setter_).

For example, for our `Circle` class, we have the following _accessors_ and _mutators_.

| Fields | Accessors | Mutators |
| ------ | --------- | -------- |
| `x`    | `getX`    | `setX`   |
| `y`    | `getY`    | `setY`   |
| `r`    | `getR`    | `setR`   |

#### Advantage

The use of _accessors_ and _mutators_ has the following advantages over using the `public` keyword to make the field public directly. By having an accessor and a mutator:

{% stepper %}
{% step %}
We are **adding a layer of abstraction.** For instance, we can still rename a field without affecting the client.

**Why?** This is because we are calling the methods (a.k.a _accessors_ and _mutators_) to retrieve or modify the fields in the object, so as long as we don't change the method name, changing the **field name** merely in class won't affect the client's code.
{% endstep %}

{% step %}
We may be able to peform some **checks on the mutator.**

This is because sometimes user may set invalid or not-compatible values to the fields. However, by using a mutator, we can do some checks to prevent this kind of bug-prone behavior. For example,

{% code lineNumbers="true" %}
```java
public void setR(double r) {
  if (r > 0) {
    this.r = r;
  } else {
    // handle error
  }
}
```
{% endcode %}
{% endstep %}
{% endstepper %}

#### Disadvantage

However, the use of _accessors_ and _mutators_ is **not** an error-free solution. For example, when the client is calling an _accessor_, the client should know the type that will be returned, a.k.a, the type of the certain field in the class. However, the client should **not** know this kind of information since it belongs to the implementer! So, here comes the first problem:

> 1. Information Leak

The problem does not stop here. Let's say if the implementer wants to change the type of the field, which will also change the return type of the _accessors_, and the client doesn't know that! This is very dangerous because likely the code will generate a **compilation error**!

> 2. May genertae compilation error

For example, let's use the following code regarding our `Circle` class as an example:

{% code overflow="wrap" lineNumbers="true" %}
```java
int cX = c.getX(); // Suppose getX() will return an double, same for the following
int cY = c.getY();
int r = c.getR();
boolean isInCircle = ((x - cX) * (x - cX) + (y - cY) * (y - cY)) <= r * r;
```
{% endcode %}

As we have seen earlier in the [subtype](lec-01-compiler-types-classes-objects/#subtypes), `int` is the **subtype** of `double`. Assign a `double` to a `int` is considered as [_narrowing type conversion_](lec-01-compiler-types-classes-objects/#subtyping-between-java-primitive-types) and [it is **not allowed** without explicit casting](lec-01-compiler-types-classes-objects/diagnostic-quiz.md#id-15.-widening-narrowing-type-conversion)! So, here we will get a **compilation error!**

***

So, the question comes, when should we use _accessors_ and _mutators_?

### The "Tell, Don't Ask" Principle

One guiding principle to whether the implementer should provide and whether the client should call the _accessor_ and _mutator_ is the "**Tell, Don't Ask**" principle. This principle suggests that we should tell an object what to do, **instead of asking an object for its state and then performing the task on its behalf**.

For example, in the example above, what we are trying to do is as follows:

<figure><img src="../../.gitbook/assets/lec02-tell-don&#x27;t-ask-previous.png" alt="" width="375"><figcaption></figcaption></figure>

Applying the "Tell Don't Ask" principle, a better approach would be to add a new `boolean` method in the `Circle` class,

{% code overflow="wrap" lineNumbers="true" %}
```java
boolean contains(double x, double y) {
  return ((x - this.x) * (x - this.x) + (y - this.y) * (y - this.y)) <= this.r * this.r;
}
```
{% endcode %}

and let the client _tell_ the `Circle` object to check if the point is within the circle.

{% code lineNumbers="true" %}
```java
boolean isInCircle = c.contains(x, y);
```
{% endcode %}

<figure><img src="../../.gitbook/assets/lec02-tell-don&#x27;t-ask-current.png" alt="" width="363"><figcaption></figcaption></figure>

Now, the `Circle` class can change its internal structure (e.g., the type of the fields) without affecting the client.

{% hint style="info" %}
1. In general, _a task that is performed only on the fields of a class should be implemented in the class itself._
2. For beginner OO programmers, it is better to **not define classes with any accessor and modifier** to the `private` fields and force yourselves to think in the OO way — to **tell an object what task to perform as a client, and then implement this task within the class as a method as the implementer**.
{% endhint %}

## Class Field

In our `Circle` class, we have a constant $$\pi$$ and this constant $$\pi$$ is universal and **does not really belong to any object**. (The value of $$\pi$$ is same for every circle!) In C, we have the solution to define a global constant using the `define` keyword.

In Java, we can associate these _global_ values and functions with a _class_ instead of with an _object_. For instance. Java predefines a [`java.lang.Math`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/Math.html) class[1](https://nus-cs2030s.github.io/2425-s2/07-static-field.html#fn:1) that is populated with constants `PI` and `E` (for Euler's number $$e$$), along with a long list of mathematical functions.

To associate a method or a field with a class in Java, we declare them with the `static` keyword. We can additionally add the keyword `final` to indicate that the value of the field will not change and `public` to indicate that the field is accessible from outside the class. In short, the combination of `public static final` modifiers is used for constant values in Java.

{% code lineNumbers="true" %}
```java
class Math {
  :
  public static final double PI = 3.141592653589793;
  :
  :
}
```
{% endcode %}

We call these `static` fields that are associated with a **class** as _class fields_ and fields that are associated with an **object** as _instance fields_. Class fields are useful for storing pre-computed values or configuration parameters associated with a class rather than individual objects.

{% hint style="info" %}
A `static` class field needs not be `final` and it needs not be `public`.
{% endhint %}

### The `static` keyword

Because `static` field is associated with the class rather than an instance, we can think about `static` field as having **exactly one** instance during the entire execution of the program. In other words, there is only exactly one instance of `PI` regardless of how many instances of `Math` we have created. In fact, we need not create any instance of `Math` at all to be able to use `PI`.

### Access Class Fields

{% stepper %}
{% step %}
**Import the certain class**

For example, if we want to use the `PI` constant defined in class `java.lang.Math`, we should first use the `import` keyword to import the `java.lang.Math` class

{% code lineNumbers="true" %}
```java
import java.lang.Math;
```
{% endcode %}
{% endstep %}

{% step %}
**Use `.` operator to access the class field**

After importing the class, we can use `class.FIELD_NAME` to acccess the specific class field.

{% code lineNumbers="true" %}
```java
public double getArea() {
  return Math.PI * this.r * this.r;
}
```
{% endcode %}
{% endstep %}
{% endstepper %}

[^1]: This is because, usually, we use `this` keyword inside the methods. And usually, methods are called using `object.method()`, so here "**the calling object itself"** refers to the "object" in front of the `.`.
