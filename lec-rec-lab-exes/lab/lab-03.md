# Lab 03

## Exercise 2 Review

### Do we need a LeaveQueueEvent?

A `SerciveBeginEvent` will be triggered immediately after a `ServiceEndEvent` with a newly dequeued customer.

## Exceptions

Redo the heap pollution error in the notes.

SuppressWarnings and type erasure.

## Generics

### Generic Type Parameters

Type parameters are dummy types which get substituted with concrete types when creating an instance.

You can't use a class's generic type parameters in static methods or static fields.

generic type parameter is not part of the class name.

generic type parameters can be used in both instance and class method. But for the class method, you must declare the method generic type parameter, you cannot use the class generic type parameter.

Type erasure and compile error! Very important!
