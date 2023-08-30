# Review of Big-O

**Definition** (Big-O) For a given function g, we define **O(g)** as the
the set of functions that grow similarly or slower than g. More
precisely, f ∈ O(g) iff ∃ k c. ∀ n ≥ k. f(n) ≤ c g(n).

**Notation** We write f ≲ g iff f ∈ O(g), and say that f is
asymptotically less-or-equal to g.

# Review of ∃

∃ means "there exists"

Proof rules:

(⇒∃) To prove ∃ x. P(x), choose x=k and show P(k).

(∃⇒) If you have an assumption ∃ x. P(x), then you have P(y)
   for any fresh variable y.
   
Let's see these rules used in an example.

## Example of ∃

We define "x is even" as ∃ k. 2 × k = x.

Theorem. 6 is even
Proof. Choose k=3 and note that 2 × 3 = 6. QED.

Theorem. If x and y are even, then x + y is even.
Proof.
Assume x is even, so (∃ k. 2 k = x) so 2 k₁ = x (∃⇒).
Assume y is even, so (∃ k. 2 k = y) so 2 k₂ = y (∃⇒).

x + y = 2k₁ + 2k₂ = 2(k₁ + k₂)          (1)

We need to show that x + y is even.
It suffices to show that (∃ k. 2 k = x + y).
Choose k = k₁ + k₂ and note that 2(k₁ + k₂) = x + y by equation (1).
QED


## Review of ∀

∀ means "for all"

Proof rules:

To prove ∀ x. P(x), 
   (⇒∀) prove that P is true for some unknown entity y.
   (Induction) If x is a natural number, use induction:
      * prove P(0)
	  * prove that P(k) implies P(1+k) for an unknown number k.

(∀⇒) If you have an assumption ∀ x. P(x), then you known P(y)
   for any choice of y.

## Example of ∀

Theorem. ∀ n. n is even implies n + 2 is even.
Proof.
 Let p be a number. (⇒∀)
 Assume that p is even, that is, (∃k. 2 × k = p).
 So 2 × k₁ = p by (∃⇒)      (1)
 
 We need to show that p + 2 is even, that is, (∃ k. 2 × k = p + 2).
 
 Aside: We need prove an equation similar to 2 × k = p + 2 except with
 something else in the k position.
 Equation (1) has p on the right-hand side, so we could add 2 to both sides:
 2 × k₁ + 2 = p + 2
 then factor out the 2
 2 × (k₁ + 1) = p + 2             (2)
 and now we're in good shape because this equation is the same as 2 × k = p + 2
 except that we have (k₁ + 1) in place of k.
 Now back to the proof.

 Choose k = k₁ + 1 and note that 2 × (k₁ + 1) = p + 2 by equation (2).
QED.

Theorem. 2 is even.
Proof.
0 is even because 2 × 0 = 0.
2 is even by applying the above theorem and rule (∀⇒).
QED.

# Back to Big-O

## Example

Theorem. If f₁ ≲ g and f₂ ≲ g, then f₁ + f₂ ≲ g.
Proof.
 Suppose f₁ ≲ g and f₂ ≲ g.
 So   ∀n ≥ k₁. f₁(n) ≤ c₁ × g(n)    (1)
 and  ∀n ≥ k₂. f₂(n) ≤ c₂ × g(n)    (2)
 by (∃⇒).

 We need to show that ∃k c. ∀n ≥ k. f₁(n) + f₂(n) ≤ c × g(n)
 Choose k = k₁ + k₂ (⇒∃).
 Choose c = c₁ + c₂ (⇒∃).
 ∀n ≥ k₁ + k₂. f₁(n) + f₂(n) ≤ (c₁ + c₂) × g(n)
 Let n be a number and assume n ≥ k₁ + k₂. (⇒∀)
 We need to show that f₁(n) + f₂(n) ≤ (c₁ + c₂) × g(n)
 equivalently: f₁(n) + f₂(n) ≤ c₁ × g(n) + c₂ × g(n)
 From (1) and (2) we have
   f₁(n) ≤ c₁ × g(n)
   f₂(n) ≤ c₂ × g(n)
 and therefore
   f₁(n) + f₂(n) ≤ c₁ × g(n) + c₂ × g(n).
QED.

## Example

Show that 2 log n ∈ O(n / 2).

We need to choose k and c.
To make it easy to compute log n, let's look at powers of 2.

|  n  | log n | n/2 | 2 log n |
| --- | ----- | --- | ------- |
|  1  |   0   | 1/2 |    0    |
|  2  |   1   |  1  |    2    |
|  4  |   2   |  2  |    4    |
|  8  |   3   |  4  |    6    |
| 16  |   4   |  8  |    8    |
| 32  |   5   | 16  |   10    |
| 64  |   6   | 32  |   12    |

