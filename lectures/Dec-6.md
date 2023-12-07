# Review for Final Exam

## Topics for Final Exam

* Hash tables
* Heaps, Priority Queues
* Binomial Queues
* Sorting
    * quicksort, partition
    * counting sort
    * radix sort
* Graphs
    * BFS, DFS
    * Shortest Paths: Dijkstra's
    * Connected Components
    * Minimum Spanning Trees: Kruskal's
* Union-find (aka. Disjoint Sets)
* Backtracking
* Dynamic Programming
    * Rod cutting
    * DNA Sequence Alignment

## Hash tables

* Dictionary/Map ADT: `get`, `put`, `remove`, `containsKey`
* Direct-address tables
* Prehashing: e.g. mapping strings to integers
* Hash functions
    * division method (modulo)
    
        h(k) = k mod m
        
      where m is the table size
      
    * multiply-add-and-divide method
    
            h(k) = ((a * k + b) mod p) mod m
            
      where
      p is a prime number larger than m and
      a,b are randomly chosen integers between 1 and p-1.
    
* Collision handling by separate chaining

## Heaps

               ___16___
              /        \
             14          10
            /  \        /  \
           /    \      /    \
          8      7    9      3
         / \    /
        2   4  1

  Def. A *max heap* is a heap in which for every node other than the root, 
     A[i] ≤ A[parent(i)]

  * Operation: max_heapify(H, i)
    The tree rooted at i is not a max heap, but the subtrees
    left(i) and right(i) are max heaps. The resulting tree is a max heap.
    Percolate the 4 down to the right spot.

                     ___16___
                    /        \
                  *4*         10
                 /  \        /  \
                /    \      /    \
               14      7   9      3
              / \    /
             2   8  1

    becomes

                     ___16___
                    /        \
                  14          10
                 /  \        /  \
                /    \      /    \
               8      7    9      3
              / \    /
             2  *4* 1

    code:
    
        void max_heapify(int i, int length) {
            int l = left(i); int r = right(i);
            int largest = i;
            if (l < length && data[i] < data[l])
                largest = l;
            if (r < length && data[largest] < data[r])
                largest = r;
            if (largest != i) {
                swap(data, i, largest);
                max_heapify(largest);
            }
        }

  * build_max_heap(A): turns an arbitrary array into a heap

                 ___1____
                /        \
               2          3
             /  \        / \
            /    \      /   \
           4      7    8     9
          / \    /
        10   14 16

    Max-Heapify the last parent: 7

                 ___1____
                /        \
               2          3
             /  \        / \
            /    \      /   \
           4      16    8     9
          / \    /
        10   14 7

    Max-Heapify the next parent: 4

                 ___1____
                /        \
               2          3
             /  \        / \
            /    \      /   \
           14     16   8     9
          / \    /
        10   4  7

    Max-Heapify the next parent: 3

                 ___1____
                /        \
               2          9
             /  \        / \
            /    \      /   \
           14     16   8     3
          / \    /
        10   4  7

    Max-Heapify the next parent: 2

                 ___1____
                /        \
              16          9
             /  \        / \
            /    \      /   \
           14     7    8     3
          / \    /
        10   4  2

    Max-Heapify the next parent: 1

                 ___16___
                /        \
              14          9
             /  \        / \
            /    \      /   \
           10     7    8     3
          / \    /
         1   4  2

    code:
    
        void build_max_heap() {
            int last_parent = length / 2 - 1;
            for (int i = last_parent; i != -1; --i) {
                max_heapify(i);
            }
        }

## Depth-first search

- Depth-first search is a graph search algorithm.
- From a given starting node, it determines which other nodes are
  reachable from it.
- Example:

        A--->B--->C
        |    ^    |
        V    |    |
        D--->E<---|

    Depth-first trees:

        A----B----C
        |         |
        |         |
        D    E----|

        A    B----C
        |    |
        |    |
        D----E

- Code:

        static <V> void DFS_visit(Graph<V> G, V u, Map<V,V> parent,
                      Map<V,Boolean> visited) {
        visited.put(u, true);
        for (V v : G.adjacent(u))
            if (! visited.get(v)) {
            parent.put(v, u);
            DFS_visit(G, v, parent, visited);
            }
        }

- Example with discover/finish times

        1/10  2/7  3/6
        A----B----C
        |         |
        |         |
        D    E----|
        8/9  4/5

        1/10  4/7  5/6
        A    B----C
        |    |
        |    |
        D----E
        2/9  3/8
    
## Breadth-first Search

- Solves the single-source shortest-hops problem
  What is the shortest path from A to E, A to B?
  DFS doesn't guarantee that it's paths are the shortest.

- Algorithm idea: expand tree one level at a time.

- Example:

        A--->B--->C
        |    ^    |
        V    |    |
        D--->E<---|

    Breadth-first trees:

        A----B----C
        |
        |
        D----E

    Show the queue at each step

- Code:

          static <V> void BFS(Graph<V> G, 
                              V start, 
                              Map<V,Boolean> visited,
                              Map<V,V> parent) {
              for (V v : G.vertices())
                  visited.put(v, false);
              LinkedList<V> Q = new LinkedList<V>();
              Q.add(start);
              parent.put(start, start);
              visited.put(start, true);
              while (Q.size() != 0) {
                  V u = Q.remove();
                  for (V v : G.adjacent(u))
                      if (! visited.get(v)) {
                          parent.put(v, u);
                          Q.add(v);
                          visited.put(v, true);
                      }
              }
          }
    
## Disjoint-sets (aka Union-Find)

