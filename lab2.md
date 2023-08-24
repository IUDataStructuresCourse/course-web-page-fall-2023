# Lab 2: Array Search

## Overview and Submission

In the previous lab, we developed test cases for three array search algorithms. This time
we are going to implement those algorithms as methods of a class called `Search`.

You may reuse the IntelliJ project from last time. Create file `src/Search.java` which
contains the `Search` public class.

When your lab is complete, submit your `Search.java` file to Autograder.
By the way, make sure to test your solutions using the test cases from Lab 1 first!

## Problem Set

### Problem 1: Linear Search on an Array of Booleans

Implement the search function `find_first_true` that finds
the position of the first `true` in array `A` of boolean values,
that is, find the smallest index `i` such that `A[i]` is `true`.
The search is restricted to the subarray within `A` that starts at the `begin`
index and finishes one element before the `end` index
(half-open interval `[begin,end)`).
If there are no `true` elements in the subarray, then `find_first_true`
returns the `end` position of the subarray.
_The time it takes for your algorithm to run should be proportional to the
length of the array `A`._

**[Example 1]** If the input array `A` is

```java
{false, false, true, false, true}
```

then the result should be 2 because `A[2] == true` and there are no
`true` elements at lower indices (`A[0]` and `A[1]` are both `false`).

**[Example 2]** Suppose `A` is the array

```java
{true, false, true, false, true}
```

and we search in the half-open interval `[1,3)`. The answer should be `2`.

```java
find_first_true(A, 1, 3) == 2
```

Add the following method to the `Search` class and fill-in the
implementation:

```java
public static int find_first_true(boolean[] A, int begin, int end) {
    // ...
}
```
