# Union-Find and Incremental Connected Components

## Overview

* Example Applications
    * tracking relatives
    * Solving equations on trees (unification)
    * Huet's unification algorithm
* The Disjoint-Sets ADT
* Linked-List Implementation
    * basics and time complexity
    * weighted union heuristic and time complexity
* Tree Implementation
    * union-by-rank
    * path compression
* Incremental Connected Components

## Tracking relatives

* Suppose we would like to keep track of who is related to who
  in the sense of family relationships in the United States.

* We say that person A is *related to* person B if either

    1) A is directly related to B via parent/child or marriage
    2) A and B are really the same person (reflexive) 
    3) B is related to A (symmetric)
    4) A is related to A' and A' is related to B (transitive)

* We can group all the people in the United States into partitions
  such that everyone in each partition is related to everyone
  else, and people from different partitions are not related.
  Think of each partition as a mega-family. The technical terms
  for each partition is an *equivalence class*.

* We want a fast way of answering the question of whether two people
  are related.

* We also want a fast way to adjust the relationships when

    1) new people are born
    2) couples get married

* The notion of *representative* is helpful in these regards.
  Choose one person to be the head of the mega-family, that is, to
  be the representative. Then ask everyone in the mega-family
  to remember who the head is.

    * Are two people related? ==> Do they have the same representative?
    * new person is born ==> New person gets the same representative as the parent.
    * marriage ==> If the two people are from different
      mega-families, then we pick the rep. of the larger
      mega-family to be the rep.  for everyone in both
      mega-families, merging them into one big mega-family.
      Everyone from the smaller mega-family need to learn a new
      name (the rep).