* Example problem:

    Which people live in the same state?
    - You're given assertions that two people live in the same state.
    - You're asked, based on the assertions so far,
      whether two people definitely live in the same state. 

    example:

            Universe: Mary, Katie, Jeremy, Luke, John

                                  {Mary} {Katie} {Jeremy} {Luke} {John}
            Mary-Katie !       {Mary, Katie} {Jeremy} {Luke} {John}
            Jeremy-Luke !      {Mary, Katie} {Jeremy, Luke} {John}
            Katie-John !       {Mary, Katie, John} {Jeremy, Luke}
            Mary-John ?    Yes
            Jeremy-John ?  No
            Luke-John !           {Mary, Katie, John,Jeremy, Luke}
            Jeremy-John ?  Yes

* Basic implementation

    idea: the set is represented as a forest, with one tree per partition.
    The root of the tree is the representative.

        public class UnionFind<N> implements DisjointSets<N>
        {
            Map<N,N> parent;

            public UnionFind(Map<N,N> p) {
                parent = p; 
            }
            public void make_set(N x) {
                parent.put(x, x);
            }
            public N find(N x) {
                if (x == parent.get(x))
                    return x;
                else 
                    return find(parent.get(x));
            }
            public N union(N x, N y) {
                N rx = find(x); N ry = find(y);
                parent.put(ry, rx);
                return rx;
            }
        }

* path compression

        public N find(N x) {
            if (x == parent.get(x))
                return x;
            else {
                N rep = find(parent.get(x));
                parent.put(x, rep);
                return rep;
            }
        }

* union-by-rank

        public void make_set(N x) {
            parent.put(x, x);
            rank.put(x, 0);
        }

        public N union(N x, N y) {
            N rx = find(x);
            N ry = find(y);
            if (rank.get(rx) > rank.get(ry)) {
                parent.put(ry, rx);
                return rx;
            } else {
                parent.put(rx, ry);
                if (rank.get(ry) == rank.get(rx))
                    rank.put(ry, rank.get(ry) + 1);
                return ry;
            }
        }

## Minimum Spanning Trees

- Kruskal's algorithm

    1. Order all the edges by their weight

    2. initialize a union-find data structure with every vertex by itself

    2. For each edge in this ordering:

        If the two vertices connected by the edge have different
        representatives, then add this edge to the tree and union the
        sets associated with the two vertices.

- Student group work: apply Kruskal's to the following graph

        A--2--B--3--C--1--D
        |     |     |     |
        3     1     2     5
        |     |     |     |
        E--4--F--3--G--3--H
        |     |     |     |
        4     2     4     3
        |     |     |     |
        I--3--J--3--K--1--L

    Solution: 
    sorted edges:

        C-D,B-F,K-L,  A-B,C-G,F-J, B-C,A-E,F-G,G-H,H-L,I-J,J-K, E-F,E-I,G-K, D-H

    An MST of weight 24:

         A--2--B--3--C--1--D
         |     |     |
         3     1     2     5
         |     |     |
         E  4  F  3  G--3--H
               |           |
         4     2     4     3
               |           |
         I--3--J  3  K--1--L

## Shortest Paths versus Minimum Spanning Trees

    A--1--B--1--C
    |           |
    4           1
    |           |
    D----2------E

What's the shortest path from 

    A to D? A-D (4)
    A to C? A-B-C (2)
    A to E? A-B-C-E (3)

What's the MST?

    A-B-C-E-D (5)

## DNA Sequence Alignment

Try to insert gaps (_) to cause the two sequences
to match up the best.

Example:

    CTGA    CTGA_  score = -2 +2 -1 +2 -1 = 0
    ATAC    AT_AC

            C_TGA_ sore = -1 -1 +2 -1 +2 -1 = 0
            _AT_AC

Working back-to-front, what choices can be made
to characterize all possible alignments?
For the last column you can either
1. take the last character of both strings to be aligned, or
2. add a gap to s1 and take the last character of s2.
   This can be thought of as an "insertion".
3. add a gap to s2 and take the last character of s1.
   This can be thought of as an "deletion".

The recursive brute-force solution, shown below, tries all three
of the above options and picks the best one.

Recursive function for computing a best alignment:

        int align(String s1, String s2, int i, int j) {
           if (i == 0 && j == 0) {
              return 0;
           } else if (i == 0) {
              return j * -1;
           } else if (j == 0) {
              return i * -1;
           } else {
              int M = align(s1, s2, i-1, j-1) + score(s1[i-1],s2[j-1]);
              int I = align(s1, s2, i, j-1) - 1;
              int D = align(s1, s2, i-1, j) - 1;
              return max(M, I, D);
           }
        }

We can then turn this into a dynamic-programming solution
with a table to record the results of subproblems and
two loops to proceed in a bottom-up fashion.

        T[0][0] = 0
        for (int i = 1; i != m + 1; ++i) {
            T[i][0] = i * -1
        }
        for (int j = 1; j != n+1; ++j) {
            T[0][j] = j * -1;
        }
        for (int i = 1; i != m+1; ++i) {
            for (int j = 1; j != n+1; ++j) {
                int M = T[i-1][j-1] + s(s1[i-1], s2[j-1]);
                int D = T[i-1][j] + -1;
                int I = T[i][j-1] + -1;
                T[(i, j)] = max(M, D, I);
            }
        }

Now let's try this on an example:
align ACGT and ATAG

                        j
                    A    T     A     G
             0    I:-1  I:-2  I:-3  I:-4
          A D:-1  M:2   I:1   I:0   I:-1
        i C D:-2  D:1   M:0   I:-1  D:-2
          G D:-3  D:0   I:-1  M:-2  M:1
          T D:-4  D:-1  M:2   D:1   D:0

So an answer is

        A C _ G T
        A T A G _
        2-2-1+2-1 = 0

