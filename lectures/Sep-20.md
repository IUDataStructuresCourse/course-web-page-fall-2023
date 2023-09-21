# Lecture: AVL Trees Continued, Code Review: Flood It!

**Definition** The AVL Invariant: the height of two child subtrees may
only differ by 1.

Red-black trees are an alternative to AVL trees.
AVL is faster on lookup than red-black trees but slower on
insertion and removal because AVL is more rigidly balanced.


## Tree Rotation

                    y                         x
                   / \    right_rotate(y)    / \
                  x   C  --------------->   A   y
                 / \     <-------------        / \
                A   B     left_rotate(x)      B   C


## Insert example: insert(55)

                  41
                /    \
              20      65
             /  \     /
            11   29  50
                 /
               26

So 65 breaks the AVL invariant, and we have a zig-zag:

               65
              /
            50
              \
               55

A right rotation at 65 gives us a zag-zig, we're not making progress!

              65(y)                        50(x)
             /        right_rotate(65)       \
            50(x)     ---------------->      65(y)
              \                              /
               55(B)                       55(B)

Instead, let's try a left rotate at 50:

              65                           65
             /        left_rotate(50)     /
            50(x)     --------------->  55(y)
              \                         /
               55(y)                 50(x)

This looks familiar, now we can rotate right. 

                  65(y)                        55(x)
                 /      right_rotate(65)       /  \
               55(x)    --------------->    50(A)  65(y)
              /
            50(A)


## Example: Remove Node and fix AVL

Given the following AVL Tree, delete the node with key 8.
(This example has two nodes that end up violating the AVL
property.)

             8
           /   \
          5     10
         / \   / \
        2   6 9   11
       / \   \      \
      1   3   7      12
           \
            4
    
Solution: 
* Step 1: replace node 8 with node 9

               9
             /   \
            5     10
           / \     \
          2   6     11
         / \   \      \
        1   3   7      12
             \
              4

* Step 2: find lowest node that breaks the AVL property: node 10
* Step 3: rotate 10 left

               9
             /   \
            5      11
           / \    /  \
          2   6  10   12
         / \   \
        1   3   7
             \
              4

* Step 4: find lowest node that breaks the AVL property: node 9
* Step 5: rotate 9 right

              5
            /   \
           /     \
          2        9
         / \     /   \
        1   3   6     11
             \   \   /  \
              4   7 10   12


## Algorithm for fixing AVL property

Starting from the changed node, repeat the following up to the root of
the tree (because there can be several AVL violations).
* check whether node x is AVL, if not do the following.
* if height(x.left) ≤ height(x.right)

    1. if height(x.right.left) ≤ height(x.right.right)

        let k = height(x.right.right)

                    x k+2                                y ≤k+2
                   / \          left_rotate(x)          / \
               ≤k A   y k+1     ===============>  ≤k+1 x   C k
                     / \                              / \
                ≤k B   C k                      ≤k A   B ≤k

    2. if height(x.right.left) > height(x.right.right)

        let k = height(x.right.left)

              k+2 x                               y k+1
                 / \                            /   \
            k-1 A   z k+1    R(z), L(x)      k x     z k
                   / \      =============>    / \   / \
                k y   D k-1              k-1 A   B C   D k-1
                 / \
                B   C <k

* if height(x.left) > height(x.right)

    1. if height(x.left.left) < height(x.left.right)  (note: strictly less!)

        let k = height(x.left.right)

                    x k+2                                 z k+1
                   / \                                  /   \
              k+1 y   D k-1      L(y), R(x)          k y     x k
                 / \            =============>        / \   / \
            k-1 A   z k                              A   B C   D    <k
                   / \
                  B   C <k

    2. if height(x.left.left) ≥ height(x.left.right)  (note: greater-equal!)

        let k = height(l.left.left)

                  x k+2                                  y k+1
                 / \         right_rotate(x)            / \
            k+1 y   C k-1    ===============>        k A   x k+1
               / \                                        / \
            k A   B ≤k                                ≤k B   C k-1


