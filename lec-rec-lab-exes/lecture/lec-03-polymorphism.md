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

[^1]: "order" here means the **type** order! For example, changing from `double a, double b` to `double b, double a` is **not** considered as changing the order of the parameters.
