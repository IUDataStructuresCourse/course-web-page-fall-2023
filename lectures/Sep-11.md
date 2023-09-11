# More Abstract Data Types

## Stack (LIFO)

analogy: stack of pancakes

	interface Stack<E> {
		void push(E d);
		E pop();
		E peek();
		boolean empty();
	}

Example uses: 

* reverse an array
* check for matching parentheses (with multiple kinds: square, round, curly)
* parsing (e.g. HTML)
* depth-search search
* procedure call stack

Implementation:

* array (push is add to the end, pop removes from end)
* singly-linked list (push on front, pop from front)

## Queue (FIFO)

Analogy: checkout line at a grocery store 

	interface Queue<E> {
		void enqueue(E e); // aka. push
		E dequeue(); // aka. pop
		E front();
		boolean empty();
	}

Example uses: 

* breadth-first search, 
* requests to a shared resource (e.g., printer),
* interupt handling inside an OS kernel, 
* buffering to handle asynchronous communiation, such as interprocess IO,
	disk access, etc.

Implementation: 

* array
* doubly-linked list

## Set

Like a set in mathematics. A collection of elements where the ordering
of the elements is not important, only membership matters.
`Set` ignores duplicates.

	interface Set {
	   void insert(int e);
	   void remove();
	   boolean member(int e);
	   boolean empty();
	   Set union(Set other);
	   Set intersect(Set other);
	   Set difference(Set other);
	}

Implementations of `Set` are the topic of several future lectures.

# Binary Search Trees

## Motivation 

Taking stock with respect to Flood-it! and the Set ADT

|                  |  array | linked list | sorted array |
| ---------------- |:------:|:-----------:|:------------:|
| membership test  | O(n)   | O(n)        | O(log(n))    |
| insertion        | O(1)   | O(1)        | O(n)         |

* How do we keep the array sorted when inserting more elements?

	Use binary search for insertion.

	What's the time complexity?

	O(n) because we still have to shift elements down.

* We'd like something that can do both membership test and insertion in
  O(lg n).

* Can we create a hybrid of the sorted array and the linked list?

	Yes: binary search trees!

* This lecture we discuss binary trees as a lead-up to discussing
  binary search trees in the next lecture.

## Binary Node and Tree Classes

	class BinaryNode<T> {
		T data;
		BinaryNode<T> left;
		BinaryNode<T> right;

		BinaryNode(T d, BinaryNode<T> l, BinaryNode<T> r) {
				data = d; left = l; right = r;
		}
	}

	public class BinaryTree<T> {
		BinaryNode<T> root;

		public BinaryTree() { root = null; }
		// ...
	}

We can traverse a binary tree in several different ways:
* preorder: me, left, right
* inorder: left, me, right
* postorder: left, right, me

Example:

				 10
				/ \
			   /   \
			  5     14
			 / \     \
			2   7     19

			pre:  10,5,2,7,14,19
			in:   2,5,7,10,14,19
			post: 2,7,5,19,14,10


## Next and Previous operations with respect to inorder traversal

Running example binary tree:

          8
        /  \
       /    \
      3     10
     / \      \
    1   6      14
       / \    /
      4   7  13
    
### Helper functions first and last

Find the first node according to an inorder traversal
within a subtree. 

Examples:

What's the first node in the above tree? answer: 1

What's the first node of the subtree rooted at 6? answer: 4

	public BinaryNode<T> first();
	
Algorithm: Go to the left as far as you can.

Find the last node according to an inorder traversal.

	public BinaryNode<T> last();

Algorithm: Go to the right as far as you can.

### Helper functions nextAncestor and prevAncestor

Given a node, find the ancestor that would come next
according to an inorder traversal, or return null
if there is none.

You may assume that each node has a `parent` attribute.

**Student in-class exercise**:

	BinaryNode<T> nextAncestor();
	
keep going up so long as the node is the right child, 
when left child, return parent
	
	

Solution:

	BinaryNode<T> nextAncestor() {
		if (parent != null && this == parent.right) {
			return parent.nextAncestor();
		} else {
			return parent;
		}
	}

Given a node, find the ancestor that would come before the node
according to an inorder traversal, or return null if there is
none.

	BinaryNode<T> prevAncestor();

### How to find the next node according to an inorder traversal?

Examples:

What is after 3? Answer: 4.

What is after 7? Answer: 8.

If their is a right child, get the first node in the subtree.
Otherwise, find the next ancestor.

	public BinaryNode<T> next() {
		if (right == null) {
		   return nextAncestor();
		} else {
		   return right.first();
		}
	}

What is the time complexity? answer: $O(h)$ where $h$ is
the height of the tree.

Finding the previous node is similar.
