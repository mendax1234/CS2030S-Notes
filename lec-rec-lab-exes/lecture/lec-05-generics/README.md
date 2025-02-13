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

A / Some _generic type parameter(s)_ can be defined for a class or interface.

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

{% hint style="warning" %}
Using raw types (e.g., `new Pair()` without type arguments) can lead to **unchecked conversion** warnings. Always use the diamond operator `<>` or provide explicit type arguments inside the `<>` to ensure type safety.
{% endhint %}
{% endstep %}

{% step %}
**A generic type**

```java
class Pair<S,T> implements Comparable<Pair<S,T>>
```

The class `Pair` takes two type parameters `S` and `T`. It implements the `Comparable` interface with the type argument `Pair<S, T>`. This means the `compareTo` method expects another `Pair` with the same type parameters.

For the explanation about this code, please go to [#an-interesting-example](./#an-interesting-example "mention") since it may invole some knowledge in the later parts of this lecture.
{% endstep %}

{% step %}
**Another type parameter that has been declared.**

```java
class DictEntry<T> extends Pair<String,T> {
    :
}
```

`DictEntry` is a generic class with one type parameter `T`. It extends `Pair<String, T>`, which means:

* The first type parameter `S` in `Pair` is fixed as `String`.
* The second type parameter `T` is passed along from `DictEntry`.

This design is useful when you want to create a specialized version of a generic class where one of the type parameters is fixed.
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

{% hint style="info" %}
A bound like `<S extends Comparable<S>>` is a common pattern called **self-referential boud.** It ensures a type can be compared to others of its own kind.
{% endhint %}

#### Declaration vs. Usage of Generic Parameters:

{% stepper %}
{% step %}
**Declaration**

The first appearance of the generic type parameters (`S` and `T`) in the class definition is where they are declared. In our example, this is in `<S extends Comparable<S>, T>`.
{% endstep %}

{% step %}
**Usage**

After declaring them, these type parameters can be used throughout the class. For instance, they are used in the type signature of the `Comparable` interface (`Comparable<Pair<S, T>>`), in method parameters, return types, or field declarations.
{% endstep %}
{% endstepper %}

## Type Erasure

### Implementing Generics

Different languages implement the Generics differently. Basically, we have the following two methods:

1. **Code specialization**: it means that instantiating the generic types, like `Pair<String, Integer>` causes new code to be generated during **compile-time**. C++ and Rust use this method.
2. **Code sharing**: it means that instead of creating a new type for every instantiation, it chooses to _erase_ the type parameters and type arguments during **compilation** (after type checking, of course). Thus, there is only one representation of the generic type in the generated code, representing all the instantiated generic types, regardless of the type arguments. Java uses this method.

Part of the reason that Java uses **code sharing** is because of the backward compatibility since before Java 5, java uses `Object` to implement classes that are general enough to work on multiple types.

### Type Erasure process in Java

Type erasure is a **compile-time** process that removes generic type information to ensure backward compatibility with legacy Java code that doesn’t use generics. Here's what happens during type erasure:

{% stepper %}
{% step %}
**Replacing Type Parameters:**

* **Non-Bounded Type Parameters:**\
  If a type parameter is not bounded (e.g., `<T>`), it is replaced with `Object`.
*   **Bounded Type Parameters:**\
    If a type parameter has an upper bound (e.g., `<T extends GetAreable>`), it is replaced with the first bound (in this case, `GetAreable`).

    > **Note:** If multiple bounds exist (e.g., `<T extends SomeClass & SomeInterface>`), only the first bound (which must be a class or `Object` if absent) is used during erasure.

{% hint style="info" %}
This step is done implicitly.
{% endhint %}
{% endstep %}

{% step %}
**Inserting Necessary Casts:**

* After replacing type parameters, the compiler inserts casts where needed. This ensures that **type checking** (done at **compile time**) is still enforced at **runtime**.
* For example, when retrieving an element from a generic collection, the compiler adds a cast to the expected type because, after erasure, the collection is treated as holding `Object` references (or the specific bound type).
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

One big danger of type erasure is the **heap pollution**, this is because generics and arrays can't mix. For example,

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

## Unchecked warnings

In Java, generics are [**invariant**](../lec-04-exception-and-wrapper-classes/#variance-of-types)**.** This means there is no subtype relationship between for example, `ArrayList<Object>` and `ArrayList<Pair<String, Integer>>`.

***

An _unchecked warning_ is basically a message from the compiler that it has done what it can, but because of type erasures, there could be a run-time error that it cannot prevent.
