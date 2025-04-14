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

**Core idea**: Delay the evaluation **as much as possible**.

* **Don’t invoke** `produce()` anywhere other than when  &#x20;`get()` is called.
* **Produce the value only once** and **cache** it immediately.  &#x20;Subsequent calls of `get()` use the cached value directly.
* **Don’t invoke** `get()` directly. **Compose** the functions  &#x20;into a new Producer in `map`, `flatMap`, `filter`, `combine`  &#x20;and so on.

## Infinite List

**Motivation**:&#x20;

* In the natural world, many things are infinite.
* But computer data structure can only hold a finite number of data points.
* A need arises: **How to efficiently represent an infinite  &#x20;set of values using a finite structure**?
* Idea: It is relatively easy to capture the structure of an  &#x20;infinite set **if its elements follow a certain pattern.**
* We can produce the “next” element based on all  &#x20;previously evaluated elements.

### Mathematical Context

The last sentence can be achieved by using **recurrence relation.**

> A **recurrance relation** on a set $$X$$ is defined by $$k$$ initial terms $$x_0,x_1,\cdots,x_{k-1}$$ and a function $$f:X^k\rightarrow X$$ such that $$f(x_i,x_{i+1},\cdots,x_{i+k-1})=x_{i+k}$$.

### List Creation

Use the above idea of **recurrance relation**, we can rewrite the Infinite list as$$[\text{head}, \text{successive mapping}]$$. This is the one of the key ideas in the Infinite List.

So, the basic field of our Infinite List will be

{% code lineNumbers="true" %}
```java
class InfiniteList<T> {
    private Producer<T> head; // A[k]
    private Producer<InfiniteList<T>> tail; // A[k + 1...]
}
```
{% endcode %}
