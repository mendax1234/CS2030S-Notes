# Lab 01 - Logistics, Introduction to OOP, Exercise 0

## Introduction

### Expectation from labs

Our focus:

* **generalisable** design principles;
* techniques for writing **good, extensible and easy-to-maintain** programs;
* coding practices to ensure **clean** structures;
* **reasoning and rationale** behind design decisions

### Suvivor's Tips

* Unless you are a very proficient programmer, try to **design your solution** in pseudocode before coding it.
* **Don's ask for help immediately** when you come across a bug or get stuck. Try to analyse the problem independently first.
* **Ask Google instead of ChatGPT** to train yourself in extracting key information from your questions.
* **Use Ed** wisely both to pose your questions and to spy on other people for hints on programming problems.
* **Make your code work** before trying to improve it.

## Exercise 0 and OOP

### Encapsulation

**Motivation:** the use case to represent **compound data**.

Encapsulation is basically a **modelling** process, which means we pack all the data we needed into a particular type.

### Information Hiding

**Motivation**: Retrieving all data and perform the operations openly is **verbose and insecure**.&#x20;

Thus, the `object` should require an external party to **query** for the data from itself.

#### Common practices

1. In principle, _instance fields_ should be declared as `private` as much as possible.
2. The idea of _abstraction_: Users only need to think at a higher level when using a method, and don't need to bother how it actually works.

### Tell, Don't Ask

Recall that in _information hiding_, we **seal** all data of an object within the object itself and use **queries** to obtain desired data. Now in "Tell, don't ask", this becomes:

> we never **ask** an object to spit out its own **raw data.** Instead, we **let the object know** what we want so that it can give us a piece of **processed data** (via an _instance method_)

And this is called the principle of "Tell, Don't Ask".

### Inheritance

#### Type

In Math, we have the following definition for data type:

> Let $$\phi$$ be a **property**, then $$Y:=\{y: \phi (y)\}$$ is the **set of all objects satisfying the property** $$\phi$$. This set $$Y$$ can be considered as a **data type**.

For example, `char` data type means the set of integers which fall into the ASCII code representation.

***

Use the idea of **type** to understand the _inheritance_, we will have: If $$X$$ inherits $$Y$$, then there is some **additional property** $$\theta$$ such that $$X=\{x:\phi(x)\text{ and }\theta(x)\}$$.

From this deduction, we can clearly see that $$X\subseteq Y$$, so [**subtype**](lab-01-logistics-introduction-to-oop-exercise-0.md#subtype-relation) is nothing but a **subset**.

### Subtype relation

**Easy check**: if "$$X$$ is $$Y$$" makes sense, then probably $$X<:Y$$. If $$X$$ and $$Y$$ are classes, we can deduce that $$X$$ **inherits** from $$Y$$.&#x20;

{% hint style="info" %}
In algebra, if $$a\nless b\text{ and } b\nless a$$, that means $$a=b$$. But the same thing **cannot be applied on** two types $$S$$ and $$T$$. One easy explanation is that $$S$$ and $$T$$ can be two totally unrelated types. The more math-related explanation is because subtyping is not a **total order** over all data types.
{% endhint %}

### Composition

_Inheritance_ does not apply under every case, sometimes the relationship between two class is not an "is a", but a "has a". For the later kind of relationship, we have _composition_.

**Easy check:** if "$$X$$ has an $$Y$$” makes sense, then probably $$X$$ should contain a field of type $$Y$$. Such design leads to **component-based architecture**.

#### Essence of Recursion?

A field of class can be of the same type as the class itself.

{% code lineNumbers="true" %}
```java
class X {
    private X x;
    private Object object;
    
    public X(X x, Object object) {
        this.x = x;
        this.object = object;
    }
}
```
{% endcode %}

Note that inside class `X`, we can access the `private` field of the object from class `X`. This has appeared in the [Diagnostic quiz](../lecture/lec-02-class-instance-methods-inheritance/diagnostic-quiz.md#q2.-more-on-private) also.

### Class Fields and Class Methods

* A **class field** should be something that is **shared among all instances** of that class.
* A **class method** performs a task which **doesn’t need to access the data from any instance**.

#### Examples

1. All [`RandomPoint`](lab-01-logistics-introduction-to-oop-exercise-0.md#exercise-0-and-oop) instances use the same random number generator.
2. A **counter** to keep track of the total number of instances present for a certain class. (Usage: optimise object pooling in software/games.)
3. Store `static` references to important objects which are frequently used at all levels of a program.

## Before you code, plan first

Think through the program to decide:

* What objects do we need (Nouns)?
* What properties should each object encapsulate (Attributes)?
* What tasks can each object perform (Verbs)?

Establish a clear structure of the program first. **Draw a picture or diagram** to visualise!

{% hint style="info" %}
CS2030S is not a course about algorithms! The most complex algorithm you may encounter may be **linear search**. But, what often makes students in trouble is that **they don't plan first**!
{% endhint %}
