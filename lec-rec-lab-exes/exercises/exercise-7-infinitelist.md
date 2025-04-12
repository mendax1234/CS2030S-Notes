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

> The most challenging part in this exercise!!!

Keeps taking elements while they satisfy a condition. Stops at first failure.

**Implementation**

{% tabs %}
{% tab title="Normal Approach" %}
1. What **conditions** do we need to check for the head?
   1. Whether the head exists,
   2. Whether the head passes the predicate

```java
Lazy<Boolean> headExists = this.head.filter(
    head -> !head.equals(Maybe.none()));
Lazy<Boolean> passTest = this.head.filter(
    head -> !head.filter(predicate).equals(Maybe.none()));
```

2. Under what **conditions** do we keep the head?
   1. The head does not exist, or
   2. The head passes the test
3. How to combine the conditions Lazily, **Use** `Lazy::combine`

```java
Lazy<Boolean> shouldKeepHead = headExists.combine(passTest,
    (b1, b2) -> !b1 || b2);
```

**Final Code**

{% code lineNumbers="true" %}
```java
public InfiniteList<T> takeWhile(BooleanCondition<? super T> p) {
    Lazy<Boolean> headExists = this.head.filter(
            head -> !head.equals(Maybe.none()));
    Lazy<Boolean> passTest = this.head.filter(
            head -> !head.filter(p).equals(Maybe.none()));
    Lazy<Boolean> shouldKeepHead = headExists.combine(passTest,
            (b1, b2) -> !b1 || b2);
    // Keep the head if it doesn't exist or passes the test.
    return new InfiniteList<>(
            shouldKeepHead.map(bool -> bool
                    ? this.head.get()
                    : Maybe.none()),
            shouldKeepHead.map(bool -> bool
                    ? this.tail.map(list -> list.takeWhile(p)).get()
                    : InfiniteList.sentinel())
    );
}
```
{% endcode %}
{% endtab %}

{% tab title="One-liner Approach" %}
The idea is that

1. Filter the current list's head first.
2. If the current head is not filtered, we pass down to the tail
3. Else, (means the head either DNE or doesn't pass the test)
   1. if the head doesn't pass the test, return sentinel
   2. else (means the head doesn't exist), pass down to the tail

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
{% endtab %}
{% endtabs %}
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

1. **Iteratively**: Use the idea of **traversing through** the list
2. **Recursively**: Skips `Maybe.none()`, combines `head` with `tail.reduce()` result recursively.

{% tabs %}
{% tab title="Iterative" %}
{% code overflow="wrap" lineNumbers="true" %}
```java
public <R> R reduce(R identity, Combiner<? super R, ? super T, ? extends R> accumulator) {
    R sum = identity;
    InfiniteList<T> currList = this;
    while (!currList.isSentinel()) {
        sum = currList.head.get()
                .map(h -> accumulator.combine(sum, h))
                .orElse(sum);
        currList = currList.tail.get();
    }
    return sum;
}
```
{% endcode %}
{% endtab %}

{% tab title="Recursive" %}
{% code overflow="wrap" lineNumbers="true" %}
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
{% endtab %}
{% endtabs %}
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
  InfiniteList<T> currList = this;
  while (!currList.isSentinel()) {
    if (!currList.head.get().equals(Maybe.none())) {
      list.add(currList.head());
    }
    currList = currList.tail.get();
  }
  return list;
}
```
{% endcode %}
{% endstep %}

{% step %}
**Traverse through the Infinite List**

There is no explicit indexing in InfiniteList, so we need to traverse the&#x20;list by **shrinking the tail**.

{% code lineNumbers="true" %}
```java
public void forEach(Consumer<? super T> action) {
    InfiniteList<T> currList = this;
    while (!currList.isSentinel()) {
        // Consume the head
        currList.head.get().ifPresent(action); // Do the operation
        // Shrink the sublist
        currList = currList.tail.get();
    }
}
```
{% endcode %}
{% endstep %}
{% endstepper %}
