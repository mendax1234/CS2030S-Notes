# Lab 08 - Exercise 7, Asynchronous Programming

## Exercise 7 Review

> For this part, the implementation of each method can be found in [Exercise 7 — Infinite List](../exercises/exercise-7-infinitelist.md#main-methods-explanation).

### head() — Retrieve the Head Safely

Write the naive if-else statement first, then convert it into a one-line.

### tail() — Retrieve the Tail

We want the tail, but we use the head to do the if-else, so a mapping is needed.

### map() — How to map the List Efficiently

Use the succinct way, your `this.head` and `this.tail` are already Lazy, can just call the method.

{% hint style="info" %}
To avoid stack overflow, try not use `this.head()` and `this.tail()`.
{% endhint %}

### forEach()

{% tabs %}
{% tab title="Iterative Approach" %}
Traverse through the list by **shrinking the tail**. This is awesome!

```java
public void forEach(Consumer<? super T> action) {
    InfiniteList<T> currList = this;
    while (!currList.isSentinel()) {
        // Consume the head
        currList.head.get().ifPresent(action); // Maybe<T>::ifPresent
        // Shrink the sublist
        currList = currList.tail.get();
    }
}
```
{% endtab %}

{% tab title="Recursive Approach" %}
```java
public void forEach(Consumer<? super T> action) {
    this.head.get().ifPresent(action);
    this.tail.get().forEach(action);
}
```
{% endtab %}
{% endtabs %}

### reduce()

{% tabs %}
{% tab title="Iterative Approach" %}
1. Traverse through the list
2. If the head exists, do the function you want
{% endtab %}

{% tab title="Recursive Approach" %}
1. If your head exists, total = partial + h + total in tail
2. Otherwise, total = partial + total in tail
{% endtab %}
{% endtabs %}

{% hint style="info" %}
General advice in writing such one-line code

1. Start by **considering the condition** to use (In this exercise, we always start with `this.head.get()`.
2. End by using `orElse()`/`orElseGet()`.&#x20;
   1. Anything between the start and the end is your **if** branch.
   2. The "placeholder" in your end is the **else** branch.
{% endhint %}

## Asynchronous Programming

### CompletableFuture\<T>

It can be interpreted as a **task** which will return a result of type `T` when completed.

#### Common Completable Future methods

1. `CF(f).then(g)` means: **start** `g` **only after** `f` **has been   completed**.
   1. Examples: `thenRun`, `thenCombine`, `thenApply`
2. `static CF.async(g)` means: **start** `g` **on a new thread**
   1. Examples: `supplyAsync`, `runAsync`
3. `CF(f).then...async(g)` means: **start** `g` **only after** `f` **has been   &#x20;completed, but use a new thread.**
   1. Examples: `thenRunAsync`, `thenApplyAsync`.

Difference between `run` and `supply`: `run` executes a void&#x20;function while `supply` executes a function with a return&#x20;value.
