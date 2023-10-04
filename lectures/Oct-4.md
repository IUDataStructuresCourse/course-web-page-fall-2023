# Lecture: Heaps and Priority Queues, Continued

## Recap: Heaps

            __16__
           /      \
         14        10
        /  \      /  \
       /    \    /    \
      8      7  9      3
     / \    /
    2   4  1

Idea: store elements in an array left-to-right, one level at a
time, top-to-bottom.

              0  1  2  3  4  5  6  7  8  9 
            [16,14,10, 8, 7, 9, 3, 2, 4, 1]

Def. A **max heap** is a heap in which for every node other than the root, 
`A[i] ≤ A[parent(i)]`.

This is called the **heap property** or max-heap property.
One can instead create a min-heap by flipping this around.

Heap class:

    public class Heap<E> {
            ArrayList<E> data;
            BiPredicate<E,E> lesseq;
            ...
    }
    
### `sortInPlace` method

Idea: swap the max (the root) with the last element, call `max_heapify`, 
then shrink the heap by 1.
Similar to `extract_max` but does a swap instead of move.

    static <E> void sortInPlace(ArrayList<E> A, 
                                BiPredicate<E,E> lessThanOrEqual)
    {
        Heap<E> H = new Heap<E>(lessThanOrEqual);
        H.data = A;
        H.build_max_heap();
        for (int i  = H.data.size() - 1; i != 0; --i) {
            swap(H.data, 0, i);
            H.max_heapify(0, i);
        }
    }

Time complexity:
The for loop executes n times, and max_heapify is `O(log(n))`,
so we have `O(n log(n))`.

### `insert` method

    void insert(E k) {
        if (data.size() + 1 ≥ data.size()) {
            ArrayList<E> d = new ArrayList<>((data.size() + 1) * 2);
            for (E e : data)
                d.add(e);
            data = d;
        }
        data.add(k);
        increase_key(data.size() - 1);
    }
	
The time complexity is amortized `log(n)`.

## Aside: Loop Invariants

A *loop invariant* is a statement about the current state of affairs
that it true at the beginning of each loop iteration.

The tricky thing about a loop invariant is that the state of affairs
is changing, so it must be abstract enough to capture some property
that stays the same. That usually means that it is some kind of
formula involving the loop index. One usually obtains a loop invariant
by means of guess and check. First, make a guess at the loop
invariant.  Then check whether it satisfies the following 3
conditions:

1. show that the loop invariant is true before the start of the loop
2. assuming that the loop invariant is true at the beginning of the loop body
   and assuming that the loop condition is true,
   show that the loop invariant is true at the end of the loop body.
   (Do this for a hypothetical iteration of the loop.)
3. show that the loop invariant combined with the loop condition
   being false logically implies the correctness criteria for the
   algorithm.

If all 3 are satisfied, then you have a valid loop invariant.
Otherwise, you need to revise your guess and check again.


## `build_max_heap` method (take 2, time permitting)

    void build_max_heap() {
        int last_parent = data.size() / 2 - 1;
        for (int i = last_parent; i != -1; --i) {
            max_heapify(i, data.size());
        }
    }

Why does this procedure work? What is the loop invariant?
Answer: the invariant is that the trees rooted at
  positions from i+1 to the end are max heaps.

What is the time complexity?
Answer: `O(n log(n))` is the easy answer, but not tight. 

The tight upper bound is `O(n)` which can be obtained by
observing that the time for `max_heapify` depends on the
height of the node, and `build_max_heap` calls `max_heapify
many times on nodes with a low height.

Consider how many nodes there can be in an n-element heap at each
height. The worst cast is a complete tree. For example,

         n = 7

          _o_        height 2, 1 node
         /   \
        o     o      height 1, 2 nodes
       / \   / \
      o   o o   o    height 0, 4 nodes

    num. nodes at height 2 =  1 = ⌈ n / 8 ⌉  =  ⌈ n / 2^(2+1) ⌉
    num. nodes at height 1 =  2 = ⌈ n / 4 ⌉  =  ⌈ n / 2^(1+1) ⌉
    num. nodes at height 0 =  4 = ⌈ n / 2 ⌉  =  ⌈ n / 2^(0+1) ⌉

In general, 

    num. nodes at hight h ≤ ⌈ n / 2^(h+1) ⌉

So we can sum these up, from h=0 to log(n), with O(h) cost for each:

               log(n)
    time(n) =  ∑      h × ⌈ n / 2^(h+1) ⌉
               h=0
    
Pull out the `n`, multiply the right-hand side by 2 (change `h+1` to `h`),
and remove the ceiling.

                   log(n)
    time(n) ≤  n × ∑      h / 2^h
                   h=0

Reorganize the `h / 2^h`

                   log(n)
    time(n) ≤  n × ∑      h × (1/2)^h        (1)
                   h=0

Recall formula A.8.

    ∞
    ∑   k × x^k    =   x / (1 - x)², for |x| < 1           (A.8)
    k=0

Let `x = 1/2` and exchange `k` for `h` in A.8 to get the following.

    ∞
    ∑   h × (1/2)^h    =   (1/2) / (1 - (1/2))²   =  (1/2) / (1/4)  =  2
    h=0
   
Note that

    log(n)                      ∞
    ∑      h × (1/2)^h    ≤    ∑   h × (1/2)^h   =  2
    h=0                         h=0

So with inequation (1) we have
   
    time(n) ≤  n × 2

So the time complexity of `build_max_heap` is O(n).

### Recap of Heap Methods


* `build_max_heap` creates a heap from an unordered array in O(n).
* `maximum` returns maximum element in O(1).
* `extract_max` removes the max in O(log(n)).
* `sortInPlace` sorts an array in-place in O(n  log(n)).
* `increase_key` updates the key of an element in the heap in O(log(n)).
* `insert` adds a new element to a heap in O(log(n)).


## Priority Queues

- implement using a `Heap`
- the priorities are the keys
- `push`: implement with `insert`
- `pop`: implement with `extract_max`

In Java:

    class PriorityQueue<E> {
        Heap<E> heap;
        PriorityQueue(BiPredicate<E,E> lessThanOrEqual) {
            heap = new Heap<>(lessThanOrEqual);
        }
        void push(E key) {
            heap.insert(key);
        }
        E pop() {
            return heap.extract_max();
        }
    }
