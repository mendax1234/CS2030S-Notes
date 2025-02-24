# Lab 02 - Interface,Wrapper Class, Exercise 1,2

## Exercise 1

### General Steps for Modelling with OO Paradigm

1. Identify the **nouns** (these tell what classes you need).
2. Set up the **relationship** between the classes. (composition or inheritance or unrelated)
3. Identify the **properties** and/or **data** needed to accurately describe the objects identified in Step 1.
4. Identify the **functionalities** and **bahaviour** of each class, i.e. what does this class do? (these tell you the methods for each class)

> How to determine the **fields** and **methods** a class should have?

#### Single Responsibility Principle

The principles states that:

> Each class should **only be responsible for doing one single thing**.

***

#### Some Tips

1. ID should be described as `String`, since it is safer.
2. Setters and getters should be used only as a last resort!
3. fluent syntax (when you write your methods, it looks like you are talking to someone). An example is a String Builder class in Java.

### Polymorphism

All the subclasses share the same functionality, but the specific outcome of running this functionality varies based on their own implementations.

{% hint style="info" %}
Use affix to name the subclass. In this Exercise, we can name `Arrival` to `ArrivalEvent`.
{% endhint %}

The use of the parent class, in this example, the `Event` class, is that:

1. To allow all subclasses to be recognised as `Events`.
2. To define a set of common properties / behaviors shared across all subclasses.

Thus, one key observation we can find here is that: there's never a need to create an instance of `Event`!

***

Here, it comes the idea of _abstract class_, which is a class that **cannot be instantiated**!

And _abstract method_ means that: instances of this _abstract class_ **possess a certain behavior**, but how exactly this behavior works **depends on the subclasses' implementation**.

### Interface

Models one simple functionality that will be used by different classes.
