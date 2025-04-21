# Diagnostic Quiz

## Problems

### 6. How many times will an lambda be evulated in an Infinite List

> If you call `get(n)`, the the lambda expression in the `itearte()` will be ecaluated `n` times!

<figure><img src="../../../.gitbook/assets/lec09-quiz-06.png" alt=""><figcaption></figcaption></figure>

Since `get(n)`, and `n` is 5, the lambda expression will be evaluated 5 times!

### 8-11. Use Heap Diagram to trace the program

### 13. Infinite Loop in Stream

> 1. `sorted()` and `distinct()` should only be called on **finite** streams. Otherwise, the program will enter an infinite loop.

## Tips

1. When calling `get(n)` in EagerList or InfiniteList, notice the the index of the element in the list starts from 0!
2. `sorted()` and `distinct()` should only be called on **finite** streams. Otherwise, the program will enter an infinite loop.
