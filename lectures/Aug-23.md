# Lecture: Arrays, Rotation, and Correctness Proofs

## Arrays in Java

Create an array of length `n`

```
int n = 10;
int[] A = new int[n];
```

Set the element at a given position:

```
A[0] = 0;
A[1] = 1;
A[2] = 4;

for (int i = 3; i != n; ++i)
  A[i] = i * i;
```

Now we have an array that looks like the following

    elements: 0, 1, 4, 9, 16, 25, 36, 49, 64, 81
    position: 0, 1, 2, 3,  4,  5,  6,  7,  8,  9
	
Get the element at a given position:

```
assert A[2] == 4;
```

Loop over all the elements of an array, front to back

```
int total = 0;
for (int y : A) {
  total += y;
}
System.out.println(total);
```

Def. a *half-open interval*, written [i,k), is a contiguous subarray
that starts at position i and finishes one place before k.

The interval [3, 7) of our example array is

    elements: 9, 16, 25, 36
    position: 3,  4,  5,  6

Loop over a half-open interval of the subarray:

```
static int sum(int[] A, int begin, int end) {
  int total = 0;
  for (int i = begin; i != end; ++i) {
      total += A[i];
  }
  return total;
}
```

```
assert total == sum(A, 0, 5) + sum(A, 5, 10);
```

## Rotate the elements of an array by 1 to the right, with wrap around.

        [1,2,3,4,5]
	--> [5,1,2,3,4]
		
Rotation is used in
* sorting algorithms,
* text editors, e.g., when you drag-and-drop a chunk of text 
  to a new location.
* Discussed in "Programming Pearls" pp. 13-15, 209-211 by Jon Bentley
   (won the Dr. Dobb's Excellence in Programming award in 2004,
    Prof. at CMU whose students include Gosling (invented Java), Leiserson, 
    Later went to Bell Labs)
* also known as block-swapping, block exchange, section swap, vector rotation.

    (Other references
    "Benchmarking Block-Swapping Algorithms" by Victor Duvanenko, 
     Dr. Dobb's 2012
    http://www.drdobbs.com/parallel/benchmarking-block-swapping-algorithms/232900395
    "Swapping Sections" by David Gries and Harlan)

* Student exercise (5 minutes)

    Write down an algorithm for rotation.
	Apply the algorithm to the array, showing the array after each
    loop iteration.
	
	    [1,2,3,4,5]

* Go over student solutions to the array rotation. 
  They might look like one of the following. 

    * Save and shift

            [1,2,3,4,5]
            [1,2,3,4,-], 5    (put 5 off to the side)
            [-,1,2,3,4], 5    (move everything else to the right)
            [5,1,2,3,4]       (put 5 at the front)

        When moving everything to the right, should you go forwards
        or backwards?
        Answer: It's easier to go backwards.

            static void rotate_1_save_n_shift(int[] A) {
               if (A.length > 1) {
                  int last = A[A.length - 1];
                  for (int i=A.length - 1; i != 0; --i) {
                     A[i] = A[i-1];
                  }
                  A[0] = last;
               }
            }
        
    * Ripple from start (think of flapping a bedsheet)
      
            [1,2,3,4,5]
            [-,2,3,4,5],1
            [-,1,3,4,5],2
            [-,1,2,4,5],3
            [-,1,2,3,5],4
            [-,1,2,3,4],5
            [5,1,2,3,4]

      The algorithm puts `A[0]` into a temporary variable `tmp1`
      and then progressively swaps the next element with `tmp1`.

            static void rotate_1_ripple(int[] A) {
                if (A.length > 1) {
                    int tmp1 = A[0];
                    for (int i=0; i != A.length - 1; ++i) {
                        int tmp2 = A[i+1];
                        A[i+1] = tmp1;
                        tmp1 = tmp2;
                    }
                    A[0] = tmp1;
                }
            }
        
    * Ripple from start by swapping with A[0]
      
                               [1,2,3,4,5]
            [1,2,3,4,5]        [-,2,3,4,5],1
            [2,1,3,4,5]        [-,1,3,4,5],2
            [3,1,2,4,5]        [-,1,2,4,5],3
            [4,1,2,3,5]        [-,1,2,3,5],4
            [5,1,2,3,4]        [-,1,2,3,4],5
                               [-,1,2,3,4],5

        Instead of using a temporary variable, use `A[0]`.

            static void swap(int[] A, int i, int j) {
                int tmp = A[i];
                A[i] = A[j];
                A[j] = tmp;
            }

            static void rotate_1_ripple_swap(int[] A) {
                if (A.length > 1) {
                    for (int i = 1; i != A.length; ++i) {
                        swap(A, 0, i);
                    }
                }
            }
        
    * Swap backwards

      Swap the last two numbers, move one to the left and swap those two, etc.

            [1,2,3,4,5]
            [1,2,3,5,4]
            [1,2,5,3,4]
            [1,5,2,3,4]
            [5,1,2,3,4]
        
            static void rotate_1_swap_bkwd(int[] A) {
                if (A.length > 1) {
                   for (int i=A.length-1; i != 0; --i) {
                      swap(A, i, i-1);
                   }
                }
            }

