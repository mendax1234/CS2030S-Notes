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

Motivation: the use case to represent **compound data**.

Encapsulation is basically a **modelling** process, which means we pack all the data we needed into a particular type.

### Information Hiding

Awesome explanation here!

As the users/clients, we only care about **what** an object can do, we don't care **how** it does it.

#### Common practices

1. In principle, instance fields should be declared as `private` as much as possible.
2. The idea of abstraction

### Tell, Don't Ask

We never ask an object to spit out its own **raw** **data.** Instead, we let the object know what we want and ask for the **processed data**.

### Inheritance

The idea of using set to explain data type is awesome! And its usage in inheritance is awesome! Can use this to understand subtype is awesome!

### Subtype relation

Easy check: if "X is Y" makes sense, then probably $$X<:Y$$

View subtype sign as less than and equal can. An interesting comparison is awesome!

### Composition

Nesting of classes. "has a" relation is an easy check. Nest of class!

### Class Fields and Class Methods

Some usage.

### Before you code, plan first

Think through the program to decide:

* What objects do we need (Nouns)?
* What properties should each object encapsulate (Attributes)?
* What tasks can each object perform (Verbs)?

Establish a clear structure of the program first. **Draw a picture or diagram** to visualise!
