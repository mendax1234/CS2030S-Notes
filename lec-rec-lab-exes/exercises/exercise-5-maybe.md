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

Main operations provided:

### get

<pre class="language-java" data-line-numbers><code class="lang-java"><strong>// Return the wrapped value
</strong><strong>// Or Maybe.NONE if
</strong><strong>//   1. It is invoked from Maybe.NONE
</strong><strong>protected abstract T get();
</strong></code></pre>

Implementation in each case

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

### filter

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

Implementation in each case

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

### map

{% code lineNumbers="true" %}
```java
// Return Maybe.none() if
//   1. It is invoked from Maybe.NONE
// Otherwise, return the wrapper of the value after transformation
public abstract <U> Maybe<U> map(Transformer<? super T, ? extends U> t);
```
{% endcode %}

Implementation in each case

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

### flatmap

{% code overflow="wrap" lineNumbers="true" %}
```java
// Return Maybe.none() if
//   1. it is invoked from Maybe.NONE
// Otherwise, return value after transformation (in this case we explicitly specify it as a Wrapper)
public abstract <U> Maybe<U> flatMap(Transformer<? super T, ? extends Maybe<? extends U>> t);
```
{% endcode %}

Implementation in each case

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

### orElse

{% code lineNumbers="true" %}
```java
// Return the alternative value t provided as parameter, if
//   1. it is invoked from Maybe.NONE
// Otherwise, return the content that is in the Wrapper
public abstract T orElse(T t);
```
{% endcode %}

#### Implementation

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

#### Application

1. Remember that this method will always give you the either [**content**](#user-content-fn-1)[^1] of the wrapper (See [#lab-sheet](../lab/lab-05.md#lab-sheet "mention") Q3) or the parameter. No wrapper is returned!
2. This is usually used at the last of your function chain because its return type may not be a Maybe, so we cannot chain anymore!

### orElseGet

{% code lineNumbers="true" %}
```java
// Return the value produced by calling produce() on the provided Producer, if
//   1. It is invoked from Maybe.NONE
// Otherwise, return the content that is in the Wrapper
public abstract T orElseGet(Producer<? extends T> p);
```
{% endcode %}

Implementation in each case

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

### ifPresent

{% code overflow="wrap" lineNumbers="true" %}
```java
// Do nothing and return, if
//   1. it is invoked from Maybe.NONE
// Otherwise, do something by executing the consumer's consume() method with the content as argument
public abstract void ifPresent(Consumer<? super T> c);
```
{% endcode %}

#### Implementation

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

#### Application

1. This is usually used to replace the `(... ≠ null)` check. See [#lab-sheet](../lab/lab-05.md#lab-sheet "mention") Q2.

### of

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

This method will call the factory method of None and Some\<T>

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

## Single return statement

The major purpose of using functional programming in this course is to let you rewrite methods into one single statment. So, how are we using the basic idea of FP to understand / read a single return statement?

> The fundamental idea of FP: In FP, **functions** are treated as the **first-class citizen**.

{% code lineNumbers="true" %}
```java
return Maybe.of(map.get(student))
    .flatMap(m -> Maybe.of(m.get(module)))
    .flatMap(a -> Maybe.of(m.get(assement)))
    .orElse("No such entry");
```
{% endcode %}

1. Line 1, `Maybe.of` is actually creating the argument that will be passed all the way down.
2. Line 2, `.flatMap()` takes in a lambda as an expression and itself (`.flatMap()`) is a function that we are going to apply on the target[^2]. This function will return another "mutatbed" instance for further operation. The return type of the function is defined in the declaration.
3. Line 3 and 4 is similar.

[^1]: 

[^2]: the calling object, the object before `.`
