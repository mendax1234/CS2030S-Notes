# Diagnostic Quiz

## Problems

### Q2. Overloading

The **only** requirement for _overloading_ is that the method should have the same name but different method signature. (**The return type doesn't matter**)

### Q3. Dynamic Binding

This question teaches us that the _dynamic binding_, which is covered in [#method-invocation](./#method-invocation "mention"), is **only** applied to **instance methods**. Class methods only take the first stage, which is [#during-compile-time](./#during-compile-time "mention"), and the method found after the compile-time will be executed directly during the run-time.

### Q5. Narrowing Type Conversion

Consider the following code,

{% code lineNumbers="true" %}
```java
S s;
T t;
s = (S) t; // Line A
```
{% endcode %}

If, the run-time type of `t` is not a subtype of `s`$$T$$, then a **run-time error** will be generated.

If, the compile-time type of `t` is **not** a subtype of `s`, then a **compile-time error** will be generated.

### Q11. Liskov Substitution Principle

When dealing with question regarding whether an _inheritance_ violates LSP or not. **Always use the test case method**, which is check whether the test cases that pass in the parent class can all pass in the derived class.

## Tips

1. We cannot do type casting between two types which has **no** **subtype** relationship. Otherwise, a **compile-time error** will be generated.
2. In the overloading methods, the return type doesn't matter.
3. When dealing with question regarding whether an _inheritance_ violates LSP or not. **Always use the test case method**, which is check whether the test cases that pass in the parent class can all pass in the derived class.