* Example: start with the following partition into disjoint sets.
   The representative is marked with a #.

    { #Jane } , { #Larry } , { #Susan }, { #Sam } , { #Linda }

    That is, no one is related. Everyone is their own representative.

    * Linda and Sam get married.

        { #Jane } , { #Larry } , { #Susan }, { #Sam , Linda }

    * Susan and Larry get married.

        { #Jane } , { Larry , #Susan }, { #Sam , Linda }

    * They have a son named Adam.

        { #Jane } , { Larry , #Susan, Adam }, { #Sam , Linda }

    * Jane and Adam get married.

        { Jane , Larry , #Susan, Adam }, { #Sam , Linda }

## Disjoint Sets Interface

        interface DisjointSets<N> {
            void make_set(N x);
            N find(N x);
            N union(N x, N y);
        }

## Linked-List Implementation

* Maintain one linked-list for each partition.

* Each node in the list contains a pointer to the list as a whole.

* The first element in the list is the representative

* `make_set`: create a list with one element

* `find`: follow the element's pointer to the whole list
   then take the head element

* `union`: append one list to the other, then update the pointer in the
   elements of one of the lists.

* time complexity

    n: the number of `make_set` operations (i.e., the number of elements)

    m: the number of `make_set`, `union`, and `find_set` operations

    `make_set`: O(1)

    `find`: O(1)

    Suppose we have n `make_set` operations followed by n-1
       `union` operations (so there's only one partition left).

    So the total number of operations is m = 2n - 1.

    The `make_set` operations take a total of O(n) time.

    The `union` operations take time proportional to the length of
    the lists, which grows from 1 to n-1. So, n-1 unions take
    O(n²) time.

    Total: O(n²)

* weighted union heuristic and time complexity

    During a `union`, always append the shorter list to the longer
    one. That way we're updating fewer pointers.

    Now, during the course of the m operations, each of the n elements
    is moved from one list to another list only log(n) times.

    Total: O(m + n log(n))

## Tree Implementation

Maintain a separate tree/forest structure in which each tree
contains one partition of elements with the root as the
representative.

Each node in the tree has a parent pointer.

* Basic implementation

          class UnionFind<N> implements DisjointSets<N>
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

* union-by-rank, by itself O(log n)

    We attach numbers called **rank** to each node.  They would be
    the same as height except that we let them go stale by not
    updating them when we do path compression during a `find`.
    But that's OK; using the stale ranks to choose the representative
    is good enough to stay balanced.

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

* With both path compression and union-by-rank, the
  time complexity is O(m α(n)) where α is the 
  inverse Ackerman function (grows really slowly).

## Student Exercise

Use the basic union-find to track relatives in the above example.


## Application: solving equations on labeled trees

Example:

        F      =        F
       / \             / \
     X1   X2          G   G
                      |   |
                      X2  X3

Can also be written as terms:

F(X1, X2) = F(G(X2), G(X3))

The solution is

      X1 = G   X2 = G   X3 = X3
           |        |
           G        X3
           |
           X3

      X1=G(G(X3)), X2=G(X3), X3=X3

So we have

F(G(G(X3)), G(X3)) = F(G(G(X3)), G(X3))

Intuition behind how to solve the equations:
propagate the equalities to sub-trees.

From the above equation we get the following two equations:

      X1 = G    X2 = G
           |         |
           X2        X3

In general, the process needs to keep track of several sets of
things that are equal, that is, we need to partition all of the
trees into sets containing equal trees.

Unification algorithm:

        class Node {
            String label;
            public Node[] kids;
            ...
        };
        class Equation {
            Equation(Node l, Node r) { lhs = l; rhs = r; }
            Node lhs; Node rhs;
        }

        static void
        unify(Node t1, Node t2, DisjointSets<Node> sets)
        {
            init(t1, sets); 
            init(t2, sets);
            LinkedList<Equation> equations = new LinkedList<Equation>();
            equations.add(new Equation(t1,t2));
            while (equations.size() != 0) {
                Equation e = equations.pop();
                Node u = sets.find(e.lhs);
                Node v = sets.find(e.rhs);
                if (u != v) {
                    if (u instanceof Variable
                        || v instanceof Variable)
                        sets.union(u,v);
                    else if (u.label.equals(v.label)) {
                        sets.union(u, v);
                        if (u.kids.length != v.kids.length)
                            return null;
                        for (int i = 0; i != u.kids.length; ++i)
                            equations.add(new Equation(u.kids[i], v.kids[i]));
                    } else
                        return null;
                }
            }
            return sets;
        }

        static void init(Node t, DisjointSets<Node> sets) {
            sets.make_set(t);
            for (int i = 0; i != t.kids.length; ++i) {
                init(t.kids[i], sets);
            }
        }
            

## Incremental Connected Components

Def. A *connected component* is a maximal subset of
  vertices C in an undirected graph such that
  for every u and v in C, u \Rightarrow v.

- Suppose the graph keeps changing, with additional edges, and we need to
  continuously keep track of the connected components.

- The algorithm for connected components is straightforward
  to expressing using disjoint sets.

          static <V> void connected_components(Graph<V> G,
                                               DisjointSets<V> sets) {
              // put every vertex in a partition by itself
              for (V v : G.vertices())
                  sets.make_set(v);

              // union partitions that have an edge between them
              for (V u : G.vertices())
                  for (V v : G.adjacent(u))
                      if (sets.find(u) != sets.find(v))
                          sets.union(u,v);
          }

- When another edge (u,v) is added to the graph,
  simply call `sets.union(u,v)`.

- You can determine which component a vertex v is in by
  calling `sets.find_set(v)`.

Example:

          a--b   e--f  h   j
          | /|   |     |
          |/ |   |     |
          c--d   g     i

          initial partitions  {a} {b} {c} {d} {e} {f} {g} {h} {i} {j}
           edge processed
           (b,d)              {a} {b,d} {c} {e} {f} {g} {h} {i} {j}
           (e,g)              {a} {b,d} {c} {e,g} {f} {h} {i} {j}
           (a,c)              {a,c} {b,d} {e,g} {f} {h} {i} {j}
           (h,i)              {a,c} {b,d} {e,g} {f} {h,i} {j}
           (a,b)              {a,c,b,d} {e,g} {f} {h,i} {j}
           (e,f)              {a,c,b,d} {e,g,f} {h,i} {j}
           (b,c)              {a,c,b,d} {e,g,f} {h,i} {j}

- Time complexity:  (n vertices, m edges)
  All the work is in the union-find operations, so let's count those.

    1. n calls to `make_set`
    2. m calls to `union`
    3. 2n calls to `find`

    The time complexity for (3n + m) union-find operations is
    O((3n+m) α(n)) = O((n + m) α(n)).

## Student Exercise

Compute the connected components of the following graph.

        V = {a,b,c,d,e,f,g,h,i,j,k}
        E = { (d,i),(f,k),(g,i),(b,g),(a,h),(i,j),(d,k),(b,j),(d,f),(g,j),(a,e) }

        solution:

        {a,e,h}
        {c}
        {b,d,g,i,j,f,k }
