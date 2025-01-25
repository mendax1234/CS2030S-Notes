# Lec  03 - Polymorphism

## Overloading

_Method overloading_ is when we have two or more methods:

1. in the same class
2. with the same name but a differeing method signature.

In other words, we create an overloaded method by changing the **type,** [**order**](#user-content-fn-1)[^1]**, and numbe**r of parameters of the method but keeping the method name identical.

### Difference between [Overriding](lec-02-class-instance-methods-inheritance/#overriding)

| Override | **must** have same [_method descriptor_](lec-02-class-instance-methods-inheritance/#method-signature-and-descriptor) |
| -------- | -------------------------------------------------------------------------------------------------------------------- |
| Overload | **must** have same method name and in the same class.                                                                |

{% hint style="info" %}
Note that in overloading, usually the **return type** of the method is the same because since the methods' name are the same, meaning that the method do the same thing.
{% endhint %}

## Method Invocation

"Thanks to" polymorphism and the [overriding](lec-02-class-instance-methods-inheritance/#overriding), we may find it hard to determine which method to invocate. To make this problem clear, let's introduce the mechanism of method invocation in Java.

Basically, the process can be divided into two parts:

1. [During Compile Time](lec-03-polymorphism.md#during-compile-time)
2. [During Run Time](lec-03-polymorphism.md#during-run-time)

### During Compile Time

**Goal**: To get the _method descriptor_ of the function we are going to invocate. Otherwise generate a compilation-error.

{% stepper %}
{% step %}
**Find the compile-time type target**

"Target" is an object. More specifically, with the invocation `curr.equals(obj)`, the "target" should be `curr`. Then the compile-time type of `curr`, let's denote it as $$C$$, is what we are interested in.

This compile-time type is actually a _class_, in which we need to **search for the methods** that have the **same name** with method name we want to invocate. In our case, we should find the method with name `equals()` in the class $$C$$.

{% hint style="info" %}
When doing the search, all the inherited methods from $$C$$'s supertypes/superclass are included in the search.
{% endhint %}
{% endstep %}

{% step %}
**Find the most specific method**

We may have several matching methods, and how do you decide which one's descriptor to get? Here, we use the **principle of "the most specific one"**.

Basically, we should first **pass in the arguments from our invocated method to the method we found during step 1.** If the arguments **cannot** be passed into the method we found, we move to the next available option. (If no more option is left, we will generate a compilation-error).

After trying all the options, if there is **only one matching method**, then we have found!

Otherwise, if there are **more than one methods** that satisfy our requirements, we can use the **principle of "the most specific one"** to return the method description of the **most specific one**.

> **The principle of "the most specific one"**: a method $$M$$ is more specific than method $$N$$ if the arguments to $$M$$ can be passed to $$N$$ without compilation error.

Basically, this means that the **type of the parameter in** $$M$$ is **subtype** of the **type of the parameter in** $$N$$. For example, `equals(Circle)` is **more specific than** `equals(Object)`.

{% hint style="info" %}
Note that after this step, if there are still more than one method that satisfy our requirements, than the compiler will **generate a compilation-error**.
{% endhint %}
{% endstep %}

{% step %}
**Pass the method description we found to run-time step**

As the name suggests, we will pass the _method descriptor_ to the next step.

{% hint style="info" %}
Note that in this step, we don't need to return any information about the class since it is not included in [_method descriptor_](lec-02-class-instance-methods-inheritance/#method-signature-and-descriptor)_._
{% endhint %}
{% endstep %}
{% endstepper %}

### During Run Time

**Goal:** Find the **only** method to execute.

{% stepper %}
{% step %}
**Retrieve the method descriptor**

As the name suggests, this step will retrieve the _method descriptor_ that is passed from the compile process above.
{% endstep %}

{% step %}
**Find the run-time type target**

Similar to the compile process, now we should find the **run-time type** of the target, let's say it's $$D$$ (If using the same example, $$D$$ should be the **subtype** of $$C$$).

This **run-time type** information tells us the class we should start to search from. Basically, from this class all the way up to the root class `Object`.
{% endstep %}

{% step %}
**Find a matching method descriptor**

Using the **retrieved method descriptor**, now we should find a method from the **run-time class** all the way up to the root class `Object` that has the **same descriptor** with our **retrieved method descriptor.**

{% hint style="info" %}
The **first** matching method will be executed.
{% endhint %}
{% endstep %}
{% endstepper %}

[^1]: "order" here means the **type** order! For example, changing from `double a, double b` to `double b, double a` is **not** considered as changing the order of the parameters.
