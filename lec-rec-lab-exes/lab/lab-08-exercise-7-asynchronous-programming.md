# Lab 08

## Exercise 7 Review

### head() — Retrieve the Head Safely

Write the naive if-else statement first, then convert it into a one-line.

This method will find the first existing head.

### tail() — Retrieve the Tail

We want the tail, but we use the head to do the if-else, so a mapping is needed.

This method will find the first existing tail.

### map() — How to map the List Efficiently

Use the succinct way, your `this.head` and `this.tail` are already Lazy, can just call the method.

{% hint style="info" %}
To avoid stack overflow, try not use `this.head()` and `this.tail()`.
{% endhint %}

### forEach()

{% tabs %}
{% tab title="Iterative Approach" %}
Add the code which uses the "shrinking" idea to traverse through the InfiniteList.
{% endtab %}

{% tab title="Recursive Approach" %}
Awesome implementation here!
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

1. Start by considering the condition to use (In this exercise, we always start with `this.head.get()`.
2. End by using `orElse()`/`orElseGet()`.&#x20;
   1. Anything between the start and the end is your **if** branch.
   2. The "placeholder" in your end is the **else** branch.
{% endhint %}

### takeWhile()

There are two conditions here

1. whether the head exists
2. whether the head passes the predicate

## Asynchronous Programming

### CompletableFuture\<T>

It can be interpreted as a **task** which will return a result of type `T` when completed.

Add the common methods notes, they are clear and awesome!
