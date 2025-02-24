# Lec 06 - Wildcards

## Wildcards

In Java, **wildcards** (`?`) are used in generics to create more flexible and reusable code by allowing parameterized types to operate on **a range of types**, rather than a single specific one.

**Motivation**: As we have seen in [lec-05-generics](lec-05-generics/ "mention"), Java Generics are **invariant**. But if we add wildcards (`?`) to Generics, it will have some **variance** relationship.

For example,

```java
// Circle <: Shape
// A<? extends Circle> <: A<? extends Shape>
```

{% hint style="info" %}
Don't worry, this is called the upper-bounded wildcards and it will be covered very soon in this lecture!
{% endhint %}

### Upper-Bounded Wildcards

An _upper-bounded wildcard_ allows a generic type to accept **any subtype** of a specified class or interface `T`. This is useful when you want to read data from a generic structure and ensure that you're working with a specific base type or its subclasses.

For example,

{% code lineNumbers="true" %}
```java
  public void copyFrom(Seq<? extends T> src) {
    int len = Math.min(this.array.length, src.array.length);
    for (int i = 0; i < len; i++) {
        this.set(i, src.get(i));
    }
  }
```
{% endcode %}

Here, the Line 4 serves to read data from a generic structure. Here, what `copyFrom` does is to copy every element from another sequence called `src`, which is `Seq<? extends T>`, to the `Seq<T>` field in the current class.

{% hint style="info" %}
Each of the element from `src` should be of type `? extends T`, which is obviously a subtype of `T`.
{% endhint %}

#### Variance Relationship: Covariance

The upper-bounded wildcard has the following subtype relations:

1. If `S` <: `T`, then `A<? extends S>` <: `A<? extends T>` (covariance)
2. For any type `S`, `A<S>` <: `A<? extends S>`

#### Explanation

Let's use the knowledge we have seen in [Lab 01](../lab/lab-01-logistics-introduction-to-oop-exercise-0.md#subtype-relation).

> subtype is nothing but a subset

For the Covariance Rule 1,

1. `A<? extends S>` represents the subset containing every type that is a subtype of `S` (denote this subset as $$X$$).&#x20;
2. Similarly, `A<? extends T>` represents the subset containing every type that is a subtype of `T` (denote this subset as $$Y$$)
3. Since `S` <: `T`, we can intuitively see that the relationship between $$X$$ and $$Y$$ is that $$X\subset Y$$.
4. So, we can use "subtype is nothing but subset" again to deduce that `A<? extends S>` <: `A<? extends T>`.

For the Covariance Rule 2,

1. `A<S>` is a subset with a **single** element, which is type `S` (deonte this subset as $$X$$)
2. Similarly, `A<? extends S>` represents the subset containing every type that is a subtype of `S` (denote this subset as $$Y$$).&#x20;
3. We can intuitively see that the relationship between $$X$$ and $$Y$$ is that $$X\subset Y$$.
4. So, we can use "subtype is nothing but subset" again to deduce that `A<S>` <: `A<? extends S>`.

#### Corollary

1. **Substitutation principle:** If `S` <: `T`, then `A<S>` <: `A<? extends T>`.
2. **Transitivity:** If `S` <: `T` and `T` <: `U`, then `A<S>` <: `A<? extends U>`.

### **Lower-Bounded Wildcards**

A _lower-bounded wildcard_ allows a generic type to accept any **supertype** of a specified class `T`. This is particularly useful when you want to **write** data to a generic structure and ensure that the structure can **accept** objects of a specific type or its subclasses.

For example,

{% code lineNumbers="true" %}
```java
  public void copyTo(Seq<? super T> dest) {
    int len = Math.min(this.array.length, dest.array.length);
    for (int i = 0; i < len; i++) {
        dest.set(i, this.get(i));
    }
  }
```
{% endcode %}