## Time Complexity of Insert and Remove for AVL Tree

O(h) = O(log n)

## Using AVL Trees for sorting

* insert n items: O(n log(n))

* in-order traversal: O(n)

* overall time complexity: O(n log(n))


## ADT's that can be implemented by AVL Trees

* priority queue:
  insert, delete, min
  
* ordered set:
  insert, delete, min, max, next, previous
  

# Code Review: Flood It!

## Straightforward but slow

    public static void flood(WaterColor color,
                             LinkedList<Coord> flooded_list,
                             Tile[][] tiles,
                             Integer board_size) {
        int i;
        for (i = 0; i < flooded_list.size(); ++i) {
            List<Coord> neighbors = flooded_list.get(i).neighbors(tiles.length);
            for (int j = 0; j < neighbors.size(); ++j) {
                if (tiles[neighbors.get(j).getY()][neighbors.get(j).getX()].getColor().equals(color) 
				    && !flooded_list.contains(neighbors.get(j))) {
                    flooded_list.add(neighbors.get(j));
                }
            }
        }
    }

## Straightforward but fast

    public static void flood(WaterColor color,
                            LinkedList<Coord> flooded_list,
                            Tile[][] tiles,
                            Integer board_size) {
        HashSet<Coord> is_flooded = new HashSet<>(flooded_list);
        ArrayList<Coord> flooded_array = new ArrayList<>(flooded_list);
        for (int i = 0; i != flooded_array.size(); ++i) {
            Coord c = flooded_array.get(i);
            for (Coord n : c.neighbors(board_size)) {
                if (!is_flooded.contains(n)
                    && tiles[n.getY()][n.getX()].getColor() == color) {
                    flooded_array.add(n);
                    flooded_list.add(n);
                    is_flooded.add(n);
                }
            }
        }
    }

## Depth-first search

    public static void flood(WaterColor color,
                              LinkedList<Coord> flooded_list,
                              Tile[][] tiles,
                              Integer board_size) {
        for (int i = 0; i < flooded_list.size(); i++) {
            checkNeighbor(flooded_list.get(i), // get is O(n), solution: use an arraylist
			              board_size, flooded_list, color, tiles);
        }
    }

    public static void checkNeighbor(Coord currentTile,
                                     Integer board_size,
                                     LinkedList<Coord> flooded_list,
                                     WaterColor color,
                                     Tile[][] tiles){
        List<Coord> neighborsList = currentTile.neighbors(board_size);
        for (int i = 0; i < neighborsList.size(); i++) {
            Coord neighbor = neighborsList.get(i);
            if(!flooded_list.contains(neighbor)) { // contains is O(n)! solution: use a hashset
                  int x = neighbor.getX();
                  int y = neighbor.getY();
                    if(tiles[y][x].getColor() == color){
                        flooded_list.add(neighborsList.get(i));
                        checkNeighbor(neighborsList.get(i), board_size, flooded_list, color, tiles);
                    }
            }
        }
    }
	
## Breadth-first search

    public static void flood(WaterColor color,
                             LinkedList<Coord> flooded_list,
                             Tile[][] tiles,
                             Integer board_size) {
        HashSet<Coord> is_flooded = new HashSet<>(flooded_list);
        boolean[][] visited = new boolean[board_size][board_size];
        ArrayList<Coord> queue = new ArrayList<>(flooded_list);
        for (Coord c : queue) {
            visited[c.getY()][c.getX()] = true;
        }
        while (queue.size() > 0) {
            Coord c = queue.remove(0);
            for (Coord n : c.neighbors(board_size)) {
                if (!visited[n.getY()][n.getX()] && tiles[n.getY()][n.getX()].getColor() == color) {
                    if (is_flooded.add(n)) {
                        queue.add(n);
					    flooded_list.add(n);
	                }
                }
                visited[n.getY()][n.getX()] = true;
            }
        }
    }

