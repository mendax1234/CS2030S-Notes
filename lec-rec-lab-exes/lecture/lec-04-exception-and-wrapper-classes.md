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
