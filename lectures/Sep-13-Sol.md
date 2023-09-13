## Solution for in-class exercise

```java
    public Node<K> insert(K key) {
        Node<K> n = find(key, root, null);
        if (n == null){
            root = new Node<K>(key);
            return root;
        } else if (lessThan.test(key, n.data)) {
            Node<K> x = new Node<K>(key);
            n.left = x;
            return x;
        }  else if (lessThan.test(n.data, key)) {
            Node<K> x = new Node<K>(key);
            n.right = x;
            return x;
        } else
            return null;
    }
```
