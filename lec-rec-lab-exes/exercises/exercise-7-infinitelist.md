# Exercise 7 - InfiniteList

## What is InfiniteList

In [Lab 06](../lab/lab-06-exercise-6-infinite-list.md#infinite-list), there is a very good explanation about Infinite List, go back to take a read for better understanding!

## Main methods explanation

{% stepper %}
{% step %}
**Fields**

Our InfiniteList implementation has three fields

{% code lineNumbers="true" %}
```java
private final Lazy<Maybe<T>> head;
private final Lazy<InfiniteList<T>> tail;
private static InfiniteList<?> SENTINEL = new Sentinel();
```
{% endcode %}
{% endstep %}

{% step %}
`generate(Producer producer)`

Creates an infinite list where each element is generated on demand using a `Producer`.

**Implementation**

It wraps each element in a `Maybe` using `Maybe.some()`, and lazily generates the rest using recursion.

{% code lineNumbers="true" %}
```java
public static <T> InfiniteList<T> generate(Producer<T> producer) {
  return new InfiniteList<>(
      Lazy.of(() -> Maybe.some(producer.produce())),
      Lazy.of(() -> generate(producer)));
}
```
{% endcode %}

{% hint style="info" %}
Here, we assume that the producer will always produce a **valid** value. So, we wrap it into a `Maybe.some` instead of using `Maybe.of()`.
{% endhint %}
{% endstep %}

{% step %}
`iterate(T seed, Transformer<T, T> next)`

Creates an infinite list starting from a seed, where each next element is computed by applying a transformer function to the previous one.

**Implementation**

Each new element is lazily created from the transformation of the seed.

{% code lineNumbers="true" %}
```java
public static <T> InfiniteList<T> iterate(T seed, Transformer<T, T> next) {
  return new InfiniteList<>(
      Lazy.of(Maybe.some(seed)),
      Lazy.of(() -> iterate(next.transform(seed), next)));
}
```
{% endcode %}
{% endstep %}

{% step %}
`head()`

Returns the first non-filtered (i.e. `Maybe.some`) element of the list.

**Implementation**

If the current head is empty (`Maybe.none`), it moves to the next one via `tail`.

{% code lineNumbers="true" %}
```java
public T head() {
  Maybe<T> h = this.head.get();
  return h.orElseGet(() -> this.tail.get().head());
}
```
{% endcode %}
{% endstep %}

{% step %}
`tail()`

Returns the next node in the list, skipping any `Maybe.none()` elements (which were filtered out).

**Implementation**

If current head is present, return tail; otherwise skip ahead recursively.

{% code overflow="wrap" lineNumbers="true" %}
```java
public InfiniteList<T> tail() {
  Maybe<T> h = this.head.get();
  return h.map(x -> this.tail.get()).orElseGet(() -> this.tail.get().tail());
}
```
{% endcode %}

{% hint style="info" %}
Condition here is `head()` is present or not, if it is, we need to use `map` to map it to the `tail` we want.
{% endhint %}
{% endstep %}

{% step %}
`map(Transformer<? super T, ? extends R> mapper)`

Transforms every element in the list using a provided function.

**Implementation**

Applies the map to both the head and tail lazily.

{% code overflow="wrap" lineNumbers="true" %}
```java
public <R> InfiniteList<R> map(Transformer<? super T, ? extends R> mapper) {
  return new InfiniteList<>(
    this.head.map(x -> x.map(mapper)),
    this.tail.map(x -> x.map(mapper)));
}
```
{% endcode %}
{% endstep %}

{% step %}
`filter(BooleanCondition<? super T> predicate)`

Filters elements based on a condition; filtered elements are wrapped as `Maybe.none()`.

**Implementation**

Applies the predicate to the head, and recursively filters the tail.

{% code lineNumbers="true" %}
```java
public InfiniteList<T> filter(BooleanCondition<? super T> predicate) {
  return new InfiniteList<>(
      Lazy.of(() -> this.head.get().filter(predicate)),
      Lazy.of(() -> this.tail.get().filter(predicate)));
}
```
{% endcode %}
{% endstep %}

{% step %}
`sentinel()`

Returns a special end-of-list marker (`Sentinel` object) that represents the end of a (limited) list.

**Implementation**

Casts the shared `SENTINEL` object to the desired type.

{% code lineNumbers="true" %}
```java
public static <T> InfiniteList<T> sentinel() {
  @SuppressWarnings("unchecked")
  InfiniteList<T> sentinel = (InfiniteList<T>) SENTINEL;
  return sentinel;
}
```
{% endcode %}
{% endstep %}

{% step %}
`limit(long n)`

Limits the list to `n` number of actual elements (ignoring filtered ones).

**Implementation**

Each time it finds a non-filtered element, it decrements `n`. If `n` is zero or less, it returns a sentinel.

{% code lineNumbers="true" %}
```java
public InfiniteList<T> limit(long n) {
  if (n <= 0) {
    return InfiniteList.sentinel();
  }
  return new InfiniteList<>(
      this.head,
      Lazy.of(
          () ->
              this.head
                  .get()
                  .map(h -> this.tail.get().limit(n - 1))
                  .orElseGet(() -> this.tail.get().limit(n))));
}
```
{% endcode %}
{% endstep %}

{% step %}
`takeWhile(BooleanCondition<? super T> predicate)`

Keeps taking elements while they satisfy a condition. Stops at first failure.

**Implementation**

Filters the head and then decides based on whether it passes or fails the predicate.

{% code overflow="wrap" lineNumbers="true" %}
```java
public InfiniteList<T> takeWhile(BooleanCondition<? super T> predicate) {
  Lazy<Maybe<T>> filteredHead = this.head.map(x -> x.filter(predicate));
  Lazy<InfiniteList<T>> t =
      Lazy.of(
          () ->
              filteredHead
                  .get()
                  .map(h -> this.tail.get().takeWhile(predicate))
                  .orElseGet(() ->
                      this.head
                          .get()
                          .map(h -> InfiniteList.<T>sentinel())
                          .orElseGet(() -> this.tail.get().takeWhile(predicate))));
  return new InfiniteList<>(filteredHead, t);
}
```
{% endcode %}
{% endstep %}

{% step %}
`isSentinel()`

Tells whether the list is the sentinel.

**Implementation**

Default returns `false`. Sentinel overrides this to return `true`.

{% code lineNumbers="true" %}
```java
public boolean isSentinel() {
  return false;
}
```
{% endcode %}
{% endstep %}

{% step %}
`reduce(U identity, Combiner<U, ? super T, U> accumulator)`

Performs a reduction/fold operation over the list (like summing up or combining values).

**Implementation**

Skips `Maybe.none()`, combines `head` with `tail.reduce()` result recursively.

{% code lineNumbers="true" %}
```java
public <U> U reduce(U identity, Combiner<U, ? super T, U> accumulator) {
  U tailResult = this.tail.get().reduce(identity, accumulator);
  return this.head
      .get()
      .map(headVal -> accumulator.combine(tailResult, headVal))
      .orElse(tailResult);
}
```
{% endcode %}
{% endstep %}

{% step %}
`count()`

Counts how many non-filtered elements are in the list (before sentinel).

**Implementation**

Uses `reduce` with accumulator that counts.

{% code lineNumbers="true" %}
```java
public long count() {
  return this.reduce(0, (x, y) -> x + 1);
}
```
{% endcode %}
{% endstep %}

{% step %}
`toList()`

Collects all non-filtered elements into a Java `List<T>` until sentinel is reached.

**Implementation**

Iterates through the list, checks if `head` is not `Maybe.none`, and adds to list.

{% code lineNumbers="true" %}
```java
public List<T> toList() {
  List<T> list = new ArrayList<>();
  InfiniteList<T> t = this;
  while (!t.isSentinel()) {
    if (!t.head.get().equals(Maybe.none())) {
      list.add(t.head());
    }
    t = t.tail.get();
  }
  return list;
}
```
{% endcode %}
{% endstep %}
{% endstepper %}
