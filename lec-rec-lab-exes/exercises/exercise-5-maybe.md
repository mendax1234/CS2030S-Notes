# Exercise 5 - Maybe

## What is Maybe\<T>

Let's take the counter as an example.

```java
Counter c = bank.findCounter();
```

The `findCounter()` function is supposed to map from the bank (domain) to an available counter (codomain should be Counter!). However, if there is not available counter, it will return a `null`, which is not a Counter! This means that `findCounter()` is **not a pure function**!

To solve that, we can create a new Type called `Maybe<Counter>`, which basically extends the codomain to include the `null`. Thus, the `findCounter()` is now **a pure function**!

***

So, `Maybe<T>` is a **wrapper** for an item that **may or may not exist**. Its key purpose is to handle the possibility of missing values in a more elegant way than using `null` references.

And inside the `Maybe<T>`, it has two cases

1. case `None`: do nothiing, which represents the **absence** of a value
2. case `Some`: actually do the thing, which represents the **presence** of a value (call be `null`)

These two cases are the class internals and are invisible to the users!

## Main methods explanation

{% stepper %}
{% step %}
`get()`

<pre class="language-java" data-line-numbers><code class="lang-java"><strong>// Return the wrapped value
</strong><strong>// Or Maybe.NONE if
</strong><strong>//   1. It is invoked from Maybe.NONE
</strong><strong>protected abstract T get();
</strong></code></pre>

**Implementation in each case**

{% tabs %}
{% tab title="None" %}
{% code lineNumbers="true" %}
```java
@Override
protected Object get() {
  throw new NoSuchElementException();
}
```
{% endcode %}
{% endtab %}

{% tab title="Some<T>" %}
{% code lineNumbers="true" %}
```java
@Override
protected T get() {
  return this.content;
}
```
{% endcode %}
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
`filter()`

{% code lineNumbers="true" %}
```java
// Returns Maybe.none() if 
//   1. it is invoked from Maybe.NONE
//   2. the content is null
//   3. the content didn't pass the test
// Otherwise, return the Maybe(Wrapper) itself
public abstract Maybe<T> filter(BooleanCondition<? super T> c);
```
{% endcode %}

**Implementation in each case**

{% tabs %}
{% tab title="None" %}
```java
@Override
public Maybe<Object> filter(BooleanCondition<? super Object> c) {
  return Maybe.none();
}
```
{% endtab %}

{% tab title="Some<T>" %}
```java
  @Override
  public Maybe<T> filter(BooleanCondition<? super T> c) {
    if (this.content != null && !c.test(this.content)) {
      return Maybe.none();
    }
    return this;
  }
```
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
`map()`

{% code lineNumbers="true" %}
```java
// Return Maybe.none() if
//   1. It is invoked from Maybe.NONE
// Otherwise, return the wrapper of the value after transformation
public abstract <U> Maybe<U> map(Transformer<? super T, ? extends U> t);
```
{% endcode %}

**Implementation in each case**

{% tabs %}
{% tab title="None" %}
{% code lineNumbers="true" %}
```java
@Override
public <U> Maybe<U> map(Transformer<? super Object, ? extends U> t) {
  return Maybe.none();
}
```
{% endcode %}
{% endtab %}

{% tab title="Some<T>" %}
{% code lineNumbers="true" %}
```java
@Override
public <U> Maybe<U> map(Transformer<? super T, ? extends U> t) {
  return Maybe.some(t.transform(this.content));
}
```
{% endcode %}
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
`flatMap()`

{% code overflow="wrap" lineNumbers="true" %}
```java
// Return Maybe.none() if
//   1. it is invoked from Maybe.NONE
// Otherwise, return value after transformation (in this case we explicitly specify it as a Wrapper)
public abstract <U> Maybe<U> flatMap(Transformer<? super T, ? extends Maybe<? extends U>> t);
```
{% endcode %}

**Implementation in each case**

{% tabs %}
{% tab title="None" %}
{% code overflow="wrap" lineNumbers="true" %}
```java
@Override
public <U> Maybe<U> flatMap(Transformer<? super Object, ? extends Maybe<? extends U>> t) {
  return Maybe.none();
}
```
{% endcode %}
{% endtab %}

{% tab title="Some<T>" %}
{% code overflow="wrap" lineNumbers="true" %}
```java
  @Override
  public <U> Maybe<U> flatMap(Transformer<? super T, ? extends Maybe<? extends U>> t) {
    @SuppressWarnings("unchecked")
    Maybe<U> m = (Maybe<U>) t.transform(this.content);
    return m;
  }
```
{% endcode %}
{% endtab %}
{% endtabs %}

**Application**

