# Lecture: Sorting in Linear Time

We discuss three sorting algorithm that have O(n) time, improving
over the O(n log(n)) algorithms by imposing extra requirements on
the input elements.

* Counting Sort: elements are integers in a specified range.
  (The Weiss textbook seems to call this bucket sort.)
* Radix Sort: elements are integers, or at least things with "digits".
* Bucket Sort: elements are totally ordered and uniformly distributed,
   such floating point numbers.

## Counting Sort

The input is an array of integers and the integers fall in the
half-open range [0,k). We can sort them using a technique called
counting sort that is similar to the one we used for checking
anagrams.

We'll start by giving some intuition for why the algorithm works.
Suppose we want to sort the following array.

    A = [2, 8, 7, 1], k = 10

Here's the output of sorting:

    B = [1, 2, 7, 8]
         0  1  2  3

Let's focus on just one element of the input, say `8`, and think about
which position it should move to. It belongs at position `3` because
there are three elements in the array that are less-than `8`.

The main idea behind counting sort is to count the number of elements
that are less-than (or equal) to each element.

Here's the algorithm:

1. Count how many times each integer appears in the input.
2. Use those counts to find out how many elements are
   less-than or equal to every element.
3. Place each number from the input into its correct
   position in the output array.

C[i] is the count for integer i

    C = [0, 1, 1, 0, 0, 0, 0, 1, 1, 0, 0]
         0  1  2  3  4  5  6  7  8  9 10
         
L stores the cumulative sum (aka. prefix sum) of the counts.
In other words, L[i] says how many elements in the input
are less-than or equal to element i.

    L = [0, 1, 2, 2, 2, 2, 2, 3, 4, 4, 4]
         0  1  2  3  4  5  6  7  8  9 10

Sorted output:

    B = [1, 2, 7, 8]
         0  1  2  3

The following array maps each element to its location in B:

    [-, 0, 1, -, -, -, -, 2, 3, -, -]
     0  1  2  3  4  5  6  7  8  9 10

How does this relate to L? Just subtract one from L[x] to
compute the location for element x in the output.

However, if there are duplicates, going from the cummulative sum in
L to the output gets a bit more complicated.

Another example with duplicate elements: 

    A = [3, 5, 2, 2, 8, 3]
         0  1  2  3  4  5

Here are the counts

         0  1  2  3  4  5  6  7  8
    C = [0, 0, 2, 2, 0, 1, 0, 0, 1]

and the cummulative sum

    L = [0, 0, 2, 4, 4, 5, 5, 5, 6]
         0  1  2  3  4  5  6  7  8

Working back-to-front through A.

Where does A[5]=3 go? L[3] = 4, 4 - 1 = 3.
    
    B = [0, 0, 0, 3, 0, 0]
         0  1  2  3  4  5
    
Update the cummulative sum L to reflect that we've dealt with
A[5]=3 by subtracting one from L[3].
    
    L = [0, 0, 2, 3, 4, 5, 5, 5, 6]
         0  1  2  3  4  5  6  7  8

Where does A[4]=8 go? L[8]=6, 6-1=5.
    
    B = [0, 0, 0, 3, 0, 8]
    
Subtract one from L[8].

    L = [0, 0, 2, 3, 4, 5, 5, 5, 5]
         0  1  2  3  4  5  6  7  8  

Where does A[3]=2 go? L[2]=2, 2-1=1.

    B = [0, 2, 0, 3, 0, 8]
    
Subtract one from L[2].
    
    L = [0, 0, 1, 3, 4, 5, 5, 5, 5]
         0  1  2  3  4  5  6  7  8  
         
where does 2 go? 1-1=0

    B = [2, 2, 0, 3, 0, 8]
    L = [0, 0, 0, 3, 4, 5, 5, 5, 5]

where does 5 go? 5-1=4

    B = [2, 2, 0, 3, 5, 8]
    L = [0, 0, 0, 3, 4, 4, 5, 5, 5]

where does 3 go? 3-1=2

    B = [2, 2, 3, 3, 5, 8]
    L = [0, 0, 0, 2, 4, 4, 5, 5, 5]

Counting sort in Java:

