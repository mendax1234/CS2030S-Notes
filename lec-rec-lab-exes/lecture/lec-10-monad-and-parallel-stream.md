# Lec 10 - Monad and Parallel Stream

## Monad

At a high level, a **Monad** is a **design pattern** (from category theory) used in **functional programming** to **wrap values** and **chain computations** in a safe and composable way.

The follwing containers we have learned and implemented are all considered as **monad**.

| Container                                                             | Side-Information                                                                |
| --------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| `Maybe<T>`                                                            | The value might be there (i.e., `Some<T>`) or might not be there (i.e., `None`) |
| `Lazy<T>`                                                             | The value has been evaluated or not                                             |
| [`Loggable<T>`](https://nus-cs2030s.github.io/2425-s2/35-logger.html) | The log describing the operations done on the value                             |

{% hint style="info" %}
**Side-Information** is any extra data or context that **accompanies the main value**, but isn't the value itself.
{% endhint %}

Each of these classes has:

* an `of` method to initialize the value and side information.
* have a `flatMap` method to update the value and side information.

The class may also have other methods besides the two above. Additionally, the methods may have different names.

***

More specifically, **monads** are the classes that we wrote can follow certain patterns that make them well-behaved when we create them with `of` and chain them with `flatMap`.

{% hint style="info" %}
For ths sake of this course, we are only interested in deciding whether a class is a **monad** using the following three rules or applying these three rules to analyze the behavior of a **monad.**
{% endhint %}
