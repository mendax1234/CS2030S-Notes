# Exercise 4 - Box

## The Basics

### `Box::equals(Object obj)`

> Two boxes are the same if the content of the box equals each other, as decided by their respective `equals` method.

The problem arose here is how to cast `obj` from `Object` to a Box, which is a generic type. The implementatino is as follows,

{% code lineNumbers="true" %}
```java
@Override
public boolean equals(Object obj) {
  if (obj instanceof Box<?>) {
    Box<?> box = (Box<?>) obj;
    return this.content.equals(box.content);
  }
  return false;
}
```
{% endcode %}

In Line 3, we check whether `obj`'s runtime type is a subtype of any `Box`, (here we should use `Box<?>` to avoid using rawtype). If so, we can than use `Box<?>` to cast `obj` to a `Box` and then access its content.

### Factory method

The factory method enables us to make our constructor `private`. For example, the factory method `of` for our `Box<T>` should be

{% code lineNumbers="true" %}
```java
public static <T> Box<T> of(T obj) {
  if (obj == null) {
    return null;
  }
  return new Box<T>(obj);
}
```
{% endcode %}

{% hint style="info" %}
Note that the type parameter `T` in the factory method is different from the type parameter `T` in the generic type `Box<T>`. But the core idea here is that we will return a `Box<T>` object, where `T` is determined during the call of this factory method `Box.of`.
{% endhint %}

## An Empty Box

### `static` field of same generic type in a generic type

As we have seen that in generics, we **cannot** use class-level type parameters in `static` field or `static` method. So, here, to define a `static` field with type `Box`, we can

1. use unbounded wildcard for generalizability
2. specify a specific type, like `Box<String>` (but this is not generalizable and thus not recommended)

```java
private static final Box<?> EMPTY_BOX = new Box<>(null);
```

{% hint style="info" %}
Java doesn't have field-leve type parameter!
{% endhint %}

### PECS

{% code lineNumbers="true" %}
```java
public Box<T> filter(BooleanCondition<? super T> condition) {
  return this.isPresent() && condition.test(this.content) ? this : empty();
}
```
{% endcode %}

For the filter function, the parameter is `BooleanCondition`, we call the `test` function to test the content of the Box, thus here the parameter is a **consumer**, meaning that we should use lower-bounded wildcards so that any `BooleanCondition` that is a supertype of the type of the content in the box, which is `T`, should be able to be tested!

## Implement Your Own Conditions <a href="#implement-your-own-conditions" id="implement-your-own-conditions"></a>

### `extends` or `implement` a generic type - 1

This tells us that we must **instantiate** our generic type when we try to `extends` or `implements` a generic type. See more from [#instantiate-a-generic-type](../lecture/lec-05-generics/#instantiate-a-generic-type "mention").

{% code lineNumbers="true" %}
```java
class DivisibleBy implements BooleanCondition<Integer> {
  private int num;

  public DivisibleBy(int num) {
    this.num = num;
  }

  @Override
  public boolean test(Integer num) {
    return num % this.num == 0 ? true : false;
  }
}
```
{% endcode %}

## Transforming a Box <a href="#transforming-a-box" id="transforming-a-box"></a>

### Declare a type parameter in a non-static method

{% code lineNumbers="true" %}
```java
public <U> Box<U> map(Transformer<? super T, U> transformer) {
  if (this.isPresent()) {
    return Box.of(transformer.transform(this.content));
  }
  return Box.empty();
}
```
{% endcode %}

Similarly, the `transformer` here is a **consumer**! And to return a `Box<U>`, we need to define a type parameter in the non-static method `map` so that we can return a `Box<U>`.

## Box in a Box

### `extends` or `implement` a generic type - 1h

This actually practices the [**third way**](../lecture/lec-05-generics/#instantiate-a-generic-type) to **parameterize** a generic type.

{% code lineNumbers="true" %}
```java
class BoxIt<T> implements Transformer<T, Box<T>> {
  @Override
  public Box<T> transform(T t) {
    return Box.of(t);
  }
}
```
{% endcode %}

In this way, we pass the type parameter `T` from `BoxIt` to `Transformer<T, B<T>>`, so in `Transformer<T, B<T>>` the `T` can be considered as the type argument!
