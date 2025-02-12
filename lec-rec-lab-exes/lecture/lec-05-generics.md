# Lec 05 - Generics

## Generic Types

The motivations, (a.k.a benefits) of using _generics_ are:

1. enable you to detect errors at compile time rather than at runtime.
2. make your code more generalizable.

_Generics_ let yoru parameterize types. With this capability, you can define a class or a method with generic types that the compiler can replace with concrete types.

Here, to make it simple, let's use the _type parameter - type argument_ notation. Here the parameter and argument are the same one we use in the method. So, for example

{% code lineNumbers="true" %}
```java
class A<T> { }

A<Integer> a = new A<Integer>();
```
{% endcode %}

* `A` or `A<T>` is a _generic type_ with `T` as the _type parameter_.
* `A<Integer>` is a _parameterized type_ with `Integer` as the _type argument_ for `T`.

{% hint style="info" %}
Here, you may get a bit confused, but actually we are using the knowledge that in Java, **type** is the same as **class**!
{% endhint %}

### Generic Class / Interface

> Use our tradition, this generic class / interface can be called _generic type_ as well.

A _generic type parameter_ can be defined for a class or interface.

#### Generic Type Declaration

To declare a generic type, you can just put the type parameters inside the `<` and `>` after the class/type name. For example,

{% code lineNumbers="true" %}
```java
class Pair<S,T> {
  private S first;
  private T second;

  public Pair(S first, T second) {
    this.first = first;
    this.second = second;
  }

  public S getFirst() {
    return this.first;
  }

  public T getSecond() {
    return this.second;
  }
}
```
{% endcode %}

Here, `<S>` and `<T>` represents the _formal generic type parameter_. And `Pair<S,T>` is a _generic type_.

#### Instantiate a Generic Type

To use a generic type, we have to pass in _type arguments_, which itself can be

{% stepper %}
{% step %}
**A non-generic type**

An example is to pass the reference type as the type argument.

{% code lineNumbers="true" %}
```javascript
Pair<String,Integer> pair = new Pair<String,Integer>("hello", 4);
```
{% endcode %}

{% hint style="info" %}
Type arguments must be _reference type_. You cannot replace a type parameter with a primitive type, like `int`, `double`, etc.
{% endhint %}
{% endstep %}

{% step %}
**A generic type**

```java
class Pair<S,T> implements Comparable<Pair<S,T>>
```

