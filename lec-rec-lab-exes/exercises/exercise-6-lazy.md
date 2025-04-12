# Exercise 6 - Lazy

## What is Lazy\<T>

`Lazy<T>` is a class that implements lazy evaluation in Java. Lazy evaluation means a value is computed only when it's actually needed, not when it's defined.

***

**Motivation:**

* Improves performance by avoiding unnecessary calculations
* Handles potentially expensive operations only when required
* Supports operations on values that don't exist yet

***

As we have seen in the lecture, Lazy has two ways

1. Use Lambda as Delayed Data
2. Memoization

And the `Lazy<T>` we build today combines these two ways!

***

**Example**

```java
// Without lazy evaluation - calculates immediately even if never used
int result = expensiveCalculation(); 

// With lazy evaluation - calculation happens only when get() is called
Lazy<Integer> lazyResult = Lazy.of(() -> expensiveCalculation());
// ...later in code...
int value = lazyResult.get(); // calculation happens here
```

## Main methods explanation

Before delving into deeper details, this is the field and constructor for our `Lazy<T>`

```java
public class Lazy<T> {
  private Producer<? extends T> producer;
  private Maybe<T> value;

  private Lazy(Maybe<T> v, Producer<? extends T> s) {
    this.value = v;
    this.producer = s;
  }
}
```

{% stepper %}
{% step %}
`of()`

The `of()` is `Lazy<T>`'s factory method, it support two ways to create a Lazy.

1. `of(T v)`: Creates a Lazy with an **already-computed** value
2. `of(Producer<? extends T> s)`: Creates a Lazy with a producer for **delayed computation**

**Implementation**

{% tabs %}
{% tab title="of(T v)" %}
```java
public static <T> Lazy<T> of(T v) {
  return new Lazy<>(Maybe.some(v), null);
}
```
{% endtab %}

{% tab title="of(Producer<? extends T> s)" %}
```java
public static <T> Lazy<T> of(Producer<? extends T> s) {
  return new Lazy<>(Maybe.none(), s);
}
```

**Example**


{% endtab %}
{% endtabs %}

**Example**

{% tabs %}
{% tab title="of(T v)" %}
```java
Lazy<String> greeting = Lazy.of("Hello");
System.out.println(greeting.get()); // "Hello" (no computation needed)
```
{% endtab %}

{% tab title="of(Producer<? extends T> s)" %}
```java
// This computation doesn't run until get() is called
Lazy<Integer> lazySum = Lazy.of(() -> expensiveSum(1000000));
// ...
Integer result = lazySum.get(); // computation happens here
```
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
`get()`

This method is the **core** of lazy evaluation. It checks if the value is already computed (present in the `Maybe` container). If available, it returns it; otherwise, it calls the producer to compute the value, stores it for future requests, and returns it. This ensures the computation happens exactly once, the first time it's needed.

**Implementation**

```java
public T get() {
  return this.value.orElseGet(
      () -> {
        T compute = this.producer.produce();
        this.value = Maybe.some(compute);
        return compute;
      });
}
```

**Example**

```java
Lazy<Double> calculation = Lazy.of(() -> Math.sqrt(1024));
// First call computes the value
double value1 = calculation.get(); // 32.0
// Second call reuses the computed value
double value2 = calculation.get(); // 32.0 (no recomputation)
```

{% hint style="success" %}
This `get()` method beautifully combines **memoization** and **lazy evaluation** using a lambda expression. And all the following methods that called `get()` will **inherit this laziness**!
{% endhint %}
{% endstep %}

{% step %}
`map(Transformer<? super T, ? extends U> t)`

The map method allows transformation of a Lazy value into another type **without triggering immediate computation**. It **creates a new Lazy** that, when evaluated, will **first get this Lazy's value and then apply the transformation function to it**. This maintains laziness throughout the chain of operations.

**Implementation**

```java
public <U> Lazy<U> map(Transformer<? super T, ? extends U> t) {
  return Lazy.of(() -> t.transform(this.get()));
}
```

**Example**

```java
Lazy<Integer> number = Lazy.of(() -> 10);
Lazy<String> text = number.map(n -> "Number is: " + n);
// Transformation only happens when get() is called
System.out.println(text.get()); // "Number is: 10"
```
{% endstep %}

{% step %}
`flatMap(Transformer<? super T, ? extends Lazy<? extends U>> t)`

