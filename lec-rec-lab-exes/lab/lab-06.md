# Lab 06

## Exercise 6 Review

### The idea behind get()

1. Update `value` is actually "memoisation"
2. The original method is correct

Pay attention to the `equals()` method.

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