1. `flatMap()` can technically achieve everything that `map()` can achieve. But the reverse is not true because map always add **Wrapper** after transforming.
2. In `flatMap()`, the transformer will always output a `Maybe` or `Lazy` or whatever class it is under. (See [Optional::flatMap](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Optional.html#flatMap\(java.util.function.Function\)) in Java)
{% endstep %}

{% step %}
`orElse()`

{% code lineNumbers="true" %}
```java
// Return the alternative value t provided as parameter, if
//   1. it is invoked from Maybe.NONE
// Otherwise, return the content that is in the Wrapper
public abstract T orElse(T t);
```
{% endcode %}

**Implementation in each case**

{% tabs %}
{% tab title="None" %}
{% code lineNumbers="true" %}
```java
@Override
public Object orElse(Object o) {
  return o;
}
```
{% endcode %}
{% endtab %}

{% tab title="Some<T>" %}
{% code lineNumbers="true" %}
```java
@Override
public T orElse(T t) {
  return this.content;
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

**Application**

1. Remember that this method will always give you the either **content** of the wrapper (See [#lab-sheet](../lab/lab-05.md#lab-sheet "mention") Q3) or the parameter. No wrapper is returned!
2. This is usually used at the last of your function chain because its return type may not be a Maybe, so we cannot chain anymore!
{% endstep %}

{% step %}
`orElseGet()`

{% code overflow="wrap" lineNumbers="true" %}
```java
// Return the value produced by calling produce() on the provided Producer, if
//   1. It is invoked from Maybe.NONE
// Otherwise, return the content that is in the Wrapper
public abstract T orElseGet(Producer<? extends T> p);
```
{% endcode %}

**Implementation in each case**

{% tabs %}
{% tab title="None" %}
{% code lineNumbers="true" %}
```java
@Override
public Object orElseGet(Producer<? extends Object> p) {
  return p.produce();
}
```
{% endcode %}
{% endtab %}

{% tab title="Some<T>" %}
{% code lineNumbers="true" %}
```java
@Override
public T orElseGet(Producer<? extends T> p) {
  return this.content;
}
```
{% endcode %}
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
`ifPresent()`

{% code overflow="wrap" lineNumbers="true" %}
```java
// Do nothing and return, if
//   1. it is invoked from Maybe.NONE
// Otherwise, do something by executing the consumer's consume() method with the content as argument
public abstract void ifPresent(Consumer<? super T> c);
```
{% endcode %}

**Implementation in each case**

{% tabs %}
{% tab title="None" %}
{% code lineNumbers="true" %}
```java
@Override
public void ifPresent(Consumer<? super Object> c) {
  return;
}
```
{% endcode %}
{% endtab %}

{% tab title="Some<T>" %}
{% code lineNumbers="true" %}
```java
@Override
public void ifPresent(Consumer<? super T> c) {
  c.consume(this.content);
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

**Application**

1. This is usually used to replace the `(... ≠ null)` check. See [#lab-sheet](../lab/lab-05.md#lab-sheet "mention") Q2.
{% endstep %}

{% step %}
`of()`

{% code lineNumbers="true" %}
```java
// Factory method used to create a Maybe(Wrapper)
public static <T> Maybe<T> of(T t) {
  if (t == null) {
    return Maybe.none();
  }
  return Maybe.some(t);
}
```
{% endcode %}

**Implementation in each case**

This method will call the factory method of `None` and `Some<T>`

{% tabs %}
{% tab title="None" %}
{% code lineNumbers="true" %}
```java
public static <T> Maybe<T> none() {
  @SuppressWarnings("unchecked")
  Maybe<T> none = (Maybe<T>) NONE;
  return none;
}
```
{% endcode %}
{% endtab %}

{% tab title="Some<T>" %}
{% code lineNumbers="true" %}
```java
public static <T> Maybe<T> some(T t) {
  return new Some<T>(t);
}
```
{% endcode %}
{% endtab %}
{% endtabs %}
{% endstep %}
{% endstepper %}

## Single return statement

The major purpose of using functional programming in this course is to let you rewrite methods into one single statment. So, how we can use the basic idea of FP to understand / read a single return statement?

> The fundamental idea of FP: In FP, **functions** are treated as the **first-class citizen**.

{% code lineNumbers="true" %}
```java
return Maybe.of(map.get(student))
    .flatMap(m -> Maybe.of(m.get(module)))
    .flatMap(a -> Maybe.of(a.get(assement)))
    .orElse("No such entry");
```
{% endcode %}

1. Line 1, `Maybe.of` is actually creating the argument that will be passed all the way down.
2. Line 2, `.flatMap()` takes in a lambda as an expression and itself (`.flatMap()`) is a function that we are going to apply on the target[^1]. This function will return another "mutated" instance for further operation. The return type of the function is defined in the declaration.
   1. Inside the lambda expression, it explicitly sets the `Transformer t` .
   2. How the transformer or a.k.a the parameter works is that the **input** is the L.H.S, which in CS2030S's Maybe should be the **value** in the previous wrapper. The **output** of the lambda is the R.H.S
   3. And how the **output** is being processed is dependent on the **outter method**, e.g. `flatMap()` will just return the **output**, `map()` will add a wrapper around the output. `filter()` will use the output to implement the checking, etc.
3. Line 3 and 4 is similar.

You can think the Line 1 as creating a naked man and Line 2,3,4 are actually adding layers to the man (From my tutor :joy:)

## Tips

1. In 2030s's Maybe, for every lambda expression passed into the API of maybe, the L.H.S is always the content of the previous Maybe

[^1]: the calling object, the object before `.`