## Testing

Let's pick one of the `rotate` implementations to test.  We'll create
a `Rotate.java` file in the `src` directory of our project.

```
public class Rotate {

    public static void rotate(int[] A) {
        rotate_1_swap_bkwd(A);
    }

    ...
}
```

To test the `rotate` method, we'll create a `RotateTest` class as
follows in a file named `RotateTest.java` in the `test` directory.
We'll need to import several items from the JUnit Jupiter library.

```
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.BeforeEach;

import static org.junit.jupiter.api.Assertions.*;

public class RotateTest {
   ...
}
```

## Testing Regular Cases

To get your bearings, its good to start with a test that captures the
common case and that you already know the solution, such as the
example above.

```
    @Test
    public void rotate_common_case() {
        try {
            int[] A = {1, 2, 3, 4, 5};
            int[] A_correct = {5, 1, 2, 3, 4};
            Rotate.rotate(A);
            assertArrayEquals(A, A_correct);
        } catch (Exception e) {
            fail(e.toString());
        }
    }
```

## Testing Corner Cases

In the code for `rotate_1_swap_bkwd`, there's a branch for length
greater than 1. So let's create tests that take different branches.

```
    @Test
    public void rotate_corner_cases() {
        // rotate an empty array
        try {
            int[] A = {};
            int[] A_correct = {};
            Rotate.rotate(A);
            assertArrayEquals(A, A_correct);
        } catch (Exception e) {
            fail(e.toString());
        }
		
        // rotate a 1-element array
        try {
            int[] A = {1};
            int[] A_correct = {1};
            Rotate.rotate(A);
            assertArrayEquals(A, A_correct);
        } catch (Exception e) {
            fail(e.toString());
        }

        // rotate a 2-element array
        try {
            int[] A = {1, 2};
            int[] A_correct = {2, 1};
            Rotate.rotate(A);
            assertArrayEquals(A, A_correct);
        } catch (Exception e) {
            fail(e.toString());
        }		
    }
```

## Testing Big Inputs using Random Input Generation

```
    @Test
    public void rotate_big() {
        // rotate a big array
        try {
            Random r = new Random(0);
            for (int t = 0; t != 50; ++t) {
                int n = r.nextInt(1000);
                int[] A = new int[n];
                for (int i = 0; i != n; ++i) {
                    A[i] = r.nextInt(100);
                }
                int[] A_correct = Arrays.copyOf(A, A.length);
                Rotate.rotate(A);
                simple_rotate(A_correct);
                assertArrayEquals(A, A_correct);
            }
        } catch (Exception e) {
            fail(e.toString());
        }
    }
```




## Correctness

We'll focus on the swap-backwards algorithm, expressed below as a
`while` loop.

```
static void rotate_1_swap_bkwd(int[] A) {
	if (A.length > 1) {
		int i = A.length - 1;
		while (i != 0) {
			swap(A, i, i-1);
			--i;
		}
	} else {
		// Nothing to do here
	}
}
```

We're going to prove that this algorithm is correct and document the
proof by adding comments and `assert` statements to the code.

