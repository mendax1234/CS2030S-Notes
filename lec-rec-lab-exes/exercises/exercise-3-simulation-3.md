# Exercise 3 - Simulation 3

## OOP Design

This exercise is built on [exercise-2-simulation-2.md](exercise-2-simulation-2.md "mention"). And the following are the tasks you should do:

{% stepper %}
{% step %}
#### Make `Queue` a generic class <a href="#id-1-make-queue-a-generic-class" id="id-1-make-queue-a-generic-class"></a>

This means that you should define your `Queue` with the generic type parameter, which means that the elements in the queue should be of type `T`.

{% code lineNumbers="true" %}
```java
public class Queue<T> {
    ...
}
```
{% endcode %}

Since the queue is supposed to store an array of elements with type `T`. Inside the class constructor, after making sure we **can** only add element of type `T` into the queue, we should do the following

{% code lineNumbers="true" %}
```java
public Queue(int size) {
    @SuppressWarnings("unchecked")
    T[] items = (T[]) new Object[size];
    ...
}
```
{% endcode %}

{% hint style="info" %}
Here, `Object` is used in Line 3 because after type-erasure, `T` will be replaced by `Object`. For more information, see [#type-erasure](../lecture/lec-05-generics/#type-erasure "mention") from Lec 05.
{% endhint %}
{% endstep %}

{% step %}
#### Create a generic `Seq<T>` class <a href="#id-2-create-a-generic-seqt-class" id="id-2-create-a-generic-seqt-class"></a>

Since in this exercise, we want to further make our code generalizable. That is, we don't want to use `Counter[] counters` in exercise 2 to explicitly state that bank has counters. Why not we define our own comparable "array" with a generic type parameter? To do so, we should define `Seq` as follows,

{% code lineNumbers="true" %}
```java
class Seq<T extends Comparable<T>> {
    ...
}
```
{% endcode %}

Similarly, we want our `Seq` to store an array of comparable elements. Thus, in our constructor, after we make sure that we **can** only add element of type `T` that is comparable into the queue, we should do the following,

{% code lineNumbers="true" %}
```java
public Seq (int size) {
    @SuppressWarnings({"rawtypes", "unchecked"})
    T[] a = (T[]) new Comparable[size];
    ...
}
```
{% endcode %}

{% hint style="info" %}
Here, the `Comparable` in Line 3 is used because after type-erasure, `T` will be replaced by `Comparable`. And notice that `Comparable` itself is a generic type, so actually we are facing two potential problems here. 1) rawtype and 2)unchecked. To solve it, we need to suppress these two potential errors.
{% endhint %}
{% endstep %}

{% step %}
#### Make `Counter` Comparable to Itself <a href="#id-3-make-your-bankcounter-comparable-to-itself" id="id-3-make-your-bankcounter-comparable-to-itself"></a>

As we want to use `Seq<T extends Comparable<T>>` to repalce the `Counter[]` in our bank, we type `T` we pass in must be comparable! That is to say, our `Counter` class must `implement` the `Comparable<Counter>`!

{% code lineNumbers="true" %}
```java
class Counter implements Comparable<Counter> {
    ...
}
```
{% endcode %}

This is to make `Counter` **comparable**!
{% endstep %}

{% step %}
#### Update Simulation <a href="#id-4-update-your-simulation" id="id-4-update-your-simulation"></a>

The last step is to update the simulation workflow accordingly.&#x20;

{% hint style="info" %}
The shortest but perhaps the most tedious part of this exercise. :joy:
{% endhint %}
{% endstep %}
{% endstepper %}

### Tips

{% stepper %}
{% step %}
**Initialize the generic type**

In this exercise, one thing that may be confused is that, `Queue` and `Seq` actually **are not** arrays! So, to initialize them, we can simply use

{% code lineNumbers="true" %}
```java
Queue<Customer> q = new Queue<Customer>(size);
Seq<Counter> s = new Seq<Counter>(size);
```
{% endcode %}

This doesn't violate the principle that "generic types and arrays cannot mix". Essentially, it's just we have dealt with this problem inside the `Queue` and `Seq` class.
{% endstep %}
{% endstepper %}
