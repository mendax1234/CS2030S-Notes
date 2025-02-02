# Lec  03 - Polymorphism

## Overloading

_Method overloading_ is when we have two or more methods:

1. in the same class
2. with the **same method name** but a **different method signature**.

In other words, we create an overloaded method by changing the **type,** [**order**](#user-content-fn-1)[^1]**, and numbe**r of parameters of the method but keeping the method name identical.

### Overloading vs. [Overriding](../lec-02-class-instance-methods-inheritance/#overriding)

| Override | **must** have same [_method descriptor_](../lec-02-class-instance-methods-inheritance/#method-signature-and-descriptor) |
| -------- | ----------------------------------------------------------------------------------------------------------------------- |
| Overload | **must** have same method name, in the same class and **different** method signature.                                   |

{% hint style="info" %}
Note that in overloading methods, the **return type doesn't matter**. Sometimes, it will be useful to write overloading methods with different return types. See more in this [thread](https://edstem.org/us/courses/72783/discussion/6049016?comment=14044537).
{% endhint %}

## Method Invocation

"Thanks to" [polymorphism](./#polymorphism) and the [overriding](../lec-02-class-instance-methods-inheritance/#overriding), we may find it hard to determine which method we invocate will be **executed**. To make this problem clear, let's introduce the mechanism of _method invocation_ in Java.

Basically, the process can be divided into two parts:

1. [During Compile Time](./#during-compile-time)
2. [During Run Time](./#during-run-time)

### During Compile Time

**Goal**: To get the _method descriptor_ of the function we are going to invocate. Otherwise generate a compilation-error.

{% stepper %}
{% step %}
**Find the compile-time type target**

"Target" is an object. More specifically, with the invocation `curr.equals(obj)`, the "target" should be `curr`. Then the compile-time type of `curr`, let's denote it as $$C$$, is what we are interested in.

This compile-time type is actually a _class_, in which we need to **search for the methods** that have the **same name** with method name we want to invocate. In our case, we should find the method with name `equals()` in the class $$C$$.

{% hint style="info" %}
When doing the search, all the inherited methods from $$C$$'s supertypes/superclass are included in the search.
{% endhint %}
{% endstep %}

{% step %}
**Find the most specific method**

We may have several matching methods, and how do you decide which one's descriptor to get? Here, we use the **principle of "the most specific one"**.

Basically, we should first **pass in the** [**arguments from our invocated method**](#user-content-fn-2)[^2] **to the method we found during step 1.** If the arguments **cannot** be passed into the method we found, we move to the next available option. (If no more option is left, we will generate a compilation-error).

After trying all the options, if there is **only one matching method**, then we have found!

Otherwise, if there are **more than one methods** that satisfy our requirements, we can use the **principle of "the most specific one"** to return the method description of the **most specific one**.

> **The principle of "the most specific one"**: a method $$M$$ is more specific than method $$N$$ if the arguments to $$M$$ can be passed to $$N$$ without compilation error.

Basically, this means that the **type of the parameter in** $$M$$ is **subtype** of the **type of the parameter in** $$N$$. For example, `equals(Circle)` is **more specific than** `equals(Object)`.

{% hint style="info" %}
Note that after this step, if there are still more than one method that satisfy our requirements, than the compiler will **generate a compilation-error**.
{% endhint %}
{% endstep %}

{% step %}
**Pass the method description we found to run-time step**

As the name suggests, we will pass the **all the information** in the _method descriptor_ to the next step.

{% hint style="info" %}
Note that in this step, we don't need to return any information about the class since it is not included in [_method descriptor_](../lec-02-class-instance-methods-inheritance/#method-signature-and-descriptor)_._
{% endhint %}
{% endstep %}
{% endstepper %}

### During Run Time

**Goal:** Find the **only** method to execute.

{% stepper %}
{% step %}
**Retrieve the method descriptor**

As the name suggests, this step will retrieve the information of the _method descriptor_ that is passed from the [compile process](./#during-compile-time) above.

{% hint style="info" %}
The information from the _method descriptor_ we get during compile-time includes:

1. The compile-time type of the method parameters.
2. The return type of the method.
{% endhint %}
{% endstep %}

{% step %}
**Find the run-time type target**

Similar to the compile process, now we should find the **run-time type** of the target, let's say it's $$D$$ (If using the same example, $$D$$ should be the **subtype** of $$C$$. Otherwise, a compilation error will be generated because this is considered as a _narrowing type conversion without explicit casting_)

This **run-time type** information tells us the class we should start to search from. Basically, it is from this class all the way up to the root class `Object`.
{% endstep %}

{% step %}
**Find a matching method descriptor**

Using the **retrieved method descriptor**, now we should find a method from the **run-time class** all the way up to the root class `Object` which has the **same descriptor** with our **retrieved method descriptor.**

{% hint style="info" %}
The **first** matching method will be executed.
{% endhint %}
{% endstep %}
{% endstepper %}

***

Till now, we have seen that a variable `obj` with type[^3] `Object` can have many "similar"[^4] methods. In Java, which method is invoked is decided _during run-time_, depending on the run-time type of the `obj`. This is called _dynamic binding_ or _late binding_ or _dynamic dispatch_.

{% hint style="info" %}
Other programming like C may use a different mechanism called _early binding_, which basically means that which function to run will be decided during the compile-time.
{% endhint %}

### Class methods

The description above **only** applies to instance methods. Class methods, on the other hand, **do not support** _dynamic binding_. The method to invoke is resolved **statically** during compile time. The same process in [compile-time](./#during-compile-time) is taken, but the corresponding method implementation in class will always be executed during run-time, without considering the run-time type of the target.

## Polymorphism

Methods [_overriding_](../lec-02-class-instance-methods-inheritance/#overriding) enables _polymorphism_, which is the last pillar of OOP, and arguably the most powerful one. It basically states that we should use the base-type class as much as possible.

Since _polymorphism_ will [**dynamically decide**](#user-content-fn-5)[^5] which method implementation to execute during **run-time**, so that to change how our existing code behaves, we don't have to change a single line of our existing code. Instead, we can just create a new derived/sub "type[^6]" and then use _polymorphism_ to achieve what we want to achieve.

Let's use an example to have a glimpse of the power of _polymorphism_.

<pre class="language-java" data-line-numbers><code class="lang-java">boolean contains(Object[] array, Object obj) {
  for (Object curr : array) {
<strong>    if (curr.equals(obj)) {
</strong>      return true;
    }
  }
  return false;
}
</code></pre>

At Line 3, **depending on the run-time type** of `curr`, the corresponding, customized version of `equals` is called to compare against `obj`. So if the run-time type of `curr` is `Circle`, then we will invoke `Circle::equals(Object)` and if the run-time type of `curr` is `Point`, then we will invoke `Point::equals(Object)`. This, of course, assumes that `Object::equals(Object)` is overridden in both classes.

## Liskov Substitution Principle

**Goal**: To provide a way to decide when we misuse of _overriding_ and _inheritance_ (a.k.a _polymorphism_).

**Soul/Main content**: A _subclass **should not**_ break the expectations set by the _superclass_. In other words, the test cases that are passed in _superclass_ should also be **passed** in the _subclass_.

{% hint style="info" %}
The LSP (**L**iskov **S**ubstitution **P**rinciple) is a formal way of speaking [_subtype_](../lec-01-compiler-types-classes-objects/#subtypes)_._
{% endhint %}

### Pure substitution vs. Impure substitution

_Pure substitution_ can be thought of as _inheritance_ should override _only_ parent-class methods (and **not** add new methods that aren't in the parent class). In this case, the relationship between the derived-class and the base-class (a.k.a parent-class) can be viewed as a "**is-a**" relationship.

{% hint style="info" %}
Even if we only override the parent-class methods, that doesn't mean **we are 100% sure** that the overriden method conforms to the LSP. An example is in the [lecture notes](https://nus-cs2030s.github.io/2425-s2/16-lsp.html#lsp-through-the-lens-of-testing) about `Restaurant` and `LunchRestaurant`.
{% endhint %}

_Pure substitution_ is the **ideal way** to treat _inheritance_. However, there are times we may need to add new method elements to the derived-class. In this case, the relationship becomes "**is-like-a**" relationship and it is known as _impure substitution_.

### The `final` keyword

The `final` keyword can help prevent **a class to be inherited from** and **a method to be overriden**. So, till now, the use of `final` keyword is as follows:

1. In a **field declaration** to prevent **re-assignment.**
2. In a **class declaration** to prevent **inheritance**.
3. In a **method declartion** to prevent **overriding**.

## Abstract class

**Goal**: To fully take the advantage of _polymorphism_, we want to make our method (a.k.a a kind of [_abstraction_](../../../#four-pillars-of-oop)) as **general** as possible.

One way to do so is to keep defining the object from the root class `Object`. For example, suppose we want to **generalise** `equals()` to check if two objects are equal or not (and extend it to all the other objects, like `Circle`, `Bicycle` etc), we can write the code as follows:

{% code lineNumbers="true" %}
```java
boolean contains(Object[] array, Object obj) {
  for (Object curr : array) {
    if (curr.equals(obj)) {
      return true;
    }
  }
  return false;
}
```
{% endcode %}

However, this style of code has a **prerequisite**: the `Object` class must have the method called `equals()`. So, what if we want to generalise other functions, like a function `getArea()` to get the area of a circle, or rectangle? Now the all-time `Object` method doesn't work because there is **no** such method called `getArea()` inside the root class `Object`!

So, seems that now we want to create something more **specific** than `Object` that supports the function we want, yet more **general** than `Circle` or `Rectangle`. Here it comes the other way —  The use of _abstract class_ and _abstract methods_.&#x20;

***

> An _abstract class_ in Java is a class that has been made into something so **general** that it **cannot and should not be instantiated**! Otherwise, errors[^7] will be generated!

In our example, we may want to create an _abstract class_ called `Shape`. To do so, we can use the keyword `abstract`.

### The `abstract` keyword

**Goal**: Basically, the `abstract` keyword is used to creat an _abstract class_ and an _abstract method_.

For example, we can implement our _abstract class_ `Shape` and the _abstract method_ `getArea()` as follows:

{% code lineNumbers="true" %}
```java
abstract class Shape {
  abstract public double getArea();
}
```
{% endcode %}

An _abstract method_ **cannot** be implemented and therefore **should not have** any method body.

***

{% hint style="info" %}
A class with **at least** one abstract method must be declared as `abstract`. Otherwise, an error[^8] will be generated. On the other hand, an abstract class may have **no** abstract method.
{% endhint %}

### Concrete class

We call a class that is **not** abstract as a _concrete class_. A concrete class **cannot** have any abstract method. Thus, **any** concrete subclass of `Shape` **must override** `getArea()` to supply its own implementation.

## Interface

**Goal**: To make a method even more **generalisable**!

> The abstraction that _models what an entity can do_ is called **interface**.

An _interface_ is also a **type** and is declared with the keyword `interface`. Since an interface models what an entity can do, the name usually ends with the "**-able**" suffix.

For example, let's make the `getArea()` even more generalisable so that it can not only get the area of a shape, but also the area of a real estate property, etc

{% code lineNumbers="true" %}
```java
interface GetAreable {
  double getArea();
}
```
{% endcode %}

{% hint style="info" %}
All methods declared in an interface are `public abstract` by default, so we can omit these two keywords.
{% endhint %}

Now, for every class that we wish to be able call `getArea()` on, we tell Java that the class `implements` that particular interface.

{% stepper %}
{% step %}
**For an abstract class**

For example, we want our abstract class `Shape` to be able to call `getArea`, we can do as follows:

{% code lineNumbers="true" %}
```java
abstract class Shape implements GetAreable {
    // ...
}
```
{% endcode %}
{% endstep %}

{% step %}
**For an concrete class**

For a concrete class, it can also implement the interface. For example,

{% code lineNumbers="true" %}
```java
class Flat extends RealEstate implements GetAreable {
  private int numOfRooms;
  private String block;
  private String street;
  private int floor;
  private int unit;

  @Override
  public double getArea() {
      :
  }
}
```
{% endcode %}

{% hint style="info" %}
In a concrete class, for it to implement an interface, it has to override **all** abstract methods from the interface and provide implementation to each. Otherwise, the class becomes `abstract`.
{% endhint %}
{% endstep %}
{% endstepper %}

### Interface as Supertype

If a class $$C$$ implements an interface $$I$$, then $$C<:I$$. This definition implies that a type can have multiple supertypes because a type can implement multiple interfaces.

{% hint style="info" %}
So far, we have seen two ways to establish the **subtype relationship** between classes and interfaces:

1. base on _inheritance_ and use the keyword `extends`
2. base on _interface_ and use the keyword `implements`
{% endhint %}

#### Type Casting using an interface

{% hint style="warning" %}
This is a knowledge point that almost all students in the past cohort fail, so it must be very valuable! See more [here](https://edstem.org/us/courses/72783/discussion/6079061?answer=14080449).
{% endhint %}

As we have seen in the previous lecture, in Java, two types without a _subtype_ relationship [cannot be casted](#user-content-fn-9)[^9]. However, let's consider the code as follows,

<pre class="language-java" data-line-numbers><code class="lang-java">interface I {
  :
}

class A {
  :
}

class B implements I {
  :
}

I i1 = new B(); // Compiles, widening type conversion
<strong>I i2 = (I) new A(); // Also compiles?
</strong></code></pre>

Will the Line 14 compile? The answer is **yes**. The compiling principle for the Java compiler is that:

> it does not let us cast when it is **provable** that it will not work. i.e. casting between two classes that have no subtype relationship. However, for `interface`, there is the _possibility_ that a subclass _could_ implement the interface. Therefore, the Java compiler trusts that the programmer knows what they are doing, and allows it to compile.

So, in our example, one such possibility is as follows,

{% code lineNumbers="true" %}
```java
class AI extends A implements I{
  :
}
```
{% endcode %}

Here, the subtype relationship we can get is $$AI<:I$$ and $$AI<:A$$. This **cannot** tell us the $$A$$ is the subtype of $$I$$. And based **solely** on this code, it **can compile** but it will generate a **run-time error**.

So, given that we are pretty sure that $$A$$ is **not** the **subtype** of $$I$$, why this code still compiles? That is because **the compiler doesn't know whether there is a possibility that** $$A$$ **has a subclass that&#x20;**_**implements**_ $$I$$. If so, the code will run perfectly without any **compile-time error** and **run-time error**.

For example, let's assume class $$C$$ extends from $$A$$ and implements $$I$$. Now, we run the following code

{% code lineNumbers="true" %}
```javascript
A a = new C(); // Compiles because C is subtype of A
I i = (I) a; // Should work! Because Run time type of a is C!
```
{% endcode %}

Type casting can work in this case because even though compile-time type of `a` is `A` , its actual run-time type is `C` , which implements `I` . Since compiler **can't foresee** what is the actual run-time type of `a` , it would just be lenient and allow your code to compile.

***

So, coming back to our problem about this line of code `I i2 = (I) new A();`. Since the compiler has no idea about what kinds of subclasses `A` has, so even if class `C` does not exist in the code. The line `I i2 = (I) new A();` would still compile.

However, if you actually declare class `A` with `final` keyword which prevents it from being extended with any subclasses. Compiler can be 100% sure that class `A` cannot hold any instance whose type is a subtype of `I` in any situation and simply give you compile-time error.

## Tips

1. The essence of the original `equals` method in `Object` is that **it will compare whether** two objects are **referenced to the same memory address or not.**
2. The use of `instanceOf` operator: for example, `obj instanceOf Circle` will check if the **run-time type** of `obj` is a **subtype** of `Circle`. (Note that it is the **run-time** of `obj`)
3. In polymorphism, remember to do explicit **type casting** since sometimes the object from the root class `Object` **does not have** the fields we want.
4. Override the functions that **should not** be overriden will generate a compilation error.
5. Include the behavior of the compiler regarding the typecasting containing `Interface` into cheatsheet!

[^1]: "order" here means the **type** order! For example, changing from `double a, double b` to `double b, double a` is **not** considered as changing the order of the parameters.

[^2]: The arguments should be **compile-time type** since during compile-time, we have no idea about the run-time.

[^3]: "type" is the synonym of "class" in Java

[^4]: I call them "similar" because they look very alike.

[^5]: This is the _dynamic binding_ we have introduced above.

[^6]: if you are not familiar with the idea that "type" is the synonym of "class", just take every type I have mentioned in this document as "class".

[^7]: which kind of error? Ask

[^8]: which kind of error? Ask

[^9]: Both explicitly and implicitly are not allowed.
