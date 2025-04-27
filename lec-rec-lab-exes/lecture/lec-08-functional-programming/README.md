# Lec 08 - Functional Programming

## Mathematical Functions

In maths, we have learned that **function** refers to a **mapping** from domain to codomain. Every input in the domain must map to exactly one output but multiple inputs can map to the same output.

Basically, mathematical functions have the following two advantages:

1. **No side effects**: This means given a function $$f$$, applying it on $$x$$, a.k.a $$f(x)$$ doesn't change the value of $$x$$ or any other unknowns $$y,z$$ etc. It simply computes and returns the value.
2. **Referential transparency**: This means if we let $$f(x)=a$$, then in every formula that $$f(x)$$ appears in, we can safely replace occurances of $$f(x)$$ with $$a$$. We can be guaranteed that the resulting formulas are still equivalent.

## Pure Functions

Ideally, functions in our program should behave the same as functions in mathematics. This requires us to treat **functions** as **first-class citizen** in our program, which means we can _assign functions to a variable, pass it as arguments, return a function from another function_, etc, **just like any other variable**.

{% hint style="info" %}
Now, in our program, our **first class citizen** is changed from **variable** to **function**.
{% endhint %}

Such a counterpart of mathematical functions in our program is called **pure functions**. This means that the pure functions also have the following two properties:

1. **No side effects**: This means our function **does not**
   1. print to the screen
   2. write to files
   3. throw exceptions
   4. change other variables
   5. modify the values of the arguments
2. **Deterministic**: This means that given the same input, the function must produce the same output, _every single time_. This deterministic property ensures **referential transparency**.

## Functional Programming

We refer to the style of programming where we build a program from **pure functions** as _functional programming (FP)_.

### Functions as First-class Citizen

We have already seen what this means from above. Now, let's recap an example **where we pass a function as an argument**.

{% code lineNumbers="true" %}
```java
void sortNames(List<String> names) {
  Comparator<String> cmp = new Comparator<String>() {
    public int compare(String s1, String s2) {
      return s1.length() - s2.length();
    }
  };
  names.sort(cmp);
}
```
{% endcode %}

In this code, the comparison function `cmp` here is implemented as a _method in an anonymous class_ that _implements an interface_. We can think of **an instance of this anonymous class** as the **function**. Since a function is now just an instance of an object in Java, we can pass it around, return it from a function, and assign it to a variable, just like any other reference type.

## Lambda Expression

While we have achieved functions as first-class citizens in Java, the code is verbose and ugly. Fortunately, there is a much cleaner syntax to write functions that applies to **interfaces with a single abstract method —** that's **lambda expression**.

A **lambda expression** is a shorthand syntax for implementing a **functional interface** (an interface with a single abstract method) in Java. It defines a function inline, specifying its parameters and body, without requiring a full method declaration or class definition.

{% hint style="success" %}
Such an interface with **exactly one abstract method** is called a **functional interface**. And a key advantage for a functional interface is that there is **no ambiguity about which method is being overridden** by an implementing subclass.
{% endhint %}

An example is as follows, notice that the Lambda Writing can be achieved by **removing the unnecessary part from** the anonymous class writing!

{% tabs %}
{% tab title="Anonymous Class" %}
{% code lineNumbers="true" %}
```java
Transformer<Integer, Integer> square = new Transformer<>() {
  @Override
  public Integer transform(Integer x) {
    return x * x;
  }
};
```
{% endcode %}
{% endtab %}

{% tab title="Lambda (Single Line)" %}
{% code lineNumbers="true" %}
```java
Transformer<Integer, Integer> square = x -> x * x;
```
{% endcode %}
{% endtab %}

{% tab title="Lambda  (Multiple Line)" %}
{% code lineNumbers="true" %}
```java
Transformer<Integer, Integer> complexSquare = x -> {
    System.out.println("Squaring the number: " + x);
    int result = x * x;
    return result;
};
```
{% endcode %}
{% endtab %}
{% endtabs %}

