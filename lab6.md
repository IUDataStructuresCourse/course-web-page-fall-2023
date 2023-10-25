# Lab 6: Generic Quicksort

## Problem Description

Implement quicksort in a way that is generic enough that it can be
applied to any input sequence that is represented as an array or a
linked list, by using the `Iterator` interface.
The elements of the sequence do not have to be integers,
but instead could be anything that implements the
`java.util.Comparable` interface:

```java
public static <E extends Comparable<? super E>>
void quicksort(Iterator<E> begin, Iterator<E> end) {
    // your code
}
```

Your implementation should maintain the time complexity of quicksort:
it should have worst case $O(n^2)$ time complexity and average case
$O(nlog(n))$ time complexity.

Test `quicksort()` thoroughly in `StudentTest.java`.

## Support Code and Submission

+ Student support code is at [link](https://github.com/IUDataStructuresCourse/quick-sort-student-support-code).
  Please make sure to go through existing code before you start.
  * There are a few helper functions in `Algorithms.java` that you may use,
    for example, `iter_swap()` swaps the elements at iterator positions `i` and `j`.
+ Submit your code file `QuickSort.java` to
  [link](https://autograder.luddy.indiana.edu/web/project/699).
+ Submit your test file `StudentTest.java` to
  [link](https://autograder.luddy.indiana.edu/web/project/709).

-----------------

* You have reached the end of Lab 6. Yay!
