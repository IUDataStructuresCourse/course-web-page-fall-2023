# Lecture: Linked lists and Abstract Data Types

> "The Tao gave birth to machine language. Machine language gave birth
> to the assembler. The assembler gave birth to the compiler. Now
> there are ten thousand languages. Each language has its purpose,
> however humble. Each language expresses the Yin and Yang of
> software. Each language has its place within the Tao.  But do not
> program in COBOL if you can avoid it."
>
> --The Tao of Programming

## Overview of the lecture

* Announcements
* Linked lists
* Abstract data types (ADTs): e.g. Sequence, Iterator

## Lab about MergeSort on linked lists. Quiz in lab.

## Linked Lists

Problem: removing from an array is slow. One must shift all the
following elements to the left.
  
    [1|2|3|4]
    => (remove 2)
    [1|3|4|-]

need to track number of elements separately from array length

waste of space

time complexity is O(n)

Alternative: linked structures

    array:
    [1|2|3|4] 
    vs.
    linked list:
    [1]->[2]->[3]->[4]-///

    class Node {
       Node(int d, Node n) { data = d; next = n; }
       int data;
       Node next;
    }

erasing is O(1):
to erase node [3], change next pointer of node `[2]`

    [1]->[2]  [3]->[4]-///
          \-------/

    static void remove_next(Node n) {
        if (n.next == null) {
            return;
        } else {
            n.next = n.next.next;
        }
    }

inserting in the middle is O(1)

    L 
    |
    V
    [42]->[7]->[3]
    insert_after(L, 10):
    [42]->[10]->[7]->[3]

    static void insert_after(Node n, int d) {
        Node m = new Node(d, n.next);
        n.next = m;
    }

What are the disadvantages of linked lists? 

* time to access nth element is O(n)

* space

### Pattern: Forward Traversal of a List
        
    for (Node n = begin; n != end; n = n.next) {
       ... n.data ...
    }

where `begin` and `end` are nodes and specify a half-open interval
within a linked list.

To process an entire list, `begin` is the head of the list and
`end` is null.


Example `find_first_equal` on linked list

    Node find_first_equal(Node begin, int x) {
        int total = 0;
        Node n;
        for (n = begin; n != null; n = n.next) {
           if (n.data == x)
             break;
        }
        return n;
    }

## Java Generics, a quick reminder

Classes and methods may have *type parameters*, written inside the
symbols `<` and `>`.

The body of a class and method may refer to the type parameters
anywhere you would use other types (variable declarations, etc.).

    class Node<T> {
      T data;
      Node<T> next;
    }

When using the class, choose a *type argument* for each parameter.
Here we choose `Integer` for the parameter `T`.

    Node<Integer> n = new Node<Integer>(42, null); 

Caveats: 

* You can't instantiate a generic with built-in types (e.g. `int`, `boolean`).
  Instead use the uppercase class versions: `Integer` and `Boolean`.

* You can't use `new` on a type parameter (e.g. `new T()`).

* You can't call methods on things whose type is a type parameter such
  as `T` (e.g. `data.foo()`).

## Java Interfaces, a quick reminder

An interface acts as an intermediary between data structures and algorithms.

An interface specifies some methods that are common to several
data structures and that are needed by one or more algorithms.

Example: the `Sequence` and `Iterator` interfaces

Enables visiting all the elements in order.

    interface Sequence<T> {
        Iter<T> begin();
        Iter<T> end();
    }

    interface Iter<T> {
        T get();
        void advance();
        boolean equals(Iter<T> other);
        Iter<T> clone();
    }

Example: Linear Search using the Sequence and Iterator Interfaces

    static <T> Iter<T> find_first_equal(Sequence<T> S, T x) {
       for (Iter<T> i = S.begin(); ! i.equals(S.end()); i.advance()) {
          if (i.get() == x) {
             return i;
          }
       }
       return S.end();
    }

