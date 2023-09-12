# Binary Search Trees

Idea: use binary trees to implement the Set interface, such that doing
a search for an element is like doing binary search.

The **Binary-Search-Tree Property**:
For every node x in the tree,
1. if node y is in the left subtree of x, then `y.data < x.data`, and
2. if node z is in the right subtree of x, then `x.data < z.data`.

We are considering BSTs that do not allow duplicates.

We can also use BSTs to implement the Map interface (aka. "dictionary").

```java
interface Map<K,V> {
   V get(K key);
   V put(K key, V value);
   V remove(K key);
   boolean containsKey(K key);
}
```

## Binary Search Tree and Node Classes

`BinarySearchTree` is like `BinaryTree` (has a `root`) but also
has a `lessThan` predicate for comparing elements.
We define `Node` as a class nested inside `BinarySearchTree` for
convenience:


``` java
public class BinarySearchTree<K> {

    static class Node<K> {
        K data;
        Node<K> left, right, parent;
        // ...
    }

    Node<K> root;
    protected BiPredicate<K, K> lessThan;
    // ...
}
```

<!-- ## `find`, `search`, and `contains` methods of `BinarySearchTree`. -->

<!-- Example: Search for 6, 9, 15 in the following tree: -->

<!--           8 -->
<!--         /   \ -->
<!--        /     \ -->
<!--       3       10 -->
<!--      / \        \ -->
<!--     1   6       14 -->
<!--        / \     / -->
<!--       4   7   13 -->

<!-- Find the node with the specified key, or if there is none, the parent of -->
<!-- where such a node would be. -->

<!--     protected Node find(K key, Node curr, Node parent) { -->
<!--         if (curr == null) -->
<!--             return parent; -->
<!--         else if (lessThan(key, curr.data)) -->
<!--             return find(key, curr.left, curr); -->
<!--         else if (lessThan(curr.data, key)) -->
<!--             return find(key, curr.right, curr); -->
<!--         else -->
<!--             return curr; -->
<!--     } -->

<!--     public Node search(K key) { -->
<!--         Node n = find(key, root, null); -->
<!--         if (n != null && n.data.equals(key)) -->
<!--             return n; -->
<!--         else -->
<!--             return null; -->
<!--     } -->

<!--     public boolean contains(K key) { -->
<!--         return search(key) != null; -->
<!--     } -->

<!-- What is the time complexity? answer: O(h), where h is the -->
<!-- height of the tree -->

<!-- ## **Student in-class exercise**:  -->

<!-- `insert` into a binary search tree using the `find` method. -->

<!-- Return the inserted node, -->
<!-- or null if the key is already in the tree. -->









<!-- Solution: -->

<!--     public Node insert(K key) { -->
<!--         Node n = find(key, root, null); -->
<!--         if (n == null){ -->
<!--             root = new Node(key); -->
<!--             return root; -->
<!--         } else if (lessThan(key, n.data)) { -->
<!--             Node x = new Node(key); -->
<!--             n.left = x; -->
<!--             return x; -->
<!--         }  else if (lessThan(n.data, key)) { -->
<!--             Node x = new Node(key); -->
<!--             n.right = x; -->
<!--             return x; -->
<!--         } else -->
<!--             return null; -->
<!--     } -->

<!-- What is the time complexity? answer: O(h) where h is the height of the tree. -->


<!-- ## Remove node z -->

<!-- * Case 1: (no left child) -->

<!--           |              | -->
<!--         z=o              A -->
<!--            \       ==> -->
<!--             A -->

<!-- * Case 2: (no right child) -->

<!--             |            | -->
<!--           z=o            A -->
<!--            /       ==> -->
<!--           A -->

<!-- * Case 3: Two children -->

<!--              | -->
<!--            z=o -->
<!--             / \ -->
<!--            A   B -->

<!--     The main idea is to replace z with the node after z, which -->
<!--     is the first node y in subtree B. -->

<!--     Two cases to consider: -->

<!--     Case a) B is y -->

<!--              |                  | -->
<!--            z=o        ==>       y -->
<!--             / \                / \ -->
<!--            A   y              A   C -->
<!--                 \ -->
<!--                  C -->

<!--     Case b) B is not y (y is properly inside B) -->

<!--              |                  | -->
<!--            z=o        ==>       y -->
<!--             / \                / \ -->
<!--            A   B             A    B -->
<!--               ...                ... -->
<!--                |                  | -->
<!--                y                  C -->
<!--                 \ -->
<!--                  C       -->

<!--     What is the time complexity? answer: O(h) where h is the height -->

<!-- Solution for `remove`: -->

<!--     public void remove(T key) { -->
<!--        Node n = remove_helper(root, key); -->
<!-- 	   if (n != null) { -->
<!-- 	      root = n; -->
<!-- 	   } -->
<!--     } -->

<!--     private Node remove_helper(Node n, int key) { -->
<!--         if (n == null) { -->
<!--             return null; -->
<!--         } else if (lessThan(key, n.data)) { // remove in left subtree -->
<!--             n.left = remove_helper(n.left, key); -->
<!-- 			n.left.parent = n; -->
<!--             return n; -->
<!--         } else if (lessThan(n.data, key)) { // remove in right subtree -->
<!--             n.right = remove_helper(n.right, key); -->
<!-- 			n.right.parent = n; -->
<!--             return n; -->
<!--         } else { // remove this node -->
<!--             if (n.left == null) { -->
<!--                 return n.right; -->
<!--             } else if (n.right == null) { -->
<!--                 return n.left; -->
<!--             } else { // two children, replace with first of right subtree -->
<!--                 Node min = n.right.first(); // min == n.right -->
<!--                 n.data = min.data; -->
<!--                 n.right = n.right.delete_first(); // another helper function -->
<!-- 			    n.right.parent = n; -->
<!--                 return n; -->
<!--             } -->
<!--         } -->
<!--     } -->