The flatMap method is similar to map but works with transformations that themselves return Lazy values. It prevents ending up with nested Lazy structures (like `Lazy<Lazy>`). When the resulting Lazy is evaluated, it gets this Lazy's value, applies the transformation to get another Lazy, and then gets the value from that second Lazy.

**Implementation**

{% code overflow="wrap" %}
```java
public <U> Lazy<U> flatMap(Transformer<? super T, ? extends Lazy<? extends U>> t) {
  return Lazy.of(() -> t.transform(this.get()).get());
}
```
{% endcode %}

**Example**

{% code overflow="wrap" %}
```java
Lazy<Integer> number = Lazy.of(() -> 4);
Lazy<Integer> squareRoot = number.flatMap(n -> Lazy.of(() -> (int)Math.sqrt(n)));
System.out.println(squareRoot.get()); // 2
```
{% endcode %}
{% endstep %}

{% step %}
`filter(BooleanCondition<? super T> c)`

The filter method returns a new Lazy that, when evaluated, will test the original value against the provided condition. Unlike filters in collections that remove elements, this filter converts the value into a boolean result indicating whether the condition passed. The evaluation of both the original value and the condition is deferred until `get()` is called.

**Implementation**

```java
public Lazy<Boolean> filter(BooleanCondition<? super T> c) {
  return Lazy.of(() -> c.test(this.get()));
}
```

**Example**

```java
Lazy<Integer> number = Lazy.of(() -> 15);
Lazy<Boolean> isEven = number.filter(n -> n % 2 == 0);
System.out.println(isEven.get()); // false
```
{% endstep %}

{% step %}
`combine(Lazy<? extends S> src, Combiner<? super T, ? super S, ? extends R> c)`

The combine method allows two Lazy values to be combined into a new Lazy result using a combining function. When evaluated, it will get both values and then apply the combiner function to produce the result. This is useful for operations that require multiple inputs, like addition, concatenation, or any binary operation, while preserving lazy evaluation for all components.

**Implementation**

```java
public <S, R> Lazy<R> combine(
    Lazy<? extends S> src, Combiner<? super T, ? super S, ? extends R> c) {
  return Lazy.of(() -> c.combine(this.get(), src.get()));
}
```

**Example**

```java
Lazy<Integer> first = Lazy.of(() -> 10);
Lazy<Integer> second = Lazy.of(() -> 20);
Lazy<Integer> sum = first.combine(second, (a, b) -> a + b);
System.out.println(sum.get()); // 30
```
{% endstep %}

{% step %}
`equals()`

Compares Lazy objects based on their computed values.

**Implementation**

```java
@Override
public boolean equals(Object obj) {
  if (obj instanceof Lazy<?>) {
    Lazy<?> lazyObj = (Lazy<?>) obj;
    if (this.get().equals(lazyObj.get())) {
      return true;
    }
  }
  return false;
}
```

**Example**

```java
Lazy<Integer> a = Lazy.of(() -> 5 + 5);
Lazy<Integer> b = Lazy.of(10);
System.out.println(a.equals(b)); // true
```
{% endstep %}
{% endstepper %}

Refer back to the [#keys-of-being-lazy](../lab/lab-06-exercise-6-infinite-list.md#keys-of-being-lazy "mention"), this is concise and important!

## Lazy List

Use the above `Lazy<T>` to build a list that supports:

1. Delayed evaluation when needed
2. Memoization

We have come up with the following `LazyList.java`

{% code overflow="wrap" lineNumbers="true" %}
```java
public static <T> LazyList<T> generate(int n, T seed, Transformer<? super T, ? extends T> f) {
    LazyList<T> lazyList = new LazyList<>(new ArrayList<Lazy<T>>(n));
    Lazy<T> curr = Lazy.of(seed); // Initialise an element with the seed.
    for (int i = 0; i < n; i++) {
        lazyList.list.add(curr); // Just append to the end of the list.
        curr = curr.map(f);      // Map the current element to its successor.
    }
    return lazyList;
}
```
{% endcode %}

In our LazyList, there is no need to keep track of current index or retrieve/insert by index etc.

{% code lineNumbers="true" %}
```java
public int indexOf(T value) {
    return this.list.indexOf(Lazy.of(value));
}
```
{% endcode %}

`List::indexOf(T arg)`: returns the index of the first element whose **value**&#x20;**equals the argument**, determined by the equals method.

{% hint style="info" %}
**No need to perform linear search** manually because our `Lazy::equals`&#x20;method checks equality by value. This is elegant!
{% endhint %}
