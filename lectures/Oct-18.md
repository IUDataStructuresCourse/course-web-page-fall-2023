# Lecture: Quicksort

Recall Mergesort, worst case O(n log(n)) time.

Quicksort is worst case O(n²) time. But on average, it takes n log(n).

Quicksort does not use extra memory; it is an inplace algorithm.

Algorithm overview:

1. Choose a **pivot** element and **partition** the array around the pivot,
   that is, each element in the first part is less than the pivot
   and each element in the second part is greater than the pivot.

2. Sort the two parts.

Implementation:

    static void quicksort(int[] A, int begin, int end) {
        if (begin != end) {
            int pivot = partition(A, begin, end);
            quicksort(A, begin, pivot);
            quicksort(A, pivot+1, end);
        }
    }

## Partition

The `partition` function chooses the pivot and then rearranges the
array around the pivot so that every element less or equal to the
pivot is to the left of the pivot and every element greater than the
pivot is to the right of the pivot.

The choice of pivot is important because it controls the size of the
left and right-hand sides.

An even split of the array is good because it leads to a balanced tree
of recursive procedure calls.

There are many ways to choose the pivot element. One of the simplest
is to choose the last element, but that doesn't guarantee an even
split.

Simple implementation:

    static int partition_ez(int[] A, int begin, int end) {
      ArrayList<Integer> L = new ArrayList<Integer>();
      ArrayList<Integer> R = new ArrayList<Integer>();
      int pivot = A[end - 1];
      for (int i = begin; i != end - 1; ++i) {
         if (A[i] ≤ pivot) { L.add(A[i]); }
         else { R.add(A[i]);  }
      }
      int pivot_loc = copy(L, A, begin);
      A[pivot_loc] = pivot;
      copy(R, A, pivot_loc + 1);
      return pivot_loc;
    }

### Partition in-place

But we can be more space efficient by reusing `A` for `L` and `R`.

Example run of the partition algorithm:

    [2,8,7,1,3,5,6,4]       (draw with extra space between the numbers)

The last element is the pivot:

    [2,8,7,1,3,5,6|4]

We move from left to right, separating the elements into those less
than 4 and those greater than 4.

    [||2,8,7,1,3,5,6|4]
    [2||8,7,1,3,5,6|4]
    [2|8|7,1,3,5,6|4]
    [2|8,7|1,3,5,6|4]

swap 1 with 8 (the front of the sequence of greater-than elements)

    [2,1|7,8|3,5,6|4]

swap 3 with 7

    [2,1,3|8,7|5,6|4]
    [2,1,3|8,7,5|6|4]
    [2,1,3|8,7,5,6|4]

To finish off, we need to put 4 (the pivot) into the middle.
swap 4 with 8

    [2,1,3|4|7,5,6,8]

Looking at a half-way point in the run gives us a good idea
for what the algorithm needs to do.

    [2,1|7,8|3,5,6|4]
       i     j

if A[j] ≤ pivot, swap it with first greater-than elt., increment i and j

    [2,1,3|8,7|5,6|4]
         i     j

if A[j] > pivot, just move on to the next j

    [2,1,3|8,7,5|6|4]
         i       j

Space-efficient implementation:

    static int partition(int[] A, int begin, int end) {
       int pivot = A[end-1];
       int i = begin;
       for (int j = begin; j != end-1; ++j) {
          if (A[j] ≤ pivot) {
             swap(A, i, j);
             ++i;
          }
       }
       swap(A, i, end-1);
       return i;
    }

### Random Pivot

A better way to choose the pivot is to choose an element at random.

* This makes it difficult for a hacker to take advantage
  of quicksort in a denial-of-service attack.

* Also, the probability of the random choice landing at the
  far ends of the array is low, so the splits are even enough
  to make the probabilistic "expected" time complexity to be
  O(n log(n)).

### Proof of correctness for partition

Let L be the region [begin..i)   (L for "less or equal")

Let G be the region [i,j)    (G for "greater")

Let T be the region [j,end-1)    (T for "to-do")

The postcondition that we want to prove is:

    let pivot = partition(A, start, end)

    for i in [start,pivot), A[i] ≤ A[pivot]
    for i in [pivot+1,end), A[pivot] < A[i]

loop invariant:
* all the elements in L are less than or equal to the pivot, and
* all the elements in G are greater than the pivot

At the start of the loop:

           |||--T--|p|
           i  j

L and G are both empty, so the loop invariant holds trivially

In the loop:

           |--L--|--G--|--T--|p|
                i       j

there are two cases to consider

1. A[j] ≤ pivot

     Here we have two cases to consider, whether G is empty or not.
     The same code runs in either case, but the reason
     why it is correct is different in each case.

     a) Suppose G is not empty.
        We add A[j] to the L region by swapping it with the first
        element x of G (at i) and incrementing i.
        Because A[j] ≤ pivot, we have that every elt. of L is less than 
        pivot. Also, we want to keep x in G, so we increment j.
        The elements in G haven't changed, so they are all still
        greater than pivot.

     b) Suppose G is empty. We still swap A[j] with i , but
        that's a no-op because j == i. We increment i and j,
        thereby adding A[j] to L. G remains empty.

2. A[j] > pivot

   We add A[j] to the G region by incrementing j.
   We have A[j] > pivot and the rest of G hasn't changed,
   so every elt. of G is greater than pivot.
   We haven't changed L, so every elt. of L is less than pivot.

After the loop:

 We have L = [start,i), G = [i,end-1)

       |---L---|---G---|p|
              i

 We swap the pivot p with the first elt. of G (at i)

       |---L---|p|---G---|

 Let pivot = i.

 Now we have L = [start,pivot), G = [pivot+1,end).

 Every element of L is less than A[pivot].

 Every element of G is greater than A[pivot].

 So the proof is complete.

### Time complexity of partition: Θ(n)

### Student exercise

Apply quicksort to the array, write the state of the array after each
step within the partitioning
    
    [6,1,2,3]

quicksort(A, 0, 4)
- partition around 3

    [|6|1,2|3]
    [1|6|2|3]
    [1,2|6|3]
    [1,2|3|6]

- quicksort(A, 0, 2)  (doesn't change anything)
  * partition around 2

        [||1|2]  
        [1|||2]
        [1|2|]

  * quicksort(A,0,1)
      - partition around 1

          [|||1]
          [|1|]

      - quicksort(A,0,0) returns immediately
      - quicksort(A,2,2) returns immediately
  * quicksort(A,2,2) returns immediately
- quicksort(A, 3, 4)
    * partition around 6

            [|||6]
            [|6|]

    * quicksort(A,3,3) returns immediately
    * quicksort(A,4,4) returns immediately

## Worst-case time complexity of quicksort

  Suppose that each time we partition, it turns out
  that everything ends up in L. So the recursive call
  to sort L is T(n-1) and G is T(0).
      
      T(n) = T(n-1) + T(0) + Θ(n)
      
      T(n) in O(n²)

## Best-case time complexity

Suppose that each time we partition, L and G come out the same size.
    
    T(n) = T(n/2) + T(n/2) + Θ(n) = 2 T(n/2) + Θ(n)
    
    T(n) in O(n log(n))