Choose k = 16.
Choose c = 1.

We need to show that ∀ n ≥ 16. 2 log n ≤ n / 2.
Proof by induction.
Base case n = 16. 2 log 16 = 8 = n / 2.
Induction step. Assume k > 16.
  Assume 2 log(k) ≤ k/2 (Induction Hypothesis).
  We need to show that 2 log (k + 1) ≤ (k + 1) / 2.
  Work backwards through the following:
  2 log(k + 1) ≤ 2 log(1.18 × k)
			   = 2 (log(1.18) + log(k))      (log(ab) = log(a) + log(n))
			   ≤ 2 (1/4  + log(k))
			   = 2(1/4) + 2 log(k)
			   ≤ 1/2 + 2 log(k) 
			   ≤ 1/2 + k/2   by IH
			   = (1 + k) / 2.
QED.

# Practice analyzing the time complexity of an algorithm: Insertion Sort

	public static void insertion_sort(int[] A) { // let n = A.length 
		for (int j = 1; j != A.length; ++j) { // iterations? n
			int key = A[j];                 // O(1)
			int i = j - 1;                  // O(1)
			while (i >= 0 && A[i] > key) {  // iterations? n
				A[i+1] = A[i];              // O(1)
				i -= 1;                     // O(1)
  				                            // while body total: O(1)
			}                               // while: O(n)
			A[i+1] = key;                   // O(1)
			                                // for body total: O(n)
		}                                   // for: n * O(n) = O(n^2)
	}

What is the time complexity of insertion_sort?

   worst-case-insertion-sort-time(n) ∈ O(n^2)

Answer:
* inner loop is O(n)
* outer loop is O(n)
* so the entire algorithm is O(n²)

# Time Complexity of Java collection operations

* LinkedList
	* add: O(1)
	* get: O(n)
	* contains: O(n)
	* remove: O(1)

* ArrayList
	* add: O(1)
	* get: O(1)
	* contains: O(n)
	* remove: O(n)

# Common complexity classes:

* O(1)                        constant
* O(log(n))                   logarithmic
* O(n)                        linear
* O(n log(n))
* O(n²), O(n^3), etc.         polynomial
* O(2^n), O(3^n), etc.        exponential
* O(n!)                       factorial


# Lower bounds

**Definition** (Omega) For a given function g(n), we define **Ω(g)** as
the set of functions that grow at least as fast a g(n):

f ∈ Ω(g) iff ∃ k c, ∀ n ≥ k, 0 ≤ c g(n) ≤ f(n).

**Notation** f ≳ g means f ∈ Ω(g).


# Tight bounds

**Definition** (Theta) For a given function g(n), **Θ(g)** is the set
of functions that grow at the same rate as g(n):

f ∈ Θ(g) iff ∃ k c₁ c₂, ∀ n ≥ k, 0 ≤ c₁ g(n) ≤ f(n) ≤ c₂ g(n).

We say that g is an *asymptotically tight bound* for each function
in Θ(g).

**Notation** f ≈ g means f ∈ Θ(g)


# Reasoning about bounds.

* Symmetry: f ≈ g iff g ≈ f

* Transpose symmetry: f ≲ g iff g ≳ f

# Relationships between Θ, O, and Ω.

* Θ(g) ⊆ O(g)

* Θ(g) ⊆ Ω(g)

* Θ(g) = Ω(g) ∩ O(g)

# Example: Merge Sort

Divide and conquer!

Split the array in half and sort the two halves.

Merge the two halves.

	private static int[] merge(int[] left, int[] right) {
	   int[] A = new int[left.length + right.length];
	   int i = 0;
	   int j = 0;
	   for (int k = 0; k != A.length; ++k) {
		   if (i < left.length
			   && (j ≥ right.length || left[i] <= right[j])) {
			  A[k] = left[i];
			  ++i;
		   } else if (j < right.length) {
			  A[k] = right[j];
			  ++j;
		   }
	   }
	   return A;
	}

	public static int[] merge_sort(int[] A) {
	   if (A.length > 1) {
		   int middle = A.length / 2;
		   int[] L = merge_sort(Arrays.copyOfRange(A, 0, middle));
		   int[] R = merge_sort(Arrays.copyOfRange(A, middle, A.length));
		   return merge(L, R);
	   } else {
		   return A;
	   }
	}    

What's the time complexity?

Recursion tree:

			   c*n                    = c*n
			 /     \
			/       \
	   c*n/2        c*n/2             = c*n
	   /  \         /   \
	  /    \       /     \     
	c*n/4  c*n/4  c*n/4  c*n/4        = c*n
	...

Height of the recursion tree is log(n).

So the total work is c\, n\, log(n).

Time complexity is O(n \, log(n)).

