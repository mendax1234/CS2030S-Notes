# Functional Interface

## CS2030S

In CS2030s, we have implemented a bunch of functional interfaces. I feel they are very important for the future topics, so I document them down here.

{% stepper %}
{% step %}
`BooleanCondition<T>::test`

{% code overflow="wrap" lineNumbers="true" %}
```java
package cs2030s.fp;

/**
 * Represent a conditional statement that returns either true of false.
 * CS2030S
 * AY23/24 Semester 2
 *
 * @param <T> The type of the variable to be tested with this conditional statement.
 */
@FunctionalInterface
public interface BooleanCondition<T> {
  /**
   * The functional method to test if the condition is true/false on the given value t.
   *
   * @param t The variable to test
   * @return The return value of the test.
   */
  boolean test(T t);
}
```
{% endcode %}

**Explanation**

Parameter Type is `T` and return type is `boolean`.

**Lambda Example**

This means whenever the method parameter is a `BooleanCondition<T>`, we can pass the following lambda expression as the method argument.

```java
BooleanCondition<Integer> isPositive = x -> x > 0;
```
{% endstep %}

{% step %}
`Producer<T>::produce`

{% code lineNumbers="true" %}
```java
package cs2030s.fp;

/**
 * Represent a function that produce a value.
 * CS2030S
 * AY23/24 Semester 2
 *
 * @param <T> The type of the value produced.
 */
@FunctionalInterface
public interface Producer<T> {
  /**
   * The functional method to produce a value.
   *
   * @return The value produced.
   */
  T produce();
}
```
{% endcode %}

**Explanation**

No parameter and return type is `T`.

**Lambda Example**

This means whenever the method parameter is a `Producer<T>`, we can pass the following lambda expression as the method argument.

```java
Producer<Double> randomValue = () -> Math.random();
```
{% endstep %}

{% step %}
`Consumer<T>::consume`

{% code lineNumbers="true" %}
```java
package cs2030s.fp;

/**
 * Represent a function that consumes a value.
 * CS2030S
 * AY23/24 Semester 2
 *
 * @param <T> The type of the value consumed.
 */
@FunctionalInterface
public interface Consumer<T> {
  /**
   * The functional method to consume a value.
   *
   * @param t The value consumed.
   */
  void consume(T t);
}
```
{% endcode %}

**Explanation**

Parameter type is `T` return type is `void`&#x20;

**Lambda example**

This means whenever the method parameter is a `Consumer<T>`, we can pass the following lambda expression as the method argument.

```java
Consumer<String> printUpperCase = s -> System.out.println(s.toUpperCase());
```
{% endstep %}

{% step %}
`Transformer<U, T>::transform`

{% code overflow="wrap" lineNumbers="true" %}
```java
package cs2030s.fp;

/**
 * Represent a function that transforms one value into another, possible of different types.
 * CS2030S
 * AY23/24 Semester 2
 *
 * @param <U> The type of the input value
 * @param <T> The type of the result value
 */
@FunctionalInterface
public interface Transformer<U, T> {
  /**
   * The function method to transform the value u.
   *
   * @param u The input value
   * @return The value after applying the given transformation on u.
   */
  T transform(U u);
}
```
{% endcode %}

**Explanation**

Parameter type is `U`, return type is `T`. Can think it as transforming one value of type `U` into another value of type `T`.

**Lambda Example**

This means whenever the method parameter is a `Transformer<T>`, we can pass the following lambda expression as the method argument.

```java
Transfomer<String, Integer> stringLength = s -> s.length();
```
{% endstep %}

{% step %}
`Combiner<S, T, R>::combine`

{% code lineNumbers="true" %}
```java
package cs2030s.fp;

/**
 * Represent a function that combines two values into one.  The two inputs
 * and the result can be of different types.
 * CS2030S
 * AY23/24 Semester 2
 *
 * @param <S> The type of the first input value
 * @param <T> The type of the second input value
 * @param <R> The type of the return value
 */
@FunctionalInterface
public interface Combiner<S, T, R> {
  /**
   * The function method to combines two values into one.
   *
   * @param s The first input value
   * @param t The second input value
   * @return The value after combining s and t.
   */
  R combine(S s, T t);
}

```
{% endcode %}

**Explanation**

Two parameters of type `S, T` respectively, return type is `R`.

**Lambda Example**

This means whenever the method parameter is a `Transformer<T>`, we can pass the following lambda expression as the method argument.

```java
Combiner<Integer, Integer, Integer> multiply = (a, b) -> a * b;
```
{% endstep %}
{% endstepper %}

## Java

Java provides its own version of functional interfaces that are comparable to ours, in the `java.util.function` package. The table below shows some commonly used ones:

| CS2030S                        | java.util.function                                                                                                                           |
| ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `BooleanCondition<T>::test`    | [`Predicate<T>::test`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/function/Predicate.html#test\(T\))             |
| `Producer<T>::produce`         | [`Supplier<T>::get`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/function/Supplier.html#get\(\))                  |
| `Consumer<T>::consume`         | [`Consumer<T>::accept`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/function/Consumer.html#accept\(T\))           |
| `Transformer<T, R>::transform` | [`Function<T, R>::apply`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/function/Function.html#apply\(T\))          |
| `Transformer<T, T>::transform` | [`UnaryOp<T>::apply`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/function/UnaryOperator.html)                    |
| `Combiner<S, T, R>::combine`   | [`BiFunction<S, T, R>::apply`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/function/BiFunction.html#apply\(T,U\)) |
