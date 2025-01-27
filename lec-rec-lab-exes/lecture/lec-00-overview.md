# Lec 00 - Overview

## Object Oriented Programming Characteristics

Alan Kay summarized five basic characteristics of Smalltalk, the first successful object-oriented language and one of the languages upon which Java is based. These characteristics represent a pure approach to object-oriented programming.

{% stepper %}
{% step %}
**Everything is an object**

Think of an object as a fancy variable: it stores data, but you can "make requests" to that object, asking it to perform operations on itself. In theory, you can take any conceptual component in the problem you're trying to solve (dogs, buildings, services, etc) and represent it as an object in your program.
{% endstep %}

{% step %}
**A program is a bunch of objects telling each other what to do by sending messages.**

To make a request of an object, you "send a message" to that object. More concretely, you can think of a message as a request to call a method that belongs to a particular object.
{% endstep %}

{% step %}
**Each object has its own memory made up of other objects.**

Put another way, you create a new kind of object by making a package containing existing objects. Thus, you can build complexity into a program while hiding it behind the simplicity of objects.
{% endstep %}

{% step %}
**Every object has a type**

Using the parlance, each object is an _instance of a class_, in which **"class" is synonymous with "type"**. The most important distinguishing characteristic of a class is "What messages can you send to it?"
{% endstep %}

{% step %}
**All objects of a particular type can receive the same messages.**

This is actually a loaded statement, as you will see later. Because an object of type "circle" is also an object of type "shape", a circle is guaranteed to accept shape messages. This means you can write code that talks to shapes and automatically handle anything that fits the description of a shape. This _substitutability_ is one of the powerful concepts in OOP.
{% endstep %}
{% endstepper %}

Booch offers an even more succinct description of an object:

> An _object_ has _state, behavior_ and _identity_

This means that an object can have internal data (which gives it _state_), methods (to produce _behavior_), and each object can be uniquely distinguished from every other object — to put this in a concrete sense, each object has a unique address in memory.

## Object

> When you're trying to develop or understand a program deign, one of the best ways to think about objects is as "**service providers"**.

Your program itself will provide services to the user, and it will accomplish this by using the services offered by other objects. Your goal is to produce (or even better, locate in existing code libraries) a set of objects that provide the ideal services to solve your problem.

### "Find" your objects

When using OOP to solve a problem, always ask yourself these two questions:

1. What _services_ would _those_ objects provide?
2. what _objects_ would _they_ need to fulfill their obligations?

### The mission of objects

In a good object-oriented design, each object does **one thing well**, but doesn't try to do too much.

## Group of objects: Class

Objects that are identical except for their state during a program's execution are grouped together into "classes of objects" and that's where the keyword **class** came from.

### Class vs. Type

**Class** and **Type** have no difference. When you see the word "type", think it as "class" and vice versa.

## Interaction with Objects: "Make a request"

To interact with objects, we "make a request". What does this mean?

1. A class has a method associated with each possible request, and when you make a particular request to an object, that method is called.
2. So, the meaning of "make a request" is saying that you "send a message" (make a request) to an object, and the object figures out what to do with that message (it executes code)

{% hint style="info" %}
Later you will find out that this is the famous "[Tell, Don't Ask](lec-02-class-instance-methods-inheritance/#tell-dont-ask)" Rule.
{% endhint %}
