# Lec 07 - Immutability and Nested Classes

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

{% hint style="info" %}
In Java, if a class is `final`, it **cannot** be **inherited**!
{% endhint %}
{% endstep %}

{% step %}
**Make the class's internal fields immutable**

This can be done by many ways:

1. For fields of primitive types, simply declare them as `final`. For other fields, make sure they are of **immutable type**! (a.k.a we can then create other larger immutable classes by **only** using **immutable classes as fields**.)
2. Have a class that prevents any and all kinds of sharing by **copying all the parameters before assigning them to the fields** and **copying all return values**. (See [#another-way-to-make-internal-fields-immutable](./#another-way-to-make-internal-fields-immutable "mention"))
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
Returning a new instance is a **recommended** and **useful** practice in writing immutable class!
{% endhint %}

Similarly, we can write our immutable class `Circle`.

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

Code written with immutable objects is easier to reason with and easier to understand because once we create an instance from an immutable class, its internal fields will never change unless we have explicitly re-assigned it.

#### Enabling Safe Sharing of Objects <a href="#enabling-safe-sharing-of-objects" id="enabling-safe-sharing-of-objects"></a>

This means we can now safely create a **class field** to represent an **immutable property**. For example, if our `Point` class is immutable, we can create a **class field** called `ORIGIN` to always represent the point `(0, 0)`.

#### Enabling Safe Sharing of Internals <a href="#enabling-safe-sharing-of-internals" id="enabling-safe-sharing-of-internals"></a>

This advantage also utilizes the fact the the internal fields of an instance from an immutable class are immutable. See more from the [lecture notes](https://nus-cs2030s.github.io/2425-s2/28-immutability.html#enabling-safe-sharing-of-internals).

#### Enabling Safe Concurrent Execution <a href="#enabling-safe-concurrent-execution" id="enabling-safe-concurrent-execution"></a>

This won't be discussed for now. But imagine having code where we have to ensure its correctness regardless of how the execution interleaves! That's interesting right?

## Nested Class

**Definition**: A _nested class_ is a class defined within another containing class. For example,

{% code lineNumbers="true" %}
```java
class A {
    private class B {
        :
    }
}
```
{% endcode %}

{% hint style="info" %}
class `A` is a containing class and class `B` is a nested class.
{% endhint %}

**Use**: It usage is to **encapsulate information within a container class**.

**Property**: A _nested class_ is a **field** of the containing class and can access **fields** and **methods** of the _container class_, including those declared as `private`.&#x20;

A _nested class_ can be classified into the following four types

1. Inner class
2. Static nested class
3. Local class
4. Anonymous class

### Inner class <a href="#static-nested-classes" id="static-nested-classes"></a>

It is a **non-static** _nested class_, thus it can access **all** fields and methods of the containing class.

{% hint style="success" %}
This means that when instantiating the inner class, you **need** an instance of the containing class and the position of `new` should be behind the `.` operator.
{% endhint %}

{% hint style="warning" %}
In the Inner class, we should be extremely careful with the use of `this`. For example, the following is wrong!

{% code lineNumbers="true" %}
```java
class A {
 private int x;

 private class B {
   void foo() {
     this.x = 1; // error
   }
 }
}
```
{% endcode %}
{% endhint %}

#### Qualified `this`

This is to resolve the issue above. A _qualified `this`_ reference is prefixed with the **containing class name**, to differentiate between the `this` of the inner class and the `this` of the containing class. In the example above, we can access `x` from `A` through the `A.this` reference.

{% code lineNumbers="true" %}
```java
class A {
  private int x;

  private class B {
    void foo() {
      A.this.x = 1; // ok
    }
  }
}
```
{% endcode %}

To create an instance of the inner class, we can see the following code

{% code lineNumbers="true" %}
```java
class A {
  public class B {
    public void buz() { 
    }
  }
  public static class C {
  }
  
  public static void main(String[] args) {
  A a = new A();
  A.B b = a.new B(); // new a.B() is not allowed!
}
```
{% endcode %}

### Static nested class

It is a **static** _nested class_, thus it is associated with the containing class, NOT an instance.

{% hint style="success" %}
This means when instantiating the static nested class, you don't need an instance of the containing class.
{% endhint %}

So, it can only access **static fields** and **static methods** of the containing class. If you want to access the fields of the containing class, you can do so by using object reference.

{% code lineNumbers="true" %}
```java
class B {
    static int x = 0;
    
    void foo() {
        A a = new A();
    }
    
    static class A {
        int y = 0;
    
        A() {
            this.y = B.x + 1;
        }
    }
    
    public static void main(String[] args) {
        A a = new A(); // B.A = new B.A() is allowed! B.new A() is not allowed
        System.out.println(String.format("A.y: %d", a.y));
    }
}
```
{% endcode %}

{% hint style="info" %}
1. When you instantiate a static nested class, you can ignore the containing class's instance.\
   `static` only restricts that the nested class is associated with the containing class **itself**, NOT with any instance.
2. But **inside the static nested class, if you want to access non-static (instance) members of the outer class, you need an explicit reference to an instance of the outer class**, because `this` in the nested class does not carry a reference to an enclosing `B` instance.
{% endhint %}

#### Hiding nested class

> The [notes](https://nus-cs2030s.github.io/2425-s2/29-nested-classes.html#hiding-nested-classes) is very detailed. Please go back and refer to it if needed.

To put it simply, the _nested class_ should be defined as `private` for the sake of not breaking the abstract barrier. This makes calling the nested class **explicitly not allowed**. However, calling a public method from the **containing class** that returns an **instance** of the `private` _nested class_ is still allowed. For example,

{% code lineNumbers="true" %}
```java
class A {
  private class B {
    public void buz() { 
    }
  }
  B foo() {
    return new B();
  }
}


A a = new A();
a.foo();         // return an instance of A.B is OK
A.B b = a.foo(); // still not allowed since the type A.B is private
a.foo().buz();  // error since `buz` is defined in a private nested class
```
{% endcode %}

{% hint style="info" %}
Since the type `A.B` is private to within `A`, we **cannot** call methods of `B` outside of `A` as well.
{% endhint %}

### Local class

It is a _nested class_ **declared within a function**, just like a local variable. The local class has access to all the local variables from **within** the method[^1] it is declared, as well as the fields of its **containing class**.

{% hint style="info" %}
The **declaration** and the **instantiation** of a local class is usually seperated.
{% endhint %}

#### Variable Capture

It is a behavior that the **local class** will capture the following variables

1. The **local variables** of the method where the local class comes from (including **only** the arguments **that the lambda uses**, see more in [Diagnostic Quiz Q13](diagnostic-quiz.md#id-13.-variable-capture))
2. The **instance** that invokes the method where the local class comes from. (See more in [Rec 05](../../recitation/rec-05.md#id-01.-stack-and-heap-with-nested-class))

#### Effectively `final`

Effectively `final` means that an **implicitly** `final` variable **cannot be re-assigned** after **they are captured**.

The use of this rule is because variable capture can sometimes be confusing, thus Java enforces a rule that **only** `final` **or effectively** `final` local variables can be **captured**. If the variables captured are neither `final` nor effectively `final`, then a **compile error** will be generated!

{% hint style="info" %}
1. Note, this "re-assignment" is usually associated with **primitive type**. If we use the reference type, we can **mutate** the value of the instance **instead of** re-assigning. Thus, the second one with mutation is **allowed** in Java.
2. The "effectively final" rule applies to _reading_ the variable’s value, not to enabling assignments. See more from [Diagnostic Quiz Q12](diagnostic-quiz.md#id-12.-inner-class-and-effectively-final).
{% endhint %}

### Anonymous Class

An **anonymous class** is one where you **declare** a **local class** and **instantiate** it in a single statement.

It has the following format: `new X (arguments) { body }`, where:

1. `X` is a class that the anonymous class **extends** or an interface that the anonymous class **implements**.
2. `arguments` are the arguments that you want to pass into the constructor of the anonymous class.
3. `body` is the body of the class as per normal, except that we **cannot** have a **constructor** for an anonymous class.

{% hint style="success" %}
Like a local class, an anonymous class captures the variables of the enclosing scope as well — the same rules to variable access as local classes apply.
{% endhint %}

[^1]: This method is called the "enclosing" method.