Here, Line 4 serves to **write** data to a generic structure. Here, what `copyTo` does is to copy every element in the `Seq<T>` field in the current class to the another sequence called `dest`, which is `Seq <? super T>`.

{% hint style="info" %}
Each element from `dest` should be of type `? super T`, which is obviously a supertype of `T`.
{% endhint %}

#### Variance Relationship: Contravariance

The lower-bounded wildcard has the following subtype relationship:

1. If `S` <: `T`, then `A<? super T>` <: `A<? super S>` (contravariance)
2. For any type `S`, `A<S>` <: `A<? super S>`

#### Explanation

Similarly as we have seen above, we can use the knowledge from [Lab 01](../lab/lab-01-logistics-introduction-to-oop-exercise-0.md#subtype-relation) again

> subtype is nothing but a subset

For the Contrariance Rule 1,

1. `A<? super S>` represents the subset containing every type that is a **supertype** of `S` (denote this subset as $$X$$).&#x20;
2. Similarly, `A<? super T>` represents the subset containing every type that is a **supertype** of `T` (denote this subset as $$Y$$)
3. Since `S` <: `T`, we can intuitively see that the relationship between $$X$$ and $$Y$$ is that $$Y\subset X$$.
4. So, we can use "subtype is nothing but subset" again to deduce that `A<? super T>` <: `A<? super S>`.

For the Contrariance Rule 2,

1. `A<S>` is a subset with a **single** element, which is type `S` (deonte this subset as $$X$$)
2. Similarly, `A<? super S>` represents the subset containing every type that is a **supertype** of `S` (denote this subset as $$Y$$).&#x20;
3. We can intuitively see that the relationship between $$X$$ and $$Y$$ is that $$X\subset Y$$.
4. So, we can use "subtype is nothing but subset" again to deduce that `A<S>` <: `A<? super S>`.

#### Corollary

1. **Transitivity:** If S <: T <: U, then A\<? super U> <: A\<? super T> <: A\<? super S>.

### PECS rule

"PECS" stands for "Producer Extends; Consumer Super". Basically this rule states that:

* **Producer** (provides data): Use upper-bounded wildcards `? extends T` to read from it.
* **Consumer** (accepts data): Use lower-bounded wildcards `? super T` to write to it.

### Unbounded Wildcards

An **unbounded wildcard** (`?`) means "I don’t know what the type is." It is used when we want to work with **any type** but don’t need to specify a relationship (subtype or supertype).

For example,

{% code lineNumbers="true" %}
```java
void foo(Seq<?> seq) {
   :
  x = seq.get(0);
  seq.set(0, y);

}
```
{% endcode %}

Here, the type of `x` can only be `Object` since it's the only safe choice. For, `y` it becomes even more restrictive, it must be `null`.

So, for a `Seq<?>`, we have the following principles,

* We **cannot add** anything to `seq`, except `null`, because we don’t know the exact type.
* We **can read** from it, but the elements are treated as `Object`.

{% hint style="warning" %}
`Seq<?>` is different from `Seq<Object>`, where the latter is **not** the supertype of any parameterized type `Seq<T>`, `Seq<Object>` is just a parameterized type of `Seq<T>` where `T` is `Object`.
{% endhint %}

#### Variance Relationship

1. `A<?>` is the **supertype** of every **parameterized** type of `A<T>`, that is `A<T>` <: `A<?>`.

#### `Seq<?>`, `Seq<Object>` and `Seq`

* `Seq<?>` is a sequence of objects of some specific, but unknown type;
* `Seq<Object>` is a sequence of `Object` instances, with type checking by the compiler;
* `Seq` is a sequence of `Object` instances, without type checking.

### Revisit Rawtype

#### **The Problem with Raw Types and Generics**

Java's generics (like `List<String>`) lose their type information during compilation due to "type erasure." This causes two main issues:

* **Type Checks:** You can't reliably check specific generic types at runtime (e.g., `instanceof List<String>` won't work).
* **Arrays:** You can't directly create arrays of specific generic types (e.g., `new List<String>[10]` is invalid).

