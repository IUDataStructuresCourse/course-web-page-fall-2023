# Lab 2: Array Search

## Overview and Submission

In the previous lab, we developed test cases for three array search algorithms.
This time we are going to implement those algorithms as methods of a class called
`Search`.

You may reuse the IntelliJ project from last time.
Create file `src/Search.java` which contains a public class called `Search`.
When your lab is complete, submit your `Search.java` file to Autograder.

By the way, make sure to test your solutions locally using the test cases
from Lab 1 first!

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
The caller of `find_first_true` always provides a valid half-open
range: `begin <= end`, `0 <= begin`, `begin <= A.length`,
`0 <= end`, and `end <= A.length`.
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

### Problem 2: Linear Search on an Array of Integers

⚠️ **Use `find_first_true`** to implement the function `find_first_equal`
that searches on an array of _integers_, with the goal of finding the
position of the first element that is equal to the `x` parameter.
If there are no elements equal to `x`, the length of the array is returned.
Again, the time it takes for your algorithm to run should be proportional
to the length of `A`.

**[Example 3]** Suppose `A` is the array

```java
{32, 11, 4, 5, 99, 5, 32, 75}
```
then the result of search for `5` should be `3`:

```java
find_first_equal(A, 5) == 3
```

Implement the following method in the `Search` class:

``` java
public static int find_first_equal(int[] A, int x) {
    // ...
}
```

### Problem 3: Binary Search on an Array of Booleans

Similar to Problem 1, we search on an array of booleans and look for the position
of the first `true`. This time we suppose that all of the `false` elements in the array
come before all of the `true` elements (sorted).

Implement `find_first_true_sorted(A, begin, end)`, which returns
the position of the first `true` in array `A`, that is, it finds the
smallest index `i` greater or equal to `begin` and less than `end`
such that `A[i]` is `true`.  If there is no `true` within the
half-open range `[begin,end)`, it returns `end`.  The caller always supplies a
valid half-open range which means `begin <= end`, `0 <= begin`,
`begin <= A.length`, `0 <= end`, and `end <= A.length`. Furthermore,
the caller also ensures that `A` must already be sorted, so that all the `false`
elements come before any `true` elements.

The algorithm should be more efficient and runs in time proportional
to the ⚠️**logarithm** of the length of the array, by
_looking at the element in the middle and restricting
the search to the right or left subarray_ depending on its value.

**[Example 4]** Suppose `A` is the _sorted_ array

```java
{false, false, true, true, true, true, true}
```

The position of the first `true` element is `2` in this case.

Implement your algorithm in the following method of the `Search` class.
Again, restrict your search to the half-open interval `[begin,end)`:

```java
public static int find_first_true_sorted(boolean[] A, int begin, int end) {
    // ...
}
```

-----------------

* You have reached the end of Lab 2. Yay!
