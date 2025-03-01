# Lec 05 - Generics

## Generic Types

The motivations, (a.k.a benefits) of using _generics_ are:

1. enable you to detect errors at compile time rather than at runtime.
2. make your code more generalizable.

_Generics_ let you parameterize[^1] types. With this capability, you can define a class or a method with generic types that the compiler can replace with **concrete types**.

Here, to make it simple, let's use the _type parameter - type argument_ notation. Here the parameter and argument are the same one as we use in the method. So, for example

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

> Using our tradition, this generic class / interface can be called _generic type_ as well.

A / Some _generic type parameter(s)_ can be defined for a class or interface, such type parameters are called **class-level** type parameters.

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

{% hint style="info" %}
The constructor for a generic type doesn't need `<>` operator!
{% endhint %}

#### Instantiate[^2] a Generic Type

To use a generic type, we have to pass in _type arguments_, which itself can be

{% stepper %}
{% step %}
**A non-generic type**

Here we want to instantiate the generic type `Pair<S,T>`, and we pass two **reference types** as the **type arguments**.

{% code lineNumbers="true" %}
```javascript
Pair<String,Integer> pair = new Pair<String,Integer>("hello", 4);
```
{% endcode %}

{% hint style="info" %}
In this way, the **type argument** must be _reference type_. You cannot use primitive types, like `int`, `double`, etc, as type arguments.
{% endhint %}

{% hint style="warning" %}
Using raw types (e.g., `new Pair()` without type arguments) can lead to **unchecked conversion** warnings. Always use the diamond operator `<>` (this will let the compiler do the type inference) or provide explicit type arguments inside the `<>` to ensure type safety.
{% endhint %}
{% endstep %}

{% step %}
**A generic type**

```java
class Pair<S,T> implements Comparable<Pair<S,T>>
```

Here the generic type that **needs** to be instantiated is `Comparable<T>`, and we are passing `Pair<S,T>` as the **type argument**.
{% endstep %}

{% step %}
**Another type parameter that has been declared.**

```java
class DictEntry<T> extends Pair<String,T> {
    :
}
```

Here the generic type that needs to be instantiated is `Pair<S,T>`, where&#x20;

* Its first type parameter `S`  is fixed as `String`.
* Its second type parameter `T` uses the first type parameter `T` in `DictEntry` as the **type argument**.
{% endstep %}
{% endstepper %}

Once a generic type is instantiated, it is called a _parameterized type_.

{% hint style="warning" %}
Always make sure which generic type is the one you want to instantiate!
{% endhint %}

### Generic Methods

Similary, a _generic type parameter_ can be defined for **any** method, and such type parameters are called **method-level** type parameters.

#### Declare a non-static generic method

{% code lineNumbers="true" %}
```java
class Box<T> {
  public T transform(T t) {
    return t;
  }
}
```
{% endcode %}

Here, the method `transform` uses the **class-level** type parameter `T`.

#### Invoke a non-static generic method

We can just use `instance.method()` to invoke a non-static generic method.

#### Declare a static generic method

To declare a static generic method, you should put the _type parameter_ immediately after the keyword `static` and before the return type. For example,

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

#### Invoke a static generic method

* Syntax for specifying a method's type parameter: `ClassName.<Type>method()`.
* Class type parameters (e.g., `<Integer>` in `A<Integer>`) are irrelevant for static methods and cannot be mixed with method type parameters in the call.

For example,

```java
String[] strArray = new String[] { "hello", "world" };
A.<String>contains(strArray, 123); // type mismatch error; compilation error
```

{% hint style="danger" %}
If you pass type argument to class `A`, e.g. `A<String>`, you will get a compilation error also!
{% endhint %}

#### Bounded Generic Type Parameter

A _generic type parameter_ can be specified as a **subtype** of another type. Such a _generic type parameter_ is called _bounded_.

