# Rec 03

Question (without answers):

{% file src="../../.gitbook/assets/ps3.pdf" %}

Answer:

{% file src="../../.gitbook/assets/ps3-comments.pdf" %}

## Problems

### 01. LSP with Exception Handling

The tricky point lies in **understanding the question**. What the question wants to ask is that: By LSP, we should be able to substitute `A` in the method `bar` with a `B` . After that, nothing more to mention about this problem.

### 02. Type Parameter with Two Bounds

In this question, we have encountered an interesting question, that is our type parameters has **two** bounds! For example, `<T extends Number & Comparable<T>>`. So, what will happen during the **Type Erasure**?

The trick is that the type erasure process erases `T` to be **the first bound**, which in this case is `Number`. It then **type casts** the variable to `Comparable` so that `compareTo` can be called.

### 03. Bridge Method\*

Suppose we have the following code,

{% code lineNumbers="true" %}
```java
class A<T> {
    public void fun(T x) {
        System.out.println("A");
    }
}
class B extends A<String> {
    public void fun(String i) {
        System.out.println("B");
    }
}
```
{% endcode %}

The code after type erasure will becomre

<pre class="language-java" data-line-numbers><code class="lang-java">class A {
    public void fun(Object o) {
        System.out.println("A");
    }
}
class B extends A {
    public void fun(Object o) { // Bridge method
        this.fun((String) o);
    }
    
    public void fun(String i) {
<strong>        System.out.println("B");
</strong>}
</code></pre>

Line 7 to 9 is called **the Bridge Method**.

> A bridge method is **always generated** when
>
> 1. a type `extends`/`implement`s a **parameterized type** and
> 2. **type erasure** changes the **signature of one or more inherited method**.

So, actually, this code is neiter overriding nor overloading.

***

Now, if you use the following code

{% code lineNumbers="true" %}
```java
A<String> a = new B();
a.fun("2");
```
{% endcode %}

It will print out `B`.&#x20;

Although this may seem intuitive, the actual behavior is more subtle due to generics and type erasure. During compilation, the compiler examines class `A` (the compile-time type of the target) to determine which methods can be invoked. Since there is only one such method, the erased method descriptor `void fun(Object)` is stored.

At runtime, Java checks class `B` (the runtime type of the target) for a method matching this descriptor. It finds the **bridge method** `void fun(Object)` in `B` and invokes it. The bridge method then calls `B::fun(String)` , which prints "B".

## Tips

1. **Bounded type parameters with multiple bounds:** If **multiple bounds** exist (e.g., `<T extends SomeClass & SomeInterface>`), `T` will be erased the **first bound**, and then it is **type casted** to the **second bound**. **Note** that the **first bound** must be a **class**! It **cannot** be an `interface`, otherwise, a **compile error** will be generated!
2. **Overrloading and Overriding**:
   1. Overloading just needs the **method name** to be the **same**, but different **method signature**!
   2. Start to determine whether it is overloading or overriding **after type erasure**.
3.  **Bridge method**: A bridge method is **always generated** when

    1. a type `extends`/`implement`s a **parameterized type** and
    2. **type erasure** changes the **signature of one or more inherited method**.

    (Include the code for the bridge method in the cheatsheet!)
