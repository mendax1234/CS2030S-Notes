# Functional Interface

## CS2030S

In CS2030s, we have implemented a bunch of functional interfaces. I feel they are very important for the future topics, so I document them down here.

### `BooleanCondition<T>::test`

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

#### Explanation

Parameter Type is `T` and return type is `boolean`.

### `Producer<T>::produce`

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

#### Explanation

No parameter and return type is `T`.

#### Lambda Example



### `Consumer<T>::consume`

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

#### Explanation

Parameter type is `T` return type is `void`&#x20;

#### Lambda example

### `Transformer<U, T>::transform`

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

#### Explanation

Parameter type is `U`, return type is `T`. Can think it as transforming one value of type `U` into another value of type `T`.

#### Lambda Example

### `Combiner<S, T, R>::combine`

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

#### Explanation

Two parameters of type `S, T` respectively, return type is `R`.

#### Lambda Example
