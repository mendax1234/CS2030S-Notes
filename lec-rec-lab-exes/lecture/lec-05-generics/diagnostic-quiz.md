# Diagnostic Quiz

## Problems

### 05. Autoboxing

> Java automatically converts a primitive type (e.g., `int`, `char`, etc.) into its corresponding wrapper class (e.g., `Integer`, `Character`, etc.) when a reference type is expected (This is called auto-boxing).

In the following code snippet,

{% code lineNumbers="true" %}
```java
class C {
  public void foo(Object o1, Object o2) { 
  }
}
```
{% endcode %}

If our function call is as follows,

```java
new C().foo(1, "String");
```

The `int` 1 will be converted to `Integer` 1 automatically. Thus, the function call can compile.

### 07. `compareTo()` method

> When implementing `Comparable<T>`, the `compareTo` method **must have exactly the same parameter type as `T`**. For example:
>
> * If `class C implements Comparable<String>`, the method **must** be `compareTo(String s)`.
> * Using the wrong type (e.g., `Object`, a generic `T`, or an unrelated class like `C`) results in a compilation error.

Using the knowledge point above, this problem will become easier to solve! Jiayous!

### 08. **Shadowing of Generic Type Parameters**

> When a method declares its own generic type parameter with the same name as the class-level type parameter, the method-level type parameter will **shadow** the class-level type parameter within the method's scope.&#x20;

For example, we have the following code snippet,

{% code lineNumbers="true" %}
```java
class A<T> {        // Line A
  T x;              // Line B
  
  <T> void foo() {  // Line C
    T y;            // line D
  }
  
  T bar() {         // Line E
    return null;
  }
}
```
{% endcode %}

|  Code Location     | 	Which T?                             | Explanation                                                                                                          |
| ------------------ | ------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| Line B (`T x;`)    | Class-level `T` (declared in Line A)  | At the class level, only the type parameter from the class declaration is available.                                 |
| Line D (`T y;`)    | Method-level `T` (declared in Line C) | The method `foo()` declares its own type parameter `T`, which shadows the class-level `T` within the method's scope. |
| Line E (`T bar()`) | Class-level `T` (declared in Line A)  | The method `bar()` does not declare its own type parameter, so it uses the class-level `T`.                          |

### 15. Raw Types and Type Erasure in Generics

> When you use a generic class as a raw type (without specifying its type parameter), Java **erases** the type parameter, and methods that depend on the generic type is replaced by `Object`. This behavior will have the following **implications**:
>
> * **Type Safety Loss:** Without a type parameter, you lose compile-time type checking.
> * **Explicit Casts Required:** You must manually cast the returned `Object` to the expected type.
> * **Potential Runtime Errors:** Incorrect casts may compile (with warnings) but can lead to `ClassCastException` at runtime.

| Option | Code                             | Return Type from a.get() | Explicit Cast? | Compile Outcome                  |                                                                                                   |
| ------ | -------------------------------- | ------------------------ | -------------- | -------------------------------- | ------------------------------------------------------------------------------------------------- |
| 1      | `a.get().length();`              | `Object`                 | No             | Error                            | Cannot call `length()` on an `Object` (only defined for `String`).                                |
| 2      | `Integer i = a.get();`           | `Object`                 | No             | Error                            | Incompatible assignment: cannot assign `Object` to `Integer` without casting.                     |
| 3      | `Integer i = (Integer) a.get();` | `Object`                 | Yes            | Compiles with unchecked warnings | The explicit cast to `Integer` allows compilation, though it may be unsafe.                       |
| 4      | `String s = (String) a.get();`   | `Object`                 | Yes            | Compiles with unchecked warnings | The explicit cast to `String` allows compilation, but it is unsafe and may cause a runtime error. |

## Tips

1. Java automatically converts a primitive type (e.g., `int`, `char`, etc.) into its corresponding wrapper class (e.g., `Integer`, `Character`, etc.) when a reference type is expected (This is called auto-boxing).
2. When implementing `Comparable<T>`, the `compareTo` method **must have exactly the same parameter type as `T`**. For example:
   * If `class C implements Comparable<String>`, the method **must** be `compareTo(String s)`.
   * Using the wrong type (e.g., `Object`, a generic `T`, or an unrelated class like `C`) results in a compilation error.
3. When a method declares its own generic type parameter with the same name as the class-level type parameter, the method-level type parameter will **shadow** the class-level type parameter within the method's scope.&#x20;
4. When you use a generic class as a raw type (without specifying its type parameter), Java **erases** the type parameter, and methods that depend on the generic type is replaced by `Object`. This behavior will have the following **implications**:
   * **Type Safety Loss:** Without a type parameter, you lose compile-time type checking.
   * **Explicit Casts Required:** You must manually cast the returned `Object` to the expected type.
   * **Potential Runtime Errors:** Incorrect casts may compile (with warnings) but can lead to `ClassCastException` at runtime.
