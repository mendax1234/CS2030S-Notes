# Lec 06 - Wildcards

## Wildcards

In Java, **wildcards** (`?`) are used in generics to create more flexible and reusable code by allowing parameterized types to operate on **a range of types**, rather than a single specific one.

### Upper-Bounded Wildcards

An _upper-bounded wildcard_ allows a generic type to accept **any subtype** of a specified class or interface `T`. This is useful when you want to read data from a generic structure and ensure that you're working with a specific base type or its subclasses.

For example,

{% code lineNumbers="true" %}
```java
public static double sumOfList(List<? extends Number> list) {
    double sum = 0.0;
    for (Number num : list) {
        sum += num.doubleValue();
    }
    return sum;
}
```
{% endcode %}

This method works for `List<Integer>`, `List<Double>`, etc.

#### Covariance

The upper-bounded wildcard has the following subtype relations:

1. If `S` <: `T`, then `A<? extends S>` <: `A<? extends T>` (covariance)
2. For any type `S`, `A<S>` <: `A<? extends S`

### **Lower-Bounded Wildcards**

A _lower-bounded wildcard_ allows a generic type to accept any **supertype** of a specified class `T`. This is particularly useful when you want to write data to a generic structure and ensure that the structure can **accept** objects of a specific type or its subclasses.

For example,

{% code lineNumbers="true" %}
```java
public static void addCircle(List<? super Circle> list) {
    list.add(new Circle());
}

```
{% endcode %}

This method can accept a `List<Circle>`, `List<Shape>`, or even a `List<Object>`. The lower-bounded wildcard `<? super Circle>` ensures that the list can hold `Circle` objects, as well as objects of any superclass of `Circle`.

#### Contravariance

The lower-bounded wildcard has the following subtype relationship:

1. If `S` <: `T`, then `A<? super T>` <: `A<? super S>` (contravariance)
2. For any type `S`, `A<S>` <: `A<? super S>`

### Unbounded Wildcards

An **unbounded wildcard** (`?`) means "I don’t know what the type is." It is used when we want to work with **any type** but don’t need to specify a relationship (subtype or supertype).

For example,

{% code lineNumbers="true" %}
```java
List<?> list = new ArrayList<String>();
List<?> list2 = new ArrayList<Integer>();
```
{% endcode %}

Here, `List<?>` can hold a list of **any type**, but once assigned, we don't know what type it holds. This means:

* We **cannot add** anything to `list`, except `null`, because we don’t know the exact type.
* We **can read** from it, but the elements are treated as `Object`.

### PECS rule

"PECS" stands for "Producer Extends; Consumer Super". Basically this rule states that:

* **Producer** (provides data): Use `? extends T` to read from it.
* **Consumer** (accepts data): Use `? super T` to write to it.
