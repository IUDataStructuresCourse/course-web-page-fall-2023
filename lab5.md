# Lab 5: Binomial Heaps

## Overview

In this lab you are going to complete the code for binomial queue,
by implementing the `isHeap()` method in the `BinomialHeap` and
`BinomialQueue` classes and the `insert()` and `merge()`
methods in the `BinomialQueue` class.

Please review the lecture notes [here](./lectures/Oct-16) before you start.

## Support Code and Submission

+ Student support code is at [link](https://github.com/IUDataStructuresCourse/binomial-queue-student-support-code).
+ Submit your code file `BinomialQueue.java` ([Problem 1 and 2](#problem-1-binomialheap)) to
  [link](https://autograder.luddy.indiana.edu/web/project/701).
+ Submit your test file `StudentTest.java` ([Problem 3](#problem-3-testing)) to
  [link](https://autograder.luddy.indiana.edu/web/project/708).

## Problem Set

### Problem 1: `BinomialHeap`

The following is an excerpt from the `BinomialHeap` class:

```java
class BinomialHeap<K> {
    K key;
    int height;
    PList<BinomialHeap<K>> children;
    BiPredicate<K, K> lessEq;

    BinomialHeap<K> link(BinomialHeap<K> other) {
        // ...
    }

    boolean isHeap() {
        // ...
    }
}
```

+ The `link()` method takes two trees of the same height and
  combines them into a new tree with one greater height.
+ **[YOUR TASK]** The `isHeap()` method should verify that the tree
  rooted at the current instance of `BinomialHeap` satisfies the
  max heap property, that is, that the key of each parent is greater or equal to
  the keys of its children.

### Problem 2: `BinomialQueue`

The following is an excerpt from the `BinomialQueue` class:

```java
public class BinomialQueue<K> {
    PList<BinomialHeap<K>> forest;
    BiPredicate<K, K> lessEq;

    public BinomialQueue(BiPredicate<K, K> le) {
        // ...
    }

    public void push(K key) {
        // ...
    }

    public K pop() {
        // ...
    }

    public boolean isHeap() {
        // ...
    }

    static <K> PList<BinomialHeap<K>>
        insert(BinomialHeap<K> n, PList<BinomialHeap<K>> ns) {
        // ...
    }

    static <K> PList<BinomialHeap<K>>
        merge(PList<BinomialHeap<K>> ns1, PList<BinomialHeap<K>> ns2) {
        //...
    }
}
```

+ **[YOUR TASK]** The `isHeap()` method verifies that the binomial queue
  (which is a forest of binomial trees) satisfies the heap property.
+ **[YOUR TASK]** The `insert()` method in `BinomialQueue` should put the tree `n` into
  the forest `ns` while maintaining two invariants:

    1. the trees in the forest must be sorted by *increasing heights*
    2. there should not be two trees with the same height

+ **[YOUR TASK]** The `merge()` method in `BinomialQueue` should combine two forests
  into a single forest that satisfies the two invariants above.

### Problem 3: Testing

Create tests in `StudentTest.java` which thoroughly test the public
`push` and `pop` methods of the `BinomialQueue`.

-----------------

* You have reached the end of Lab 5. Yay!