What does it mean exactly for an array to be rotated by 1 to the right
The following `is_rotated` function makes this idea precise. If the
array has zero or one element, then rotating it does not change the
array. Otherwise, the last element becomes the first element and all
the other elements are moved by one to the right.

```
static boolean is_rotated(int[] A_orig, int[] A_new) {
	if (A_orig.length < 2) {
        return Arrays.equals(A_orig, A_new);
	} else {
		boolean result = A_new[0] == A_orig[A_orig.length - 1];
		for (int i = 0; i != A_orig.length - 1; ++i) {
			result &= A_orig[i] == A_new[i + 1];
		}
		return result;
	}
}
```

Most of the work of the algorithm is in a `while` loop, which means we
need to come up with a *loop invariant* to prove that it is correct.
A loop invariant is a statement about the current state of affairs
that it true at the beginning of each loop iteration.

The tricky thing about a loop invariant is that the state of affairs
is changing, so it must be abstract enough to capture some property
that stays the same.

Let's watch the algorithm in action to see if we can see some patterns
from which we can create the loop invariant. The `while` loop is
controled by variable `i`, so let's draw a vertical line just before
the element at the index i. The loop invariant will need to relate the
current state of the array to it's original state, so we'll write down
both at each iteration of the loop:

    A_orig: 1 2 3 4 | 5
    A:      1 2 3 4 | 5
	
    A_orig: 1 2 3 | 4 5
    A:      1 2 3 | 5 4
	
    A_orig: 1 2 | 3 4 5
    A:      1 2 | 5 3 4
	
    A_orig: 1 | 2 3 4 5
    A:      1 | 5 2 3 4
	
    A_orig: | 1 2 3 4 5
    A_:     | 5 1 2 3 4

Looking at the above, let's focus on the front part of the array, the
elements before the line. There's an easy pattern: the elements in the
front part of A are the same as those in the front part of A_orig.

What about the back part, i.e. the elements after the line? The back
part of A is the rotated version of the back part of A_orig!

We write down the loop invariant as another function:

```
static boolean loop_invariant_rotate_bkwd(int[] A_orig, int[] A, int i) {
    return is_rotated(copyOfRange(A_orig, i, A.length), 
                      copyOfRange(A, i, A.length))
        && Arrays.equals(copyOfRange(A_orig, 0, i), 
                         copyOfRange(A, 0, i));
}
```

Now that we've identified a loop invariant, we need to check that it
really is an appropriate loop invariant for this algorithm.

1. show that the loop invariant is true before the start of the loop
2. show that the loop invariant is true at the end of the loop body,
   assuming that it started out true at the beginning of the loop.
   (For a hypothetical iteration of the loop.)
3. show that the loop invariant combined with the loop condition
   being false logically implies the correctness criteria for the
   algorithm.

### Student Exercise

write down arguments for 1, 2, and 3.


### Rotate with annotations 

```
static void rotate_1_swap_bkwd_short_proof(int[] A) {
	int[] A_orig = copyOf(A, A.length);
	assert Arrays.equals(A_orig, A);
	if (A.length > 1) {
		int i = A.length - 1;
		// The subarrays [A.length-1, A.length) of A_orig and A have length 1,
		//     and are equal, so they satisfy first branch of is_rotated.
		// The subarrays [0, A.length-1) of A_orig and A are equal.
		assert loop_invariant_rotate_bkwd(A_orig, A, i);
		while (i != 0) {
			assert loop_invariant_rotate_bkwd(A_orig, A, i);
			// A_orig: ... V W | X Y Z
			// A:      ... V W | Z X Y
			//                   i
			swap(A, i, i-1);
			// A_orig: ... V W | X Y Z
			// A:      ... V Z | W X Y
			assert loop_invariant_rotate_bkwd(A_orig, A, i - 1);
			--i;
			// A_orig: ... V | W X Y Z
			// A:      ... V | Z W X Y
		}
		assert loop_invariant_rotate_bkwd(A_orig, A, i) && i == 0;
		// The subarrays [0, A.length) of A and A_orig are rotated, and
		// they are equal to the entire arrays.
		assert is_rotated(A_orig, A);
	} else {
		// Nothing to do here
		assert is_rotated(A_orig, A);
	}
	assert is_rotated(A_orig, A);
}
```
