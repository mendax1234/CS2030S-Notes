# Diagnostic Quiz

## Problems

### Q2. Overloading

Seems that till now the **only** requirement for _overloading_ is that the method should have the same name but different method signature. (The return type doesn't matter)

### Q3. Dynamic Binding

This question teaches us the _dynamic binding_, which is covered in [#method-invocation](./#method-invocation "mention"), is **only** applied to **instance methods**. Class methods only take the first stage, which is [#during-compile-time](./#during-compile-time "mention"), and the method found after the compile-time will be executed directly during the run-time.

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

### Q11. Liskov Substitution Principle

## Tips
