# Lec 07 - Immutability, Nested Classes, Lambdas

So far in this course, we have been focusing on **three ways of dealing with software complexity**:

1. by **encapsulating** and hiding the complexity behind abstraction barriers,&#x20;
2. using a language with a **strong type system** and adhering to the **subtyping substitution principle**,
3. and applying the **abstraction principles** and reusing code written as functions, classes, and generics types.

> This summarizes the Lec 01 - Lec 06 so perfectly!

## Immutability

> The **fourth way** of dealing with software complexity is to **make our classes** _immutable_.

An **immutable class** means that: the **internal fields** of the **same instance** from this immutable class **cannot be changed outside its abstraction**. (a.k.a If you do make a change, then likely you will get a new instance and it is not the same instance as before anymore).

### Write Immutable Class

Basically, there are two steps we should follow to make a class **immutable**

{% stepper %}
{% step %}
**Add** `final` **to class declaration to prevent inheritance**

This is to prevent subclass of **immutable** class to override some methods to make the internal field of the immutable class mutable.
{% endstep %}

{% step %}
**Make the class's internal fields immutable**

This can be done by many ways:

1. For fields of primitive types, simply declare them as `final`. For other fields, make sure they are of **immutable type**! (a.k.a we can then create other larger immutable classes by **only** using **immutable classes as fields**.)
2. Have a class that prevents any and all kinds of sharing by **copying all the parameters before assigning them to the fields** and **copying all return values**. (See [#another-way-to-make-internal-fields-immutable](lec-07-immutability-nested-classes-lambdas.md#another-way-to-make-internal-fields-immutable "mention"))
{% endstep %}
{% endstepper %}

For example, the following are two good examples of the immutable `Point` and `Circle` class.

{% code title="Point.java" lineNumbers="true" %}
```java
final class Point {
  private final double x;
  private final double y;

  public Point(double x, double y) {
    this.x = x;
    this.y = y;
  }

  public Point moveTo(double x, double y) {
    return new Point(x, y);
  }
    :

  @Override
  public String toString() {
    return "(" + this.x + "," + this.y + ")";
  }
}
```
{% endcode %}

We start by making the fields `final` to signal our intention that we do not intend to _assign_ another value to them. Now that the `x` and `y` cannot be re-assigned (a new value or even the same value), to move a point, we shouldn't re-assign to the fields `x` and `y` anymore. Instead, we **return a new** `Point` **instance** to prevent mutating the current instance.

{% hint style="info" %}
By returning a new instance is a **recommended** and **useful** practice in writing immutable class!
{% endhint %}

{% code title="Circle.java" lineNumbers="true" %}
```java
final class Circle {
  private final Point c;
  private final double r;

  public Circle (Point c, double r) {
    this.c = c;
    this.r = r;
  }
    :

  public Circle moveTo(double x, double y) {
    return new Circle(c.moveTo(x, y), r);
  }
}
```
{% endcode %}

Similarly, we can write our immutable class `Circle`.

#### Another way to make internal fields immutable

In fact, there is another way to make the internal fields of an immutable class **immutable**. That is,

> to have a class that prevents any and all kinds of sharing by **copying all the parameters before assigning them to the fields** and **copying all return values**.

For example, we can rewrite our immutable `Circle` class as follows:

{% code title="Circle2.java" lineNumbers="true" %}
```java
final class Circle {
  private Point c;
  private double r;

  public Circle (Point c, double r) {
    this.c = c.clone();
    this.r = r; // primitive, no need cloning
  }
    :

  public Point getCenter() {
    return this.c.clone();
  }

  public Circle moveTo(double x, double y) {
    return new Circle(c.moveTo(x, y), r);
  }
}
```
{% endcode %}

Here, the `.clone()` is used to make a copy of the value of internal fields.

{% hint style="warning" %}
Note that we assume that `Point` is **immutable**!
{% endhint %}

**Takeaway**

1. That is **not** to say that the `final` keyword is **not important**. It helps accidental re-assignment and in some cases, **that is sufficient especially if the fields are of primitive type**.
2. Once we have created one immutable class, we can then create other larger immutable classes by **only** using **immutable classes as fields**.

### Advantages of Immutable Class

#### Ease of Understanding <a href="#ease-of-understanding" id="ease-of-understanding"></a>

Code written with immutable objects is easier to reason with and easier to understand because once we create an instance from an immutable class, it's internal fields will never change unless we have explicitly re-assigned it.

#### Enabling Safe Sharing of Objects <a href="#enabling-safe-sharing-of-objects" id="enabling-safe-sharing-of-objects"></a>

This means we can now safely create a **class field** to represent an **immutable property**. For example, if our `Point` class is immutable, we can create a **class field** called `ORIGIN` to always represent the point `(0, 0)`.

#### Enabling Safe Sharing of Internals <a href="#enabling-safe-sharing-of-internals" id="enabling-safe-sharing-of-internals"></a>

This advantage also utilizes the fact the the internal fields of an instance from an immutable class are immutable. See more from the [lecture notes](https://nus-cs2030s.github.io/2425-s2/28-immutability.html#enabling-safe-sharing-of-internals).

#### Enabling Safe Concurrent Execution <a href="#enabling-safe-concurrent-execution" id="enabling-safe-concurrent-execution"></a>

This won't be discussed for now. But imagine having code where we have to ensure its correctness regardless of how the execution interleaves! That's interesting right?