List implementation of Sequence 

    class LinkedList<T> implements Sequence<T> {
       Node<T> head;
       ...
       public class ListIter implements Iter<T> {
	     Node<T> position;
		 ListIter(Node<T> pos) { position = pos; }
		 T get() { return position.data; }
		 void advance() { 
		    position = position.next;
		 }
		 boolean equals(Iter<T> other) { ... }
		 Iter<T> clone() { ... }
	   }
       ...
       Iter<T> begin() {
	     return new ListIter(head);
	   }
       Iter<T> end() {
	     return new ListIter(null);
	   }
	   
    }

## Why abstract data types like iterators?

Iterators add a fair amount of complexity, so there needs to be a
benefit that outweighs the cost, otherwise it would be better to do
without them.

Answer: code reuse.

Example: `equals` algorithm for comparing contents of two
sequences. Consider how much code is needed to implement this
algorithm for arrays (A), singly-linked lists (SL), doubly-linked
lists (DL), and combinations of them.  The algorithm has two
parameters, so you'd need 9 different versions of the equals
algorithm!

|     |  A  |  SL |  DL |
| --- | --- | --- | --- |
| A   |  1  |  2  |  3  |
| SL  |  4  |  5  |  6  |
| DL  |  7  |  8  |  9  |

Here's the code for the A-A combination

    static <T> boolean equals(T s1[], T s2[]) {
            int j = 0; 
            for (int i = 0; i != s1.length; ++i) {
                    if (j == s2.length || s1[i] != s2[j])
                            return false;
                    ++j;
            }
            return j == s2.length;
    }

Student in-class exercise: implement the SL-A version (without iterators).

*Abstraction* is the act of removing characteristics that are not
relevant to your immediate purpose while retaining the characteristics
that are necessary.

The `Sequence` and `Iter` interfaces provide an abstract view of a
sequence of elements for the purpose of algorithms that need to
traverse sequences and inspect their elements.

The algorithms speak the 'iterator' language (`i.get()`, `i.advance()`).
Each iterator implementation translates to a different 'data structure' language,
e.g., the array language (`A[i]`, `++i`).

    | Data Structure   | Iter Implementation            |   Abstraction                   | Algorithms   |
    | ---------------- | ------------------------------ | ------------------------------- | ------------ |
    | Array            | ArrayIter (`A[i]`,`++i`)       | Iter (`i.get()`,`i.advance()`)  | `equals`     |
    | LinkedList       | ListIter (`n.data`,`n=n.next`) |                                 | `find_first` |
    | DoublyLinkedList | DLIter   (`n.data`,`n=n.next`) |                                 | `max`        |

Using the `Sequence` and `Iter` interfaces, we can implement 1 version
of `equals` that does the same job as all 9 specific versions! (And
many more!)

    public static <T, U> boolean equals(Sequence<T> s1, Sequence<U> s2, BiPredicate<T,U> eq) {
        Iter<T> j = s2.begin();
        for (Iter<T> i = s1.begin(); ! i.equals(s1.end()); i.advance()) {
            if (j.equals(s2.end()) || ! eq.test(i.get(), j.get()))
                    return false;
            j.advance();
        }
        return j.equals(s2.end());
    }


    LinkedList<Integer> L = ...
    ArrayList<Integer> A = ...
    equals(L, A)

Student in-class exercise: implement a `max` algorithm that returns the maximum
element of a `Sequence`.

    public static <T> T max(Sequence<T> s, T zero, BiPredicate<T, T> less);

Here's what you need to know about `BiPredicate`:

    interface BiPredicate {
	    boolean test(T t, U u);
	}
    
Student in-class exercise: implement Sequence using an array


Now the algorithms on `Sequence` (`find_first_equal`, `equals`, etc.)  can
also be used with Array!

    Array<Integer> B = new Array<Integer>();
    for (int i = 0; i != 20; ++i) {
      B[i] = i;
    }
    Iter<Integer> i = find_first_equal(B, 10);
    assert i.get() == 10;

    Iter<Integer> i = find_first_equal(B, 30);
    assert i == B.end();

The solutions to the in-class exercises are [here](./Sep-6-solutions.md).