#### **Old Solution - Raw Types**

Previously, Java allowed you to:

* Use raw types (without generics) for these cases:
  * `instanceof` checks: `a instanceof ArrayList` (instead of `ArrayList<String>`)
  * Array creation: `new ArrayList[10]` (instead of `ArrayList<String>[10]`)

#### **New Solution - Unbounded Wildcards (`<?>`)**

Instead of raw types, we now use `<?>` (unknown type) to handle both scenarios better:

{% stepper %}
{% step %}
**For** `instanceOf` **checks**

```java
if (a instanceof ArrayList<?>) { ... }
```

* The `<?>` explicitly tells readers: "We're checking if it's an ArrayList of _any type_."
* This works because `<?>` matches the erased type (just like raw types), but it's clearer and safer.
{% endstep %}

{% step %}
**For Array creation**

```java
Comparable<?>[] arr = new Comparable<?>[10];
```

* `Comparable<?>` is considered a "reifiable" type (its type info isn't lost during compilation).
* This allows safe array creation while still using generics.
{% endstep %}
{% endstepper %}

#### **Why This Matters**

* **Clarity:** `<?>` clearly communicates "any type" instead of silently dropping generics (raw types).
* **Safety:** Discouraging raw types helps avoid accidental type errors in your code.
* **Modern Java:** Newer Java versions encourage using wildcards (`<?>`) over raw types.

{% hint style="success" %}
Always use `<?>` instead of raw types when you need to check generic types with `instanceof` or create arrays of generic classes.
{% endhint %}

## Type Inference

Type inference in Java is the compiler's ability to _automatically determine_ (deduce) the type arguments for generic methods, constructors, or classes **based on the context** where they are used.&#x20;

### Rule of Thumb

When doing Type Inference, form your constraints in the following ways

{% stepper %}
{% step %}
**Target: \_\_ <: \_\_**

This means "the return type of the method" <: "the type of the variable you are assigning to"
{% endstep %}

{% step %}
**Argument: \_\_ <: \_\_**

This means "the type of the **argument**" <: "the type of the **parameter**"
{% endstep %}

{% step %}
**Bound: \_\_ <: \_\_**

This means we need to consider "the bound of the generic type parameters"
{% endstep %}
{% endstepper %}

For example, the following is our background,

{% code lineNumbers="true" %}
```java
class Fruit implements Comparable<Fruit> {
    @Override
    public int compareTo(Fruit f) {
        return 0; // stub
    }
}

class Apple extends Fruit {
}

List<Fruit> fruits = List.of(new Fruit(), new Apple());
List<Apple> apples = List.of(new Apple(), new Apple());

static <T extends Comparable<T>> T max(List<T> list) {
    T max = list.get(0);
    if (list.get(1).compareTo(max) > 0) {
        return list.get(1);
    }
    return max;
}
```
{% endcode %}

If we use the following command, what will the `T` in `max` be inferred as?

{% code lineNumbers="true" %}
```java
Fruit f = max(fruits);
```
{% endcode %}

1. **Target**: `T` <: `Fruit`
2. **Argument:** `List<Fruit>` <: `List<T>`. Due to invaraince of generics, `T` must be `Fruit`
3. **Bound**: `T` <: `Comparable<T>`

By combing these three constraits, we can see that `T` will be inferred as `Fruit`.

#### Rules of Type Inference

We now summarize the steps for type inference. First, we figure out all of the type constraints on our type parameters, and then we solve these constraints. If no type can satisfy all the constraints, we know that Java will fail to compile. If in resolving the type constraints for a given type parameter `T` we are left with:

* `Type1 <: T <: Type2`, then `T` is inferred as `Type1`
* `Type1 <: T`, then `T` is inferred as `Type1`
* `T <: Type2`, then `T` is inferred as `Type2`

where `Type1` and `Type2` are arbitary types.
