# Rec 07

## Problems

### 01. Infinite List

> 1. When returning/creating a new Infinite List, always think about how to come up with the **head** and the **tail**!
> 2. Know how to pass in a **curried lambda** when the transformer will transform a value into **another transformer**

As for point 1, look at the question a) and b) will be quite enough. For point 2, let's see the follwing example, which is question b)

{% code overflow="wrap" %}
```java
public <S, R> InfiniteList<R> zipWith(
  InfiniteList<? extends S> list,
  Transformer<? super T, ? extends Transformer<? super S, ? extends R>> mapper) {
}
```
{% endcode %}

This method `zipWith` 's second parameter is a transformer that transforms a value into **another transformer**. As such, when we pass a lambda in, we should pass a **curriend lambda**. For example,

```sh
jshell> InfiniteList<Integer> l1 = InfiniteList.iterate(0, i -> i + 1);;
jshell> InfiniteList<String> l2 = InfiniteList.iterate("", s -> s + "S");;
jshell> InfiniteList<Object> l3 = l1.zipWith(l2, i -> s -> i + s.length());
```

In the curried lambda, the `i` at the end is actually accessed via **variable capture.** To visualize it vividly, we can rewrite the **curried lambda** into the **anonymous class**

```java
new Transformer<Integer, Transformer<String, Integer>>() {
    @Override
    public Transformer<String, Integer> transform(Integer i) {
        return new Transformer<String, Integer>() {
            @Override
            public Integer transform(String s) {
                return i + s.length(); // <-- i is captured here!
            }
        };
    }
}
```

Now, we can use our **head** and **tail** thinking to implement the `zipWith` method

{% code overflow="wrap" %}
```java
public <S, R> InfiniteList<R> zipWith(
  InfiniteList<? extends S> list,
  Transformer<? super T, ? extends Transformer<? super S, ? extends R>> mapper) {
    return new InfiniteList<R> (
      () -> mapper.transform(this.head()).transform(list.head()),
      () -> this.tail().zipWith(list.tail(), mapper)
}
```
{% endcode %}

{% hint style="info" %}
A small trick is to look at the given sample code to decide when to use `this.head()/tail()` and when to use `list.head()/tail()`.
{% endhint %}

### 02. Stream Construction

> 1. `iterate()` is general enough so that we can use it to build a Stream whose elements is `Pair`! Include the example in the cheatsheet!
> 2. `map()` and `flatMap()` have different use cases
>    1. `map()` is usually used when each element in the previous Stream has a **one-to-one** relationship with the elements in the new Stream.
>    2. `flatMap()` is usally used when each element in the previous Stream has a **one-to-multiple** relationship with the elements in the new Stream.

For example, to create a Fibonacci Stream, we can use the following code

{% code overflow="wrap" %}
```java
Stream <BigInteger> fib(int n) {
  return Stream.iterate(new Pair<>(BigInteger.ONE, BigInteger.ONE) // seed
                        pair -> new Pair<>(pair.second, pair.first.add(pair.second))) // Trasformer
               .map(pair -> pair.first)
               .limit(n)
}
```
{% endcode %}

### 03. Stream Construction\*

> This question is awesome and it is worth to try again before Final/PE2! It vividly shows the difference between `flatMap()` and `map()`

Write a method `product` that takes in two `List` objects `list1` and `list2` , and produce a `Stream`\
containing elements combining each element from `list1` with evey element from `list2` using a given\
lambda expression of type `BiFunction` .

```java
<T, U, R> Stream<R> product(List<? extends T> list1,
    List<? extends U> list2,
    BiFunction<? super T, ? super U, ? extends R> func) {
  :
}
```

The sample jshell code is given below:

```sh
jshell> List<Integer> list1 = List.of(1, 2, 3, 4)
jshell> List<String> list2 = List.of("A", "B")
jshell> product(list1, list2, (i, s) -> i + s).forEach(System.out::println)
1A
1B
2A
2B
3A
3B
4A
4B
```

***

To do this kind of questions, determine our steps first!

1. Get the element of `list1`
2. Get the element of `list2`
3. Pass them to the `BiFunction` `func`.

After that, we have a brief idea that we may use `map()`/`flatMap()` to retrieve the elements. How to make sure which one to use? Let's look at the sample code,

1. After we retrieve the first element of `list1`, which is 1. For each element of `list2`, we use 1 and it to form **two** new elements in our new Stream. This is actually a **one-to-multiple** relationship! So, `flatMap()` should be used to retrieve the elements from `list1`
2. Then after retrieve element from `list2`, we find that it is a **one-to-one** relationship! So, `map()` should be used!

In the end, we have the final answer!

```java
list1.stream().flatMap(l1 -> list2.stream().map(l2 -> func.apply(l1, l2)));
```

### 04. Stream Construction\*

> 1. Include the Stream implementation of `isPrime()`/ `countPrimeFactors()` into cheatsheet!

{% tabs %}
{% tab title="isPrime()" %}
```java
boolean isPrime(int n) {
  return IntStream.range(2, n)
                  .noneMatch(x -> n % x == 0);
}
```
{% endtab %}

{% tab title="countPrimeFactors()" %}
{% code overflow="wrap" %}
```java
long countPrimeFactors(int n) {
    return IntStream
        .rangeClosed(2, n) // primes start from 2
        .filter(x -> (n % x == 0 && isPrime(x))) // use previous isPrime function
        .count();
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

Follow the systematetic steps, to solve the questions

1. Generate a continious stream
2. Map each value to its number of Prime Factors

Knowing this, our final solution will be

```java
Stream<Long> omega(int n) {
  return Stream.iterate(1, i -> i <= n, i -> i += 1)
               .map(x -> countPrimeFactors(x));
}
```

## Tips

1. When returning/creating a new Infinite List, always think about how to come up with the **head** and the **tail**!
2. Know how to pass in a **curried lambda** when the transformer will transform a value into **another transformer**
3. `iterate()` is general enough so that we can use it to build a Stream whose elements is `Pair`! Include the example in the cheatsheet!
4. `map()` and `flatMap()` have different use cases
   1. `map()` is usually used when each element in the previous Stream has a **one-to-one** relationship with the elements in the new Stream.
   2. `flatMap()` is usally used when each element in the previous Stream has a **one-to-multiple** relationship with the elements in the new Stream.
5. In **Stream Construction** problems, always be clear about what **are your steps** to solve the problem!
