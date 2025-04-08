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
For ths sake of this course, we are only interested in deciding whether a **class/type** is a **monad** using the monad law or applying these laws to analyze the behavior of a **monad.**
{% endhint %}

### Monad Laws

Let `Monad` be a type that is a monad and `monad` be an instance of it, a Monad should following the following three laws,

{% stepper %}
{% step %}
**The left identity law**

`Monad.of(x).flatMap(x -> f(x))` must be the same as `f(x)`

Our `of` method should not do anything extra to the value and side information — it should simply wrap the value `x` into the `Monad`. Our `flatMap` method should not do anything extra to the value and the side information, it should simply apply the given lambda expression to the value.
{% endstep %}

{% step %}
**The right identity law**

`monad.flatMap(x -> Monad.of(x))` must be the same as `monad`

Since `of` should behave like an identity, it should not change the value or add extra side information. The `flatMap` above should do nothing and the expression above should be the same as `monad`.
{% endstep %}

{% step %}
**The associative law**

`monad.flatMap(x -> f(x)).flatMap(x -> g(x))` must be the same as `monad.flatMap(x -> f(x).flatMap(y -> g(y)))`

Regardless of how we group those calls to `flatMap`, their behavior must be the same.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
A **Monad** helps us reason about our code!
{% endhint %}

## Functors

A **functor** is a simpler construction than a [monad](lec-10-monad-and-parallel-stream.md#monad) in that it only ensures **lambdas can be applied sequentially to the value**, without worrying about side information.

{% hint style="info" %}
Recall that when we build our `Loggable<T>` abstraction, we add a [`map`](https://nus-cs2030s.github.io/2425-s2/35-logger.html#making-loggable-general) that only updates the value but changes nothing to the side information. One can think of a functor as an **abstraction that supports** `map`.
{% endhint %}

### Functor Laws

Let `Functor` be a type that is a functor and `functor` be an instance of it, a **functor** should following the following three laws,

{% stepper %}
{% step %}
**Identity Law**

`functor.map(x -> x)` is the same as `functor`
{% endstep %}

{% step %}
**Composition Law**

`functor.map(x -> f(x)).map(x -> g(x))` is the same as `functor.map(x -> g(f(x))`.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
Our classes from `cs2030s.fp`, `Lazy<T>`, `Maybe<T>`, and `InfiniteList<T>` are **functors** as well.
{% endhint %}

<details>

<summary>Some Tips about <strong>Monad and Functors</strong></summary>

1. For a class/type to be a Monad, it must have
   1. Two methods: `of` and `flatMap` which does their corresponding jobs respectively
   2. Adhere to the three [#monad-laws](lec-10-monad-and-parallel-stream.md#monad-laws "mention")
2. For a class/type to be a Functor, it must have
   1. One method: `map` which does its job
   2. Adfere to the two [#functor-laws](lec-10-monad-and-parallel-stream.md#functor-laws "mention")
3. A class/type can be **both** monad and functor!
4. Every **Monad is a Functor**, But not every **Functor is a Monad**.

</details>
