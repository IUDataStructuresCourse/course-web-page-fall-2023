# Backtracking

Backtracking is exhaustive search (like depth-first-search) with
pruning. It is applicable anytime a solution to the problem can be
broken into partial solutions, and there is a way to prune partial
solutions that can't be extended to a full solution.

The general recipe for backtracking is to write a recursive function
of the following form.

    solve(partialSolution) =
      if prune(partialSolution) 
        return null
      if accept(partialSolution)
        return partialSolution
      else
        for every way to extend partialSolution to partialSolution2:
          partialSolution3 = solve(partialSolution2)
          if partialSolution3 != null
            return partialSolution3
        return null

Example: Sudoku (world's hardest by the mathematician Arto Inkala)

    800 000 000
    003 600 000
    070 090 200

    050 007 000
    000 045 700
    000 100 030

    001 000 068
    008 500 010
    090 000 400

For each square, we record the set of all available numbers that are
still valid choices, a technique called *pencil marks*.

We can `prune` a board if there is a square that no longer has any
valid choices. Let `m` and `n` be the number of rows and columns.

    static boolean prune(Set<Integer>[][] PM, int m, int n) {
        for (int i = 0; i != m; ++i)
            for (int j = 0; j != n; ++j)
                if (PM[i][j].isEmpty())
                    return true;
        return false;
    }
    
We succeed, and `accept` a board, if all the squares have been filled
in with non-zero numbers.

    static boolean accept(int[][] board, int m, int n) {
        for (int i = 0; i != m; ++i) {
            for (int j = 0; j != n; ++j) {
                if (board[i][j] == 0)
                    return false;
            }
        }
        return true;
    }

If there are still choices to be made, the question is which square to
fill-in next. 

Of course, it should be a square that has not yet been filled-in, that
is, a square with a `0`.

We can speed up the search by using a **most-constrained-first**
strategy. In this setting, a square is most-constrained if it has the
fewest options left, i.e., the smallest pencil-marks set.  The idea is
that we better deal with the most-constrained square now because if
instead we make other choices, the most-constrained square may become
unsolvable.
  
    static Coord
    most_constrained(int[][] board, Set<Integer>[][] PM, int m, int n)
    {
        int fewest_marks = Integer.MAX_VALUE;
        Coord best = null;
        for (int i = 0; i != m; ++i)
            for (int j = 0; j != n; ++j) {
                if (board[i][j] == 0 && PM[i][j].size() < fewest_marks) {
                    fewest_marks = PM[i][j].size();
                    best = new Coord(i,j);
                }

            }
        return best;
    }

With the identification of the most-constrained square at some location `i,j`,
we recursively investigate all the choices for that square. 
For each choice `k`, we must update the pencil marks accordingly.

    static void
    update_marks(Set<Integer>[][] PM, int i, int j, int k, int m, int n)
    {
        for (int ii = 0; ii != m; ++ii) // same column
            if (ii != i)
                PM[ii][j].remove(k);
        for (int jj = 0; jj != n; ++jj) // same row
            if (jj != j)
                PM[i][jj].remove(k);
        int a = i / 3;
        int b = j / 3;
        for (int ii = a * 3; ii != (a+1)*3; ++ii) // same 3x3 region
            for (int jj = b * 3; jj != (b+1)*3; ++jj) {
                if (! (i == ii && j == jj))
                    PM[ii][jj].remove(k);
            }
    }

In the recursive calls to `solve`, we must make sure to use copies of
the current board and pencil marks so that the different choices don't
stomp on eachother.

Putting this altogether, here's a backtracking algorithm for Sudoku.

    public static int[][] 
    solve(int[][] board, Set<Integer>[][] PM, int m, int n) {
        if (prune(PM, m, n))
            return null;
        else if (accept(board, m, n))
            return board;
        else {
            Coord coord = most_constrained(board, PM, m, n);
            int i = coord.i, j = coord.j;
            for (int k : PM[i][j]) {
                int[][] new_board = copy(board, m, n);
                Set<Integer>[][] new_PM = copy_marks(PM, m, n);
                new_board[i][j] = k;
                update_marks(new_PM, i, j, k, m, n);
                int[][] result = solve(new_board, new_PM, m, n);
                if (result != null)
                    return result;
            }
            return null;
        }
    }

The solution generated in under a second for the world's hardest Sudoku
puzzle is:

    812 753 649
    943 682 175
    675 491 283

    154 237 896
    369 845 721
    287 169 534

    521 974 368
    438 526 917
    796 318 452
