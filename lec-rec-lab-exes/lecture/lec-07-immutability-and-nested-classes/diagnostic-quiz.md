# Diagnostic Quiz

## Problems

### 01. Aliasing

> Aliasing means several different objects are referencing the same [object address](#user-content-fn-1)[^1], (**sharing the same address**), so if you change the object address, all the different objects will be changed. So, **the key to judge whether a snippet of code is aliasing is that whether two references share the same address.**

### **02. Factory method**

> **Factory method** must be `static`. Otherwise, if your constructor is `private` and your factory method is non-static, there is no way to instantiate an object from that class.

### 05. `...` and `@SafeVarargs`

> 1. The triple `.` notation is a Java syntax for a **variable number of arguments of the same type**. Often called _varargs_, this is just syntactic sugar for passing in an array of items to a method.
> 2. If the type is a generic type, we need to use `@SafeVarargs` to indicate that our code is safe.

The usage of `...` can be showed using the following code snippet

```java
class A {
  int[] x;
  A(int... x) {
    this.x = x;
  }
}
```

In this code, passing the following two arguments is allowed

{% code lineNumbers="true" %}
```java
A a = new A(1, 2, 3, 4);
A a = new A(new int[] {1, 2, 3, 4});
```
{% endcode %}

### 12. Inner class and effectively `final`

> The "effectively final" rule applies to _reading_ the variable’s value, not to enabling assignments.

With the following code, which line will cause the compilation error?

{% code lineNumbers="true" %}
```java
class E {
  int w;

  void doTask() {
    int x;

    class F {
      int y;

      F() {
        w = 1;
        x = 1;
        y = 1;
        z = 1;
      }
    }
  
    F f = new F();
  }

  class G {
    int z;
  }
}
```
{% endcode %}

Line 12 `x=1` and Line 14 `z=1` won't compile, because `x` is a captured variable and is treated as effectively `final` and the rule of effectively `final` applies to reading the variable value, not to enabling assignment to that variable.

### 13. Variable Capture

> In variable capture, the following will be captured:
>
> 1. The **local variables** of the method where the local class comes from (including the arguments)
> 2. The **instance** that invokes the method where the local class comes from.

Consider the code below.  Which variables are captured by the returned object from `foo`?

{% code lineNumbers="true" %}
```java
class A {
  int a;
  
  Object foo(String s) {
    return new Object() {
       double b;
       @Override
       public String toString() {
         return s + a + b;
       }
    };
  }
}
```
{% endcode %}

The variables captured are&#x20;

{% code lineNumbers="true" %}
```java
A.this
s
```
{% endcode %}

### 15. Valid anonymous class

> An anonymous can only extend one class or implement one interface. (A generic type is also considered as one class, remember that in Java, type is a synonym of class!)

[^1]: also can be understood as object reference.
