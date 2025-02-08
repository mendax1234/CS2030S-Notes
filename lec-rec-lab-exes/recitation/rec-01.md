# Rec 01

Question (without comments):

{% file src="../../.gitbook/assets/ps1.pdf" %}

Answer:

{% file src="../../.gitbook/assets/ps1-comments.pdf" %}

## Problems

### 01. Information Hiding and Tell, Don't Ask

This is a typical question about infromation hiding and "Tell, Don't Ask". Nothing much to talk about.

### 02. Stack and Heap

The thing we should learn from this question is **how to declare and initialize** an **array** in Java

{% code lineNumbers="true" %}
```java
// Declaration
private double[] coord2D;

// Initialization
coord2D = new double[] {1，2}；
```
{% endcode %}

### 03. Method Invocation

1. We cannot change the CTT of a variable. So, if we have declared `Circle c`, we cannot use `String c` anymore in the same segement.
2. Always pay attention to whether the `Object::equals(Object)` has been overriden or not. If not, it will always compare whether two instances are the same or not.

## Tips

1. We cannot change the CTT of a variable. So, if we have declared `Circle c`, we cannot use `String c` anymore in the same segement.
2. Always pay attention to whether the `Object::equals(Object)` has been overriden or not. If not, it will always compare whether two instances are the same or not.
