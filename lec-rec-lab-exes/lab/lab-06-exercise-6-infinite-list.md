# Lab 06 - Exercise 6, Infinite List

## Exercise 6 Review

{% stepper %}
{% step %}
`get()`

`get()` is the soul of `Lazy`! See more example from [#main-methods-explanation](../exercises/exercise-6-lazy.md#main-methods-explanation "mention") from Exercise 6. Remember that `get()` is **expensive**!
{% endstep %}

{% step %}
`equals()`

In `Lazy::equals` evaluate both Lazy first before comparing them.

{% tabs %}
{% tab title="Wrong" %}
{% code lineNumbers="true" %}
```java
@Override
public boolean equals(Object obj) {
    if (obj instanceof Lazy<?>) {
        @SuppressWarnings("unchecked")
        Lazy<?> lazy = (Lazy<?>) obj;
        return this.get().equals(lazy.get());
    }
    return false;
}
// Not correct because this.get() could return a null.
```
{% endcode %}
{% endtab %}

{% tab title="Correct" %}
{% code lineNumbers="true" %}
```java
@Override
public boolean equals(Object obj) {
    if (obj instanceof Lazy<?>) {
        @SuppressWarnings("unchecked")
        Lazy<?> lazy = (Lazy<?>) obj;
        // Evaluate both Lazy
        this.get();
        lazy.get();
        // Now both values are Maybe.Some
        return this.value.equals(lazy.value);
    }
    return false;
}
```
{% endcode %}
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
`map()`

Whenever you implement the `map`, `flatMap`, `filter`, `combine`, take note that the `get()` is **expensive**!

{% tabs %}
{% tab title="Wrong" %}
```java
public <U> Lazy<U> map(Transformer<? super T, ? extends U> f) {
    U result = f.transform(this.get());
    return Lazy.of(() -> result);
}
// No Longer Lazy because this.get() is expensive
```
{% endtab %}

{% tab title="Correct" %}
```java
public <U> Lazy<U> map(Transformer<? super T, ? extends U> f) {
    return Lazy.of(() -> f.transform(this.get()));
}
```
{% endtab %}
{% endtabs %}

Similarly, you can do the same thing on the `flatMap`, `filter` and `combine`. See more from [#main-methods-explanation](../exercises/exercise-6-lazy.md#main-methods-explanation "mention") from Exercise 6.
{% endstep %}
{% endstepper %}

### Keys of being lazy

Note down the usage of `indexOf()`.

## Infinite List

**Motivation**: In the natural world, many things are infinite. But computer data structure can only hold a finite number of data points.

### Mathematical Context

Recurrence Relation

### List Creation

Use the recurrence relation to understand. Plus the ps7! The next term is a function of all its previous terms.

Use this, we can rewrite the Infinite list as \[head, successive mapping]. Awesome!

### List Memoisation

Motivation and implementation

Lazy\<Maybe\<T>> or Maybe\<Lazy\<T>>

### List Aggregation

Combine n elements into one using the function f provided.

1. Find identity
2. Find the aggregation function (fancy way of saying combiner)

### Sublist by Predicate

When to stop, when not, important.
