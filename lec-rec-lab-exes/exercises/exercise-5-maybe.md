# Exercise 5 - Maybe

## What is Maybe\<T>

Let's take the counter as an example.

```java
Counter c = bank.findCounter();
```

The `findCounter()` function is supposed to map from the bank (domain) to an available counter (codomain should be Counter!). However, if there is not available counter, it will return a `null`, which is not a Counter! This means that `findCounter()` is **not a pure function**!

To solve that, we can create a new Type called `Maybe<Counter>`, which basically extends the codomain to include the `null`. Thus, the `findCounter()` is now **a pure function**!

