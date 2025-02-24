# Rec 04

## Problems

### 01. Generic Type parameter and `static` field

As we have seen from [Lab 03](../lab/lab-03-exceptions-generics-exercises-2-3.md#scope-of-type-parameters),

> A class's generic type parameters **cannot be used** in `static` methods or `static` fields.

For more information, please go to [Lab 03](../lab/lab-03-exceptions-generics-exercises-2-3.md#scope-of-type-parameters)!

### 02. Variance Relationship about Generics and Wildcards

Just follow the different rules we have introduced in [lec-06-wildcards](../lecture/lec-06-wildcards/ "mention")

### 03. Type Inference and the Application of Wildcards

> If `class A implements Comparable<A>`, and `class B extends A`, then `B` actually implements `Comparable<A>` **not** `Comparable<B>`!

The original max method is as follows,

{% code lineNumbers="true" %}
```java
static <T extends Comparable<T>> T max (List<T>, list) {
    T max = list.get(0);
    if (list.get(1).compareTo(max)) > 0) {
        return list.get(1);
    }
    return max;
}
```
{% endcode %}

To make the `max` method more flexible and allow assignments (i) `Fruit f = max(apples);` and (ii) `Apple a = max(apples);`, we need to adjust the method header using **PECS (Producer-Extends, Consumer-Super)** principles. Here's the solution and explanation:

{% code lineNumbers="true" %}
```java
static <T extends Comparable<? super T>> T max(List<? extends T> list)
```
{% endcode %}

#### Explanation using PECS

{% stepper %}
{% step %}
**Producer (`List<? extends T>`)**

The `List` acts as a **producer** of elements (we read `T` instances from it). Using `? extends T` allows the list to contain **subtypes of `T`**. For example:

* If `T = Fruit`, a `List<Apple>` (where `Apple extends Fruit`) is valid.
* This enables `Fruit f = max(apples);` (assignment (i)), where `apples` is `List<Apple>`.
{% endstep %}

{% step %}
**Consumer (`Comparable<? super T>`)**

The `Comparable` interface is a **consumer** (it accepts `T` as an argument in `compareTo`). Using `? super T` allows `T` to implement `Comparable` for **itself or any supertype**. For example:

* If `Apple` doesn’t implement `Comparable<Apple>` but `Fruit` implements `Comparable<Fruit>`, `Apple` can still be compared via its supertype `Fruit`.
* This enables `Apple a = max(apples);` (assignment (ii)), as `Apple` satisfies `Comparable<? super Apple>` (via `Fruit`).
{% endstep %}
{% endstepper %}

## Tips

1. If `class A implements Comparable<A>`, and `class B extends A`, then `B` actually implements `Comparable<A>` **not** `Comparable<B>`!