For the explanation about this code, please go to [#an-interesting-example](lec-05-generics.md#an-interesting-example "mention") since it may invole some knowledge in the later parts of this lecture.
{% endstep %}

{% step %}
**Another type parameter that has been declared.**

```java
class DictEntry<T> extends Pair<String,T> {
    :
}
```

Here, we define a generic class called `DictEntry<T>` with a single type parameter `T`, that extends from `Pair<String,T>`. The type parameter `<T>` from `DictEntry<T>` is passed as the type argument for `T` of `Pair<String, T>`
{% endstep %}
{% endstepper %}

Once a generic type is instantiated, it is called a _parameterized type_.

#### Inheritance with Generics

You can define a class or an interface as a subtype of a generic class or interface. For example,

```java
public class String implements Comparable<String>;
```

{% hint style="info" %}
Notice that when you implement an interface or extend from a class, you are actually instantiating the generic type, so the three rules above must be followed!
{% endhint %}

### Generic Methods

Similary, a _generic type parameter_ can be defined for a **static** method.

#### Declare a generic method

To declare a generic method, you should put the _generic type parameter_ immediately after the keyword `static` and before the return type. For example,

```java
class A {
  public static <T> boolean contains(T[] array, T obj) {
    for (T curr : array) {
      if (curr.equals(obj)) {
        return true;
      }
    }
    return false;
  }
}
```

#### Invoke a generic method

To invoke a generic method, you can **prefix** the method name with the actual type in angle brackets `<>`.

```java
String[] strArray = new String[] { "hello", "world" };
A.<String>contains(strArray, 123); // type mismatch error
```

#### Bounded Generic Type Parameter

A _generic type parameter_ can be specified as a **subtype** of another type. Such a _generic type parameter_ is called _bounded_.

**Motivation**: Since during the compile time, generic type parameter have no method associated with it! So, to enabling us to call the methods associating with our generic type parameter, we can used _bounded type parameters_!

For example, our `getArea()` can be generalized using the generics as follows

{% code lineNumbers="true" %}
```java
class A {
  public static <T extends GetAreable> T findLargest(T[] array) {
    double maxArea = 0;
    T maxObj = null;
    for (T curr : array) {
      double area = curr.getArea();
      if (area > maxArea) {
        maxArea = area;
        maxObj = curr;
      }
    }
    return maxObj;
  }
}
```
{% endcode %}

We use the keyword `extends` here to indicate that `T` must be a subtype of `GetAreable`. It is unfortunate that Java decides to use the term `extends` for any type of subtyping when declaring a bounded type parameter, even if the supertype (such as `GetAreable`) is an interface.

***

### An interesting example

Let's say we want to compare two `Pair` instances, by comparing the first element in the pair, we can define our class as follows:

{% code lineNumbers="true" %}
```java
class Pair<S extends Comparable<S>,T> implements Comparable<Pair<S,T>> {
    :
}
```
{% endcode %}

* We declared `Pair` to be a generic type of two type parameters: the first one `S` is bounded and must be a subtype of `Comparable<S>`. This bound is self-referential, but it is intuitive — we say that `S` must be comparable to itself, which is common in many use cases.
* Since we want to compare two `Pair` instances, we make `Pair` implement the `Comparable` interface too, passing in `Pair<S,T>` as the type argument to `Comparable`. (So, actually here the type arguement is a generic type, which serves as a good example for our second case)

Antother interesting way to think about / know what happens with the _generic type parameter_ is: that

{% stepper %}
{% step %}
**Declaration**

This is to take the first appearance of the _generic type parameter_ as the **declaration**. For example,  in the above generic type, we are **declaring/defining** two type parameters `S, T`, where `S extends Comparable<S>` (which basically means type `S` can be compared to itself).
{% endstep %}

{% step %}
**Usage**

Then we in our class or method parameters, we can use the type we have declared/defined at Step 1.
{% endstep %}
{% endstepper %}

## Type Erasure

### Implementing Generics

Different languages implement the Generics differently. Basically, we have the following two methods:

1. **Code specialization**: it means that instantiating the generic types, like `Pair<String, Integer>` causes new code to be generated during **compile-time**. C++ and Rust use this method.
2. **Code sharing**: it means that instead of creating a new type for every instantiation, it chooses to _erase_ the type parameters and type arguments during **compilation** (after type checking, of course). Thus, there is only one representation of the generic type in the generated code, representing all the instantiated generic types, regardless of the type arguments. Java uses this method.

Part of the reason that Java uses **code sharing** is because of the backward compatibility since before Java 5, java uses `Object` to implement classes that are general enough to work on multiple types.

### Type Erause process in Java

{% stepper %}
{% step %}
**Replace the type parameters**

The non-bounded type parameters are replaced with `Object`. The bounded type parameters are replaced by the bounds instead. (e.g., If `T extends GetAreable`, then `T` is replaced with `GetAreable`.)

{% hint style="info" %}
This step is done implicitly.
{% endhint %}
{% endstep %}

{% step %}
**Do the necessary type casting**

This is done after the compiler has done the type checking
{% endstep %}
{% endstepper %}

For example, in the following code where a generic type is instantiated and used, the code

```java
Integer i = new Pair<String,Integer>("hello", 4).getSecond();
```

is transformed into,

```java
Integer i = (Integer) new Pair("hello", 4).getSecond();
```

### Some dangers of Type Erasure

One big danger of type erasure is the **heap pollution**, this is also caused because generics and arrays can't mix. For example,

```java
// create a new array of pairs
Pair<String,Integer>[] pairArray = new Pair<String,Integer>[2];

// pass around the array of pairs as an array of object
Object[] objArray = pairArray;

// put a pair into the array -- no ArrayStoreException!
objArray[0] = new Pair<Double,Boolean>(3.14, true);
```

After type erause, it will become

```java
// create a new array of pairs
Pair[] pairArray = new Pair[2];

// pass around the array of pairs as an array of object
Object[] objArray = pairArray;

// put a pair into the array -- no ArrayStoreException!
objArray[0] = new Pair(3.14, true);
```

Seems that this code will generate no compile-time error and run-time error! But you are storing a pair `<Double, boolean>` into the pair array of `<String, Integer>`!

But actually, the first code snippet **cannot compile** because generic array declaration is fine but generic array instantiation is **not**!
