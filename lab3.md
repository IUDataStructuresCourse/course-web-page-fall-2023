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

Look at `MergeSort.java` in the student support code. Apart from `sort()` and `sort_in_place()`,
it also contains type signatures for `merge()` and `merge_in_place()`. The sort functions call
their respective merge functions to combine two sorted halves into one. Similar to their sorting
counterparts, `merge()` creates a _new_ list from two input lists while `merge_in_place()` works
in-place by rearranging the nodes.

Before implementing the two versions of merge sort, think about their correctness criteria.
Write regular, corner, and random test cases for `merge()`, `sort()`, `merge_in_place()`,
and `sort_in_place()`.
Run your test cases on Autograder against four buggy implementations and see whether they can
catch all the bugs!

<details open="true">
  <summary>Hints: a few things to test...</summary>
  <ul>
    <li>Whether <code>merge()</code> and <code>sort()</code> create <em>new</em> output lists</li>
    <li>Whether the output lists of <code>sort()</code> and <code>sort_in_place()</code> are sorted</li>
    <li>Whether the output lists of <code>sort()</code> and <code>sort_in_place()</code> are permutations
        of their input lists</li>
    <li>Corner cases</li>
    <li>...</li>
  </ul>
</details>

### Implementing Merge Sort

Implement both `sort()` (functional) and `sort_in_place()` (in-place) in class `MergeSort`.
Both functions have a `Node`-typed parameter and `Node` return type, which point to the first node
in the input list and the first node in the output list respectively.
The `Node` class is defined in `Node.java`. As is mentioned above, they call `merge()` and
`merge_in_place()` respectively, which you are supposed to implement as well.

<!-- For example, merge applied to the following two sorted lists -->

<!-- [1] -> [2] -> [5] -> [7] -->
<!-- [2] -> [3] -> [6] -> [8] -->
<!-- produces the following newly allocated list -->

<!-- [1] -> [2] -> [2] -> [3] -> [5] -> [6] -> [7] -> [8] -->
<!-- The idea of the merge algorithm is to scan through the two input sequences, choosing the smaller of the two current elements for the output sequence. -->

### Lab Report

Answer the following questions in your lab write-up `README.md`:

+ **[Question 1]** What is the time and space complexity of your `merge()` function?
+ **[Question 2]** What is the time and space complexity of your `sort()` function?
+ **[Question 3]** What is the time and space complexity of your `merge_in_place()` function?
+ **[Question 4]** What is the time and space complexity of your `sort_in_place()` function?

<!-- Submission -->
<!-- Submit your MergeSort.java, MergeSortTest.java, and README.md files to the autograder: -->