So, the expression above `x -> x * x` is called **lambda expression**.

1. The LHS lists the **parameters** (use `()` if there is no parameter)
2. The RHS is the **computation**.
   1. If RHS only has one line, you can omit curly `{}` braces and `return`.
   2. If RHS has multiple lines, use curly braces `{}` and explicitly write `return` if a value is returned.

{% hint style="success" %}
A **lambda expression** is essentially **syntactic sugar** for writing an **anonymous class** that implements a functional interface.
{% endhint %}

### Method Referencing

A lambda expression is useful for specifying a **new anonymous method**. Sometimes, we want to use an **existing method** as a first-class citizen instead. That is why it comes **method referencing**.

An example using the `distanceTo(Point p)` is shown as follows,

{% tabs %}
{% tab title="Lambda Expression Style" %}
{% code lineNumbers="true" %}
```java
Point origin = new Point(0, 0);
Transformer<Point, Double> dist = p -> origin.distanceTo(p);
```
{% endcode %}
{% endtab %}

{% tab title="Method Referencing Style" %}
{% code lineNumbers="true" %}
```java
Point origin = new Point(0, 0);
Transformer<Point, Double> dist = origin::distanceTo;
```
{% endcode %}
{% endtab %}
{% endtabs %}

The double-colon notation `::` is used to **specify a method reference**. We can use method references to refer to:

1. static methods in a class `Box::of`
2. instance method of a class or interface `x::compareTo`
3. constructor of a class `Box::new`

<pre class="language-java" data-line-numbers><code class="lang-java">Box::of            // x -> Box.of(x)
Box::new           // x -> new Box(x)
x::compareTo       // y -> x.compareTo(y)
<strong>A::foo             // (x, y) -> x.foo(y) or (x, y) -> A.foo(x,y)
</strong></code></pre>

However, in Line 4, we should be extremely careful because if there are multiple matches or if there is ambiguity in which method matches, the Java compiler will generate a **compile error**!

{% hint style="info" %}
1. In Method Referencing, if the thing before `::` is an **instance**, it **does not** need to be **effectively final**.
2. In `A::foo`, if `foo` is an **instance method**, it will use the **first** input as the **instance**, and pass the remaining **inputs** as arguments. Otherwise, it will call the **class** method and pass **all inputs** as arguments.
3. The **number of inputs** is decided in the **functional interface**'s abstract metho&#x64;**.** It is the same as the number of **parameters** in that abstract method.
{% endhint %}

## Curried Functions

Mathematically, a **function** takes in only **one value** and returns **one value**. In programming, however, we may write functions that take in more than one value. In FP, this can be achieved by using **curried functions.**

> **Currying** is the technique that translates a general _n-ary_ function to a sequence of _n_ **unary** functions.

{% hint style="success" %}
This utilizes the fact from the first-class citizen principle that **functions can be returned from a function**!
{% endhint %}

## Lazy Evaluation

The Lazy evaluation means that we only **invoke** the function when we truly needs to do so. This can be done in two ways:

1. Use Lambda as Delayed Data
2. Memoization

### Lambda as Delayed Data

Recall that when we write a lambda expression as follows:

{% code lineNumbers="true" %}
```java
Transformer<Integer, Integer> f = x -> x + 1;
```
{% endcode %}

We are just **defining function** $$f$$. To invoke the function $$f$$, we need an argument for `x`. So, nothing will happen here because we are just defining the function.

So, we can pass in the **lambda expression** to procrastinate our computation until we really need the data. For the example, please see from the [lecture notes](https://nus-cs2030s.github.io/2425-s2/32-lazy.html#lambda-as-delayed-data).

### Memoization

This is the same as what we haved encountered in CS1010 or some other data structures. So, the basic idea here is that if we have computed the value of a function before, we can cache (or memoize) the value, and keep it somewhere, so that we don't need to compute it again.
