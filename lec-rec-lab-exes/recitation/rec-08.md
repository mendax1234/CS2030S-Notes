# Rec 08

## Problems

### 01. Monad and Functors

> Every **Monad** is a **Functor**! Since it is generalisable, include the implementation of `map()` using `of()` and `flatMap()` into the cheatsheet.

The question a) actually proves that given a Monad, we can use its `flatMap()` and `of()` to build a `map()` that satisfies the functor laws.

For example, this is our setup code

{% code overflow="wrap" %}
```java
import cs2030s.fp.Transformer;

class Monad<T> {
    private T x;

    private Monad(T x) {
        this.x = x;
    }

    public static <T> Monad<T> of(T x) {
        return new Monad<>(x);
    }

    public T get() {
        return x;
    }

    public <R> Monad<R> flatMap(Transformer<? super T, ? extends Monad<? extends R>> f) {
        return new Monad<>(f.transform(this.x).get());
    }
}
```
{% endcode %}

Our `map()` can be easily implemented as follows

```java
public <R> Monad<R> map(Transformer<? super T, ? extends R> f) {
    return flatMap(x -> Monad.of(f.transform(x)));
}
```

**Proof of Functor Laws — Composition Law**

> 1. When using whatever monad/functor law, find the **part of the expression** that you are using that law, and replace it with the output of that law.
> 2. To find the ultimate proof, try comparing **what you want** with **what you have now**. And then slowly slowly prove it.

* Suppose we have an instance of `Monad<T>` called `m` and two functions `f` and `g`.
* `m.map(x -> f(x)).map(x -> g(x))` is equivalent to the following Expression A based on our implementation,
  * Exppression A: `m.flatMap(x -> Monad.of(f(x))).flatMap(x -> Monad.of(g(x)))`
* Invoking Monad's **Associative Law** on the whole expression, Expression A is equivalent to Expression B below
  * Expression B: `m.flatMap(x -> Monad.of(f(x)).flatMap(x -> Monad.of(g(x))))`
* Invoking Monad's **Left Identity Law** on `Monad.of(f(x)).flatMap(x -> Monad.of(g(x)))`, this part is equivalent to `Monad.of(g(f(x)))`. Thus, Expression B is equivalent to Expression C below
  * Expression C: `m.flatMap(x -> Monad.of(g(f(x))))`
* In our implementation, Expression C is equivalent to `m.map(x -> g(f(x)))`
* Thus, the **composition** of functions is preserved in our implementation.

**Proof of Functor Laws — Identity Law**

Suppose we have an instance of `Monad<T>` called `m`. "What we want" is `m.map(x -> x)` = `m`. So, we start from `m.map(x -> x)` and slowly derive that it is ultimately equivalent to `m`.

* Using our implementation, `m.map(x -> x)` is equivalent to `m.flatMap(x -> Monad.of(x))`.
* By invoking Monad's **Right Identity Law** on the whole expression, we find that it is equivalent to the expression below
  * `m`
* This is equivalent to "what we want". Thus, the **identity** law is preserved!

### 02. Parallel Stream

> Calling a 3-parameter `reduce()` on a normal stream (not parallelized), we just do the normal reducing method. If same `reduce()` is called on a parallelized stream (e.g., `stream().parallel()`), we **cannot** decide the final output.

## Tips

1. Every **Monad** is a **Functor**! Since it is generalisable, include the implementation of `map()` using `of()` and `flatMap()` into the cheatsheet.
2. When using whatever monad/functor law, find the **part of the expression** that you are using that law, and replace it with the output of that law.
3. To find the ultimate proof, try comparing **what you want** with **what you have now**. And then slowly slowly prove it.
