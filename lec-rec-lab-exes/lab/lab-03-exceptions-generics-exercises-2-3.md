# Lab 03 - Exceptions, Generics,Exercises 2,3

## Exercise 2 Review

### Do we need a LeaveQueueEvent?

A `SerciveBeginEvent` will be triggered immediately after a `ServiceEndEvent` with a newly dequeued customer.

## Exceptions

This part is one important test point in [pe0-review.md](../../past-year-exam/pe0-review.md "mention"). Refer to the cheatsheet there will be pretty enough.

## Generics

### Generic Type Parameters

Type parameters are dummy types which get substituted with concrete types when creating an instance.

#### Scope of Type Parameters

> A class's generic type parameters **cannot be used** in `static` methods or `static` fields.

This is because `static` means the same for all instances, but `T` will change based on what you instantiate them to be. Thus, class's type parameter is **not allowed** in `static` method or `static` field.

For example,

{% code lineNumbers="true" %}
```java
class A<T> {
    public static T foo() {...}
    public static <S> S bar1() {...}
    public static <S> T bar2() {...}
    public static <T> T bar3() {...}
}
```
{% endcode %}

Line 2 and Line 4 are **not allowed** because they use the generic type parameter `T` from the class `A`.

> To use generic type parameters in `static` method, you must declare [#generic-methods](../lecture/lec-05-generics/#generic-methods "mention")! (a.k.a declare a type parameter in the `static` method again)

For example, in Line 3 and Line 5, we declare a new type parameter `S` and a shadowed `T`. This is allowed!

### Classic Questions

#### Question 1 (Type Checking)

For the the following code,

{% code lineNumbers="true" %}
```java
class A<T> {
    public static <S> S bar1() {
        return null;
}
    public static <T> T bar3() {
        return null;
    }
}
```
{% endcode %}

Determine the 8 code snippets in the following table will compile or not?

**Solution:**

* **Static Methods in Generic Classes**:
  * generic type parameters.
  * Example: `bar1` uses `<S>`, and `bar3` uses `<T>`. These are method-level type parameters, unrelated to the class's `<T>`.
* **Calling Static Generic Methods**:
  * Syntax for specifying a method's type parameter: `ClassName.<Type>method()`.
  * Class type parameters (e.g., `<Integer>` in `A<Integer>`) are irrelevant for static methods and cannot be mixed with method type parameters in the call.

| Code Snippet                 | Compiles? | Reason                                                   |
| ---------------------------- | --------- | -------------------------------------------------------- |
| `A<Integer>.<Double>bar1();` | ❌         | Invalid syntax (mixes class/method type parameters).     |
| `A<Integer>.<Double>bar3();` | ❌         | Same as above.                                           |
| `A<T>.<Double>bar1();`       | ❌         | `T` is undefined unless in a valid generic context.      |
| `A<T>.<Double>bar3();`       | ❌         | Same as above.                                           |
| `A.<Double>bar1();`          | ✅         | Correct syntax (method’s type parameter explicitly set). |
| `A.<Double>bar3();`          | ✅         | Same as above.                                           |
| `A.bar1();`                  | ✅         | Type inference (method’s type parameter inferred).       |
| `A.bar3();`                  | ✅         | Same as above.                                           |

#### Question 2 (Type Checking)

For the the following code,

```java
class B<T> {
    private T x; // Instance field of type T (class-level)
    
    public T foo() { return null; } // Returns class's T
    
    public <S> S bar1() { return null; } // Generic method: returns S
    public <S> T bar2() { return null; } // Generic method: returns class's T
    public <T> T bar3() { return null; } // Generic method: shadows class's T
}
```

Determine the 5 code snippets in the following table will compile or not?

**Solution**:

| Method Call                        | Compiles? | Return Type | Explanation                                                    |
| ---------------------------------- | --------- | ----------- | -------------------------------------------------------------- |
| `new B<Integer>().foo();`          | ✅         | `Integer`   | Non-generic method returns class’s `T`.                        |
| `new B<Integer>().<String>foo();`  | ❌         | —           | `foo()` is not generic; invalid syntax.                        |
| `new B<Integer>().<String>bar1();` | ✅         | `String`    | Generic method’s `S` set to `String`.                          |
| `new B<Integer>().<String>bar2();` | ✅         | `Integer`   | Method’s `S` is `String`, but returns class’s `T` (`Integer`). |
| `new B<Integer>().<String>bar3();` | ✅         | `String`    | Method’s `T` shadows class’s `T`; returns `String`.            |

<details>

<summary>What if we create a <code>public &#x3C;T> T bar4()</code> that returns <code>this.x</code>?</summary>

It **doesn't compile** because the generic method type parameter `T` may be different from the class's type parameter `T`.

</details>

**Takeaway:**

* **Non-generic methods** (e.g., `foo()`) cannot have type arguments specified.
* **Generic methods** (e.g., `bar1()`, `bar2()`, `bar3()`) are **independent** of the class’s type parameters.
* **Shadowing** (e.g., `bar3()`) creates a new type variable unrelated to the class’s `T`.
* **Type Mismatch** in `bar4()` occurs because the method’s `T` is distinct from the class’s `T`, so you cannot return `this.x`, which is of the class's `T`.
