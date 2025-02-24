# Rec 04

## Problems

### 01. Generic Type parameter and `static` field

As we have seen from [Lab 03](../lab/lab-03-exceptions-generics-exercises-2-3.md#scope-of-type-parameters),

> A class's generic type parameters **cannot be used** in `static` methods or `static` fields.

For more information, please go to [Lab 03](../lab/lab-03-exceptions-generics-exercises-2-3.md#scope-of-type-parameters)!

### 02. Variance Relationship about Generics and Wildcards

Just follow the different rules we have introduced in [lec-06-wildcards](../lecture/lec-06-wildcards/ "mention")

### 03. Type Inference and the Application of Wildcards

> If `class A implements Comparable<A>`, and `class B extends A`, then `B` actually implements `Comparable<A>` **not** `Comparable<B>`!

## Tips

1. If `class A implements Comparable<A>`, and `class B extends A`, then `B` actually implements `Comparable<A>` **not** `Comparable<B>`!