**Motivation**: Since during the compile time, generic type parameter may not have the method that you want associated with it! So, to enable us to call the methods associated with our generic type parameter, we can used _bounded type parameters_! (**Jump to** [#type-erasure-process-in-java](./#type-erasure-process-in-java "mention") **if you want to understand it in advance)**

{% hint style="success" %}
"Bounded" used to mean **adding restrictions**, but here it actually serves to **add more usages!** Interesting!
{% endhint %}

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

We use the keyword `extends` here to indicate that `T` must be a **subtype** of `GetAreable`. It is unfortunate that Java decides to use the term `extends` for any type of subtyping when declaring a bounded type parameter, even if the supertype (such as `GetAreable`) is an interface.

{% hint style="info" %}
To speicify **more than one bound,** use `&` operator to link the **bounds**. But **the first bound** must be a **class**! It **cannot be** an `interface`, otherwise, a compile error will be generated.
{% endhint %}

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

Here, we have two `Comparable<T>` that needs to be instantiated, a.k.a, we want to make two types **comaprable**!

1. For the first type parameter `S` in `Pair<S,T>`
2. For the generic type `Pair<S,T>`

{% hint style="info" %}
A bound like `<S extends Comparable<S>>` is a common pattern called **self-referential bound.** It ensures a type can be compared to others of its own kind.
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
2. **Code sharing**: it means that instead of creating a new type for every instantiation, it chooses to _**erase**_**&#x20;the type parameters and type arguments** during **compilation** (after type checking, of course). Thus, there is only [**one representation of the generic type**](#user-content-fn-3)[^3] in the generated code, representing all the instantiated generic types, **regardless of the type arguments**. Java uses this method.

Part of the reason that Java uses **code sharing** is because of the backward compatibility since before Java 5, Java uses `Object` to implement classes that are general enough to work on multiple types.

### Type Erasure process in Java

Type erasure is a **compile-time** process that **removes generic type information** to ensure backward compatibility with legacy Java code that doesn’t use generics. And the whole process of type erasure can be divided into:

1. Type checking (Before type erasure)
2. Type erausre (During type erasure)

#### Type Checking

Java will do the type checking during the **compile time** to make sure that the code compile!&#x20;

{% hint style="success" %}
See more application from [Lab 03](../../lab/lab-03-exceptions-generics-exercises-2-3.md#classic-questions)!!! Must see!!!
{% endhint %}

#### Type Erasure

This happens in the **compile-time** also, after the **type checking**.

{% stepper %}
{% step %}
**Replace generic type with its rawtype**

The type parameters of the generic type will be discarded and replaced by its raw type during the type erasure. For example, `Pair<String, Integer>` will be erased to `Pair`.
{% endstep %}

{% step %}
**Replacing Type Parameters used in the generic type(Type erasure starts)**

* **Non-Bounded Type Parameters:** If a type parameter is not bounded (e.g., `<T>`), it is replaced with `Object`.
* **Bounded Type Parameters:** If a type parameter has an upper bound (e.g., `<T extends GetAreable>`), it is replaced with the first bound (in this case, `GetAreable`).

{% hint style="info" %}
If **multiple bounds** exist (e.g., `<T extends SomeClass & SomeInterface>`), `T` will be erased the first bound, and then it is **type casted** to the second bound. (You can find more in [Recitation 03](../../recitation/rec-03.md#id-02.-type-parameter-with-two-bounds))

**Note** that the first bound must be a **class**! It cannot be an interface, otherwise, a **compile error** will be generated!
{% endhint %}

{% hint style="success" %}
This step is done implicitly.
{% endhint %}
{% endstep %}

{% step %}
**Inserting Necessary Casts:**

* After replacing type parameters, the compiler inserts casts where needed. This ensures that **type checking** (done at **compile time**) is still enforced at **runtime**.
* For example, when retrieving an element from a generic collection, the compiler adds a **cast** to the expected type because, after erasure, the collection is treated as holding `Object` references (or the specific bound type).
{% endstep %}
{% endstepper %}

For example, in the following code where a generic type is instantiated and used, the code

```java
Integer i = new Pair<String,Integer>("hello", 4).getSecond();
```

is transformed into the following code **after type erasure**.

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

Seems that this code will generate **no compile-time error** and **run-time error**! But you are actually storing `Pair<Double, boolean>` into the `Pair` array of `<String, Integer>`!

But in fact, the first code snippet **cannot compile** because generic array **declaration is fine** but generic array **instantiation is** **not**!

## Unchecked Warnings

Basically, unchecked warnings will happen in the following **two** cases:

1. the **type casting** process when you create an array with type parameters. See [#create-arrays-with-type-parameters](./#create-arrays-with-type-parameters "mention")
2. **raw types** are used. (This actually will cause a `rawtype` warning instead of an `unchecked` warning). See [#raw-types](./#raw-types "mention")

### Generics are Invariant

In Java, generics are [**invariant**](../lec-04-exception-and-wrapper-classes/#variance-of-types)**.** This means there is **no subtype relationship** between two generic types. For example,

{% code lineNumbers="true" %}
```java
class A<T> {
    ...
}

// S <: T
// A<S> </: A<T>
```
{% endcode %}

Even if $$S$$ <: $$T$$, we **cannot** say `A<S>` <: `A<T>`.

### Create Arrays with Type parameters

As we have seen earlier, Java arrays and generics **cannot** mix together! This means that,

> we **cannot instantiate** a Java array using the type parameter, e.g. `new T[]` is **not** allowed. However, we **can declare** a Java array using the type parameter, e.g. `T[] a` is allowed.

So, how can we create arrays with type parameters? To get around with this, we should

{% stepper %}
{% step %}
**Determine the type** `Q` **of the type parameter after type erasure**

Let's define the type as `Q`. For example,

1. If the type parameter is **unbounded**, then `Q` will be `Object`
2. If the type parameter is **bounded**, e.g., `T extends Comparable<T>`, then `Q` will be the bound type, which is `Comparable` in our example.
{% endstep %}

{% step %}
**Create a Java array with type** `Q[]` **cast it to type** `T[]`

For example, we will have

{% code overflow="wrap" lineNumbers="true" %}
```java
class A<T> {
    public A(int size) {
        T[] a = (T[]) new Object[size]; // Demo only, not correct till this step
    }
}

class B<T extends Comparable<T>> {
    public B(int size) {
        T[] a = (T[]) new Comparable[size]; // Demo only, not correct till this step
    }
}
```
{% endcode %}

{% hint style="info" %}
Explicit casting is allowable here because during **compile time**, we are sure that `T` will be **subtype** of `Object` or `Comparable` in this example.
{% endhint %}
{% endstep %}

{% step %}
**Suppress the warning after we check that everything is okay manually**

Till now, we are still not done. We may still get a warning as follows,

```bash
Note: Seq.java uses unchecked or unsafe operations.
Note: Recompile with -Xlint:unchecked for details.
```

This is called an [**unchecked warning**](#user-content-fn-4)[^4]. And it is caused because the compiler doesn't know whether we can do the casting safely. A.k.a, we are not sure whether the all the elements in the Java array **have a subtype relationship with** `T`, thus an explicit casting maybe dangerous!

To suppress this warning, the first thing we need to do is

> be 100% sure that all the elements in your array are of type `T` or at least have subtype relationship with `T`.

Then ,we can use the code as follows to suppress the warning

```java
@SuppressWarnings("unchecked")
```

{% hint style="info" %}
Using `@SuppressWarnings` actually means that we are more sure than the compiler that there will be no error with this piece of code!
{% endhint %}

Now, our final code should look like as follows,

{% code overflow="wrap" lineNumbers="true" %}
```java
class A<T> {
    private T[] array;
    
    public A(int size) {
        @SuppressWarnings("unchecked")
        T[] a = (T[]) new Object[size]; // Demo only, not correct till this step
        this.array = a;
    }
}

class B<T extends Comparable<T>> {
    private T[] array;
    
    public B(int size) {
        @SuppressWarnings("unchecked")
        T[] a = (T[]) new Comparable[size]; // Demo only, not correct till this step
        this.array = a;
    }
}
```
{% endcode %}

{% hint style="info" %}
`@SuppressWarnings` cannot apply to an assignment but only to the declartion. That's why we must use it before Line 16 instead of before Line 17.
{% endhint %}
{% endstep %}
{% endstepper %}

### Raw Types

A _raw type_ is a generic type used **without type arguments**. For example, we have a `Seq<T>`,

```java
Seq s = new Seq(4); // Raw Type is used here!
```

The code **will compile**! But it's just that the compiler **cannot** help us to check the type-safety during the compile-time!

> Raw type should **never** be used in your code! But till now, we have a small exception.

For example, in the following code snippet, `new Comparable[size]` is actually a use of **raw types**.

{% code overflow="wrap" lineNumbers="true" %}
```java
class B<T extends Comparable<T>> {
    private T[] array;
    
    public B(int size) {
        @SuppressWarnings("unchecked")
        T[] a = (T[]) new Comparable[size]; // Demo only, not correct till this step
        this.array = a;
    }
}
```
{% endcode %}

In fact, merely doing so will **still give us a warning**! And that is a **rawtype warning** because Line 6 actually is using **raw type**! But since we are sure `T` will be replaced by `Comparable` after type erausre, let's just allow this kind of stuff first.

But to fully make this code warning-free. We need to suppress the rawtype warning. Thus, we need to modify our code as follows,

{% code overflow="wrap" lineNumbers="true" %}
```java
class B<T extends Comparable<T>> {
    private T[] array;
    
    public B(int size) {
        @SuppressWarnings({"rawtypes", "unchecked"})
        T[] a = (T[]) new Comparable[size]; // Demo only, not correct till this step
        this.array = a;
    }
}
```
{% endcode %}

## Classic Problems

1. There are some classic problems related to type erasure and generics covered during [#generics](../../lab/lab-03-exceptions-generics-exercises-2-3.md#generics "mention"). Remember to take a look before exams!

[^1]: sometimes it is called "instantiate"

[^2]: sometimes it is called "parameterize", they can be used interchangeably.

[^3]: **Soul** of Type Erasure in Java!

[^4]: An _unchecked warning_ is basically a message from the compiler that it has done what it can, but because of type erasures, there could be a run-time error that it cannot prevent.
