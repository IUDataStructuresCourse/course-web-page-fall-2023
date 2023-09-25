# Hash Tables

Java's `HashMap` and `HashSet` classes are implemented with hash tables

Most modern languages have hash tables built-in or in the standard library. 

The Map Abstract Data Type (aka. "dictionary")

	interface Map<K,V> {
	   V get(K key);
	   V put(K key, V value);
	   V remove(K key);
	   boolean containsKey(K key);
	}

Compared to Binary Search Trees, the Map ADT does not provide an
ordering of the elements.

Motivation: maps are everywhere!

* compilers
* Python interpreter: variables stores in dictionaries
* spell checking
* search engines use to use dictionaries that linked a word to
  web pages that the word appears in
* computer login
* network router to lookup local machines
* substring search, string commonoalities (DNA)

We could implement the Map ADT with AVL Trees, then `get()` is O(log(n)).

## A simple `Map` implementation

If keys are integers, we can use an array.
        
Store items in array indexed by key (draw picture) use None to
indicate absense of key.

What's good?

`get()` is O(1)

What's bad?

1. keys may not be natural numbers
2. memory hog if the set of possible keys is huge, if much
   larger than than the number of keys stored in the dictionary.
		   

## Prehashing

Prehashing fixes problem 1 by mapping everything to integers.
(Textbook calls this the creation of a hash code.)

In Java, `o.hashCode()` computes the prehash of object `o`.

Ideally: `x.hashCode() == y.hashCode()` iff `x` and `y` are the same
object (but sometimes different objects have the same hash code)

User-definable: a class can override the `hashCode` method, and should
do so if you are overriding the `equals` method.

Algorithm for prehashing a string (aka. polynomial hash code)
Map each character to one digit in a number.
But there are 256 different characters, not 10.
So we use a different base.

	prehash_string('ab') == 97 * 256 + 98
	prehash_string('abc') == 97 * (256^2) + 98 * (256^1) + 99

## Hashing

Hashing fixes problem 2 (reduce memory consumption).
    
The word "hash" is from cooking: "a finely chopped mixture".

* draw picture of universe of keys getting mapped down by hash 
  function h to 0... m  (for a table of size m)
* a subset of the universe is present in the table, subset is size n
* we want m in O(n).
* problems with this idea? answer: collision: h(key1) = h(key2)

## Chaining fixes collisions.

* each slot of the hashtable contains a linked list of the items that
  collided (had the same hash value)
* draw picture
* worst case: search in O(n)

Towards proving that the average case time is O(1).

* Simple Uniform Hashing assumption (mostly true but not completely true)

	each key is equally likely to be hashed to each slot of the table
	independent of where other keys land. (uniformity and idependence)

* what's the expected length of a chain? (load factor)

	n keys in m slots: n/m = λ

* Search:
	1. hash the key: O(1)
	2. find the chain: O(1)
	3. linear search in the chain: O(λ)

    total for search: O(1 + λ)

Takeaway: need to grow table size m as n increases so that λ stays small.

## hash functions
    
### division method: h(k) = k mod m
            
need to be careful about choice of table size m
                
if not, may not use all of the table
        
	table size 4 (slots 0..3)
	suppose the keys are all even: 0,2,..

			0 -> 0           (0 mod 4 = 0)
			2 -> 2           (2 mod 4 = 2)
			4 -> 0           (4 mod 4 = 0)
			6 -> 2           (6 mod 4 = 2)
			8 -> 0           (8 mod 4 = 0)
			...

Never use slot 1 and 3.

    Good to choose a prime number for m, not close to a power of 2 or 10.

### Multiply-Add-and-Divide (MAD) method

    
    h(k) = ((a * k + b) mod p) mod m
	
where
* p is a prime number larger than m
* a,b are randomly chosen integers between 1 and p-1.

### Student Exercise 1

Using the division method and chaining, insert the
keys 4, 1, 3, 2, 0 into a hash table with table size 3 (m=3).

[solution](./Sep-25-solutions.md#student-exercise-1)


## Rehashing

When the load factor gets too high, we need to grow the table.

1. Allocate a new table that is double the size.

2. Insert all of the entries from the old table into the new table,
   using the new table size (the `m`) in the hash function.

Rehashing is an O(n) operation, but by doubling the same of the table,
it doesn't need to happen very often.
