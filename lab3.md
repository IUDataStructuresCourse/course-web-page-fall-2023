# Lab 3: Merge Sort with Linked Lists

## Overview

In this lab you are asked to implement two versions of merge sort on linked lists:
one that produces a _new_, sorted list and another that works _in-place_ by changing
the input list.

Section 7.6 of the textbook describes merge sort on an array. The steps of the algorithms are

+ split the input sequence in half
+ recursively sort the two halves
+ merge the two results into one

You’ll need to adapt the algorithm for linked lists.
The main difference between the two versions is that the sorted list is new in the first,
[functional](https://en.wikipedia.org/wiki/Functional_programming) version,
while it overwrites the input list in the second, in-place version.

**[Example 1]** The `sort` function applied to the following linked list

```
[5] -> [2] -> [7] -> [1]
```

produces the following new linked list, while leaving _the original one unchanged_:

```
[1] -> [2] -> [5] -> [7]
```

**[Example 2]** The `sort_in_place()` function applied to the following list

```
[6] -> [3] -> [8] -> [2]
```

should rearrange the nodes into the following order:

```
[2] -> [3] -> [6] -> [8]
```

## Problem Set

### Testing Merge Sort

Look at the `MergeSort.java` in student support code. Beside `sort()` and `sort_in_place()`,
it contains the type signatures for `merge()` and `merge_in_place()`. The sort functions call
their respective merge functions to combine two sorted halves into one.

Before implementing the two versions of merge sort, think about their correctness criteria.
Write test cases for `merge()`, `sort()`, `merge_in_place()` and `sort_in_place()`.
Run your test cases on Autograder against four buggy implementations and see whether they can
catch all the bugs!

<details open="true">
  <summary>Hints: a few things to test...</summary>
  <ul>
    <li>Whether <code>merge()</code> and `sort()` create new output lists</li>
  </ul>
</details>

### Implementing Merge Sort

Implement both `sort()` (functional) and `sort_in_place()` (in-place) in class `MergeSort`.
Both functions have a `Node`-typed parameter and `Node` return type, which point to the first node
in the input list and the first node in the output list respectively.
The `Node` class is defined in `Node.java`.

<!-- You’ll need to implement two versions of the merge algorithm, one that returns a new linked list and the other that works in-place, rearranging the nodes. -->

<!-- static Node merge(Node A, Node B) { ... } -->

<!-- static Node merge_in_place(Node A, Node B) { ... } -->
<!-- For example, merge applied to the following two sorted lists -->

<!-- [1] -> [2] -> [5] -> [7] -->
<!-- [2] -> [3] -> [6] -> [8] -->
<!-- produces the following newly allocated list -->

<!-- [1] -> [2] -> [2] -> [3] -> [5] -> [6] -> [7] -> [8] -->
<!-- The idea of the merge algorithm is to scan through the two input sequences, choosing the smaller of the two current elements for the output sequence. -->

<!-- Testing -->
<!-- Create a file named MergeSortTest.java that includes three tests for each of your merge and sort methods. -->

<!-- Questions -->
<!-- In a file named README.md answer the following questions. -->

<!-- What is the time and space complexity of your merge function? -->
<!-- What is the time and space complexity of your sort function? -->
<!-- What is the time and space complexity of your merge_in_place function? -->
<!-- What is the time and space complexity of your sort_in_place function? -->
<!-- Submission -->
<!-- Submit your MergeSort.java, MergeSortTest.java, and README.md files to the autograder: -->