```java
static void counting_sort(int[] A, int[] B, int k) {
   int[] C = new int[k+1]; // counts of each element of A
   int[] L = new int[k+1];  // L[j] = number of elements less or equal j.
   // stage 1: counting
   for (int i = 0; i != A.length; ++i) { // O(n)
	  ++C[A[i]];
   }
   // stage 2: cummulative sum
   L[0] = C[0];
   for (int j = 1; j != k+1; ++j) {    // O(k)
	  L[j] = C[j] + L[j-1];
   }
   // stage 3: produce output
   for (int j = A.length - 1; j != -1; --j) {  // O(n)
	  int elt = A[j];
	  int num_le = L[elt];
	  B[num_le - 1] = elt;
	  L[elt] = num_le - 1;
   }
   // total time complexity: O(n + k)
   // if k is a constant,  O(n)
   // space complexity: O(k)
}
```

### Time complexity of counting_sort

* First loop: O(n)
* Second loop: O(k)
* Third loop: O(n)
* overall time complexity: O(n + k)

### Counting-sort is stable

Among equal elements, they appear in the output in the same order that
they appeared in the input. If the elements are merely integers, this
doesn't matter. But if the elements are something like personel
records sorted by unique ID's, then this might matter.

## Radix Sort

Radix sort also works on integers, and it sorts them by one digit at a
time, starting with the least significant digit.

It's important to use a stable sort for the sorting of each digit.

Example:

       V       V      V
     329      720     720    329
     457      355     329    355
     657      436     436    436
     839  ->  457 ->  839 -> 457
     436      657     355    657
     720      329     457    720
     355      839     657    839

    static void radix_sort(int[] A, int d) {
       int[] B = new int[A.length];
       for (int i = 0; i != d; ++i) { // O(n*d)
          counting_sort(A, B, 10, extract_digit(i,d)); // k=10, O(n+10) = O(n)
          // swap A and B
          for (int j = 0; j != A.length; ++j) { // O(n)
             int tmp = A[j];
             A[j] = B[j];
             B[j] = tmp;
          }
       }
    }

Had to update `counting_sort` to extract key from element, using function f.

    static void counting_sort<E>(E[] A, int[] B, int k, 
                                 Function<E,Integer> f)
    {
       int[] C = new int[k+1]; // counts of each element of A
       int[] L = new int[k+1];  // L[j] = number of elements less or equal j.
       // Compute C
       for (int i = 0; i != A.length; ++i) {
          ++C[f.apply(A[i])];
       }
       // Compute L
       L[0] = C[0];
       for (int j = 1; j != k+1; ++j) {
          L[j] = C[j] + L[j-1];
       }
       // Generate output
       for (int j = A.length - 1; j != -1; --j) {
          int key = f.apply(A[j]);
          int num_le = L[key];
          B[num_le - 1] = A[j];
          L[key] = num_le - 1;
       }
    }

Time complexity of radix_sort: O(d (n + k))

## Bucket Sort

Bucket Sort assumes that the input is drawn from a uniform
distribution. It then partitions the space into buckets and puts the
input elements into their buckets.

Let's fix the space to be [0,1). Then if we make the bucket array B
the same size as A, we can just multiply the element number by the
length of A to get the bucket number.

    static void bucket_sort(double[] A) {
       // Allocate the buckets 
       ArrayList<ArrayList<Double>> B = new ArrayList<>();
       for (int i = 0; i != A.length; ++i) { // O(n)
          B.add(new ArrayList<Double>());
       }
       // Distribute the elements of A to the buckets
       for (int i = 0; i != A.length; ++i) { // O(n)
          int bucket = (int)Math.floor(A[i] * A.length); // O(1)
          B.get(bucket).add(A[i]); // O(1)
       }
       // Sort each bucket
       for (int i = 0; i != B.size(); ++i) { // n iter, 
          B.get(i).sort((Double x, Double y) -> x < y ? -1 : (x > y) ? 1 : 0); // worst:O(n log n)
		                                                                       // average: O(1)
       }
       // Put the results back in A
       int k = 0;
       for (int i = 0; i != B.size(); ++i) { // n iters, O(n^2)? really O(n) 
          for (int j = 0; j != B.get(i).size(); ++j) { // n iters
             A[k] = B.get(i).get(j);  // O(1) , really once per input element
             ++k;
          }
       }
	   // total: average O(n)
	   // worst O(n^2 log n)
    }

Time complexity of bucket_sort:
* first loop: O(n)
* second loop: O(n) expected time because sorting
  a 1-length list is O(1)
* third loop: O(n)


