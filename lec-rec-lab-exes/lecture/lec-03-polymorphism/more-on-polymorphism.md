# More on polymorphism

When dealing with type hierarchies, you often want to treat an object **not** as the specific type that it is, **but** instead as its bast type. This allows you to write code that doesn't depend on specific type. This is called _polymorphism_.

## The power of Polymorphism

In the `Shape` example, methods manipulate generic shapes, unconcerned about whether they're circles, squares, triangles, or some shape that hasn't even been defined yet. All shapes can be drawn, erased, and moved, so these methods simply send a message to a `Shape` object; they don't worry about how the object copes with the message.

Such code is unaffected by the addition of new types, and adding new types is the most common way to extend an object-oriented program to handle new situations.

{% hint style="info" %}
This ability to easily extend a design by deriving new subtypes is one of the essential ways to encapsulate change. This greatly improves designs while reducing the cost of software maintenance.
{% endhint %}
