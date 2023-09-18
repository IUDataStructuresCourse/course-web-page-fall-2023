# Lab 4: Next Prev Binary Tree

## Background and Overview

This lab is a stepping stone for the Segment Intersection Project.

The lab is to finish a `BinaryTree` class that implements the
`Sequence` and `ReverseSequence`
interfaces. These interfaces are defined in terms of iterators, `Iterator`
and `ReverseIterator`. Traversing according to the `Sequence`
should provide an *inorder traversal*. Traversing according to the
`ReverseSequence` should provide a *backwards inorder traversal*.

## Support Code and Submission

+ Student support code is at [link](https://github.com/IUDataStructuresCourse/next-prev-bt-student-support-code).
+ Submit your code file `BinaryTree.java` ([Problem 1](#problem-1-tree-traversal)) to
  [link](https://autograder.luddy.indiana.edu/web/project/693).
+ Submit your test file `BinaryTreeTest.java` ([Problem 2](#problem-2-testing)) to
  [link](https://autograder.luddy.indiana.edu/web/project/831).


## Problem Set

### Problem 1: Tree Traversal

#### Part 1: Helper functions

The first step is to implement `next` and `previous` methods in the
`Node` class (which is nested inside the `BinaryTree` class).
We recommend that you start by implementing the following helper
functions in the `Node` class, which come in handy when trying to move
forwards or backwards within the binary tree:

```java
    public Node first();
```

+ `first()`: returns the first node in the current subtree according to an inorder traversal.

```java
    public Node last();
```

+ `last()`: returns the last node in the current subtree according to an inorder traversal.

```java
    public Node nextAncestor();
```

+ `nextAncestor()`: returns the first ancestor that is next with respect to
an inorder traversal or `null` if there is none.
    * We recommend that you use the `parent` field that has been added to the `Node`
      class for this purpose.

```java
    public Node prevAncestor();
```

+ `prevAncestor()`: returns the first ancestor that is previous with respect to
and inorder traversal or null if there is none.
    * Again, you'll need to use the `parent` field to walk up the tree.

#### Part 2: Next and Previous

Once the above helper functions are complete, it is straightforward to
implement methods that return the next and previous nodes according
to an inorder traversal:

```java
    public Node next();
    public Node previous();
```

* The idea for `next()` is that:
1. if the current node has a right child, then the `first()` of
   that child's subtree is the next node.
2. if the current node does not have a right child (if it is `null`),
   then the next node is it's `nextAncestor()`.

* The idea for `previous()` is the mirror image of the idea for `next()`.

#### Part 3: Iter

You can now move on to finish the implementation of the `Iter` class
(which is nested inside the `BinaryTree` class) by filling in:

* `get()`: returns `data` of the current node `curr`
* `retreat()`: as its name suggests, sets the current node to be the previous node of `curr`
* `advance()`: sets the current node to be the next node of `curr`
* `equals()`: if `curr` of this `Iter` is equal to the `curr` of the other
* `clone()`: returns a new iterator that copies `curr`

These methods are 1-liners.

### Problem 2: Testing

Write test cases in `BinaryTreeTest.java` with one method named `test()`.
This method should thoroughly test the `BinaryTree` class.

Test and debug your own code from Problem 1 locally and then submit both your code
and your test cases to Autograder for grading.

The Autograder will apply your tests to buggy implementations of the
`BinaryTree` class. You receive 1 point for each bug detected.
The Autograder will also apply your tests to a correct implementation
to rule out false positives.


-----------------

* You have reached the end of Lab 4. Yay!
