# Lab 05

## Factory method

The motivation of using factory method is that

1. many overloaded methods may be confusing.
2. cannot return a null instance from a constructor

(The example is awesome!)

### Singleton Pattern

To ease communication between very far away data structure.

### Caching

Store the unique values as static fields in the class. For the caching to work, the variable should hold a constant value.

### Error-Handling

Throwing exceptions is not recommended. Thus, in our factory method, we can deal with these by again returning instances with special meanings.

## Exercise 5 Review

1. Choose the most flexible type to declare the special meaning variables. For example, NONE should be declared using unbounded wildcard.

### map and flatMap

Draw the pipeline to help you visualize the boxing process.

To define the wildcard to the parameter, try to relax it!

### Maybe

Maybe\<T> is a wrapper for an item that may not may not exist.

### Option and Result

