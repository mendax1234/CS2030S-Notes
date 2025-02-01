# Lec 04 - Exception and Wrapper Classes

## Wrapper Class

In the previous [lec](lec-03-polymorphism/), we have introduced how to write general code on **reference type** by using _polymorphism_. Then, how about the **primitive type**? How do we write general code for the primitive type (a.k.a make primitive types less primitive)? Here it comes — The Wrapper class.

***

A _wrapper class_ is a class that encapsulates a _type_. For example, the wrapper class for `int` is called `Integer`, for `double` is called `Double`, etc. The table for the wrapper class for all primitive types in Java is summarised as follows:

| Primitive | Wrapper     |
| --------- | ----------- |
| `byte`    | `Byte`      |
| `short`   | `Short`     |
| `int`     | `Integer`   |
| `long`    | `Long`      |
| `float`   | `Float`     |
| `double`  | `Double`    |
| `char`    | `Character` |
| `boolean` | `Boolean`   |

In particular, the wrapper class is considered as _reference type_ and their instances can be created with `new` and stored on the heap.

{% hint style="info" %}
All primitive wrapper class objects are _immutable —_ once you create an object, it cannot be changed.
{% endhint %}

### Conversion between primitive types and its wrapper class

This is known as _auto-boxing_ and _unboxing._

{% stepper %}
{% step %}
**Auto-boxing**

This is used when you convert a primitive type to its wrapper class.

{% code lineNumbers="true" %}
```java
Integer i = 4;
```
{% endcode %}

Here, the primitive value of `int` 4 is converted into an _instance_ of `Integer`.
{% endstep %}

{% step %}
**Unboxing**

This is used when you convert an _instance_ of a wrapper class to its primitive type.

<pre class="language-java" data-line-numbers><code class="lang-java">Integer i = 4;
<strong>int j = i;
</strong></code></pre>

Line 2 converts an instance of `Integer` back to `int`.
{% endstep %}
{% endstepper %}

{% hint style="info" %}
Both auto-boxing and unboxing are implemented automatically.
{% endhint %}

### Performance

For the sake of general code and polymorphism, why don't we use _wrapper class_ all the time? The reason is **performance**. Using primitive types is **faster** than using its _wrapper class_ since the latter needs to instantiate an object every time it is being used.

## Run-Time Class Mismatch

The main point of this part is about **when explicit casting will be successful**?

***

**Explicit casting** is usually done during the _narrowing type conversion_ process, which is happened during the **run-time**, and as the name suggests, the type must be "narrowed down". So, for an **explicit casting** to be successful, the two types must have a **subtype relationship**.

### Run-Time error or compile-time error?

When doing type casting, if the **compile-time type** of the two objects have **no** subtype relationship, then a **compile-time error** will be generated.

On the other hand, a **run-time error** will be generated when there is **no** subtype relationship between the **run-time** **type** of the two objects you are operating on.

## Variance

Till now, we have seen the **subtype relationship** on classes and interefaces, this is trivial. However, how about the _complex types_ such as arrays? Seems that the subtype relationship is not that trivial. So, here it comes — the principle of _variance of types_:

> The _variance of types_ refers to how the **subtype relationship** between _complex types_ relates to the subtype relationship between [_components_](#user-content-fn-1)[^1].

### Variance of types

Let $$C(S)$$ correspond to some **complex type** based on type $$S$$, which means $$C(S)$$ is the type of the array and $$S$$ is the type of each element/component in the array. We say a complex type is:

* _covariant:_ which means if $$S<:T$$, then $$C(S)<:C(T)$$
* _contravariant:_ which means if $$S<:T$$, then $$C(T)<:C(S)$$
* _invariant_: if it is neither covariant nor contravariant

{% hint style="info" %}
Java Array is **covariant**.
{% endhint %}



[^1]: or "elements" if you are from CS1010, which uses C as the teaching language.
