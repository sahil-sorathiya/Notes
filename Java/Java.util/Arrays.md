`java.util.Arrays` is a **utility class for working with arrays** in Java.

You don't create an `Arrays` object. Almost everything you use is **static**:

```java
import java.util.Arrays;

Arrays.sort(arr);
Arrays.fill(arr, 0);
Arrays.toString(arr);
```

Think of it as the **`Collections` utility class for arrays**.

---

# 1. `Arrays.toString()`

Used to convert a **1D array** into a readable string.

```java
int[] arr = {10, 20, 30};

System.out.println(Arrays.toString(arr));
```

Output:

```text
[10, 20, 30]
```

Without it:

```java
System.out.println(arr);
```

you'll get something like:

```text
[I@5acf9800
```

because you're printing the array object's identity representation.

Works for primitive arrays and object arrays:

```java
String[] arr = {"Java", "C++", "Python"};

System.out.println(Arrays.toString(arr));
```

```text
[Java, C++, Python]
```

---

# 2. `Arrays.deepToString()`

For **multidimensional arrays**.

```java
int[][] arr = {
    {1, 2},
    {3, 4}
};

System.out.println(Arrays.deepToString(arr));
```

Output:

```text
[[1, 2], [3, 4]]
```

### Why not `toString()`?

```java
Arrays.toString(arr);
```

doesn't recursively print the inner arrays properly.

Use:

```java
Arrays.deepToString(arr);
```

for arrays containing other arrays.

---

# 3. `Arrays.sort()`

Probably the most frequently used method.

```java
int[] arr = {5, 2, 8, 1, 3};

Arrays.sort(arr);

System.out.println(Arrays.toString(arr));
```

Output:

```text
[1, 2, 3, 5, 8]
```

It **modifies the original array**.

It does not create a sorted copy.

---

## Sort a range

You can specify a range:

```java
Arrays.sort(arr, 1, 4);
```

This sorts:

```text
index 1
index 2
index 3
```

but **not index 4**.

So the range is:

```text
[fromInclusive, toExclusive)
```

This is consistent with Java's other range APIs.

---

# 4. Sorting object arrays

You can sort:

```java
Integer[] arr = {5, 2, 8, 1};

Arrays.sort(arr);
```

This uses the elements' **natural ordering**.

For `String`:

```java
String[] arr = {"dog", "cat", "apple"};

Arrays.sort(arr);
```

Result:

```text
[apple, cat, dog]
```

---

# 5. Sorting with a Comparator

For object arrays:

```java
Integer[] arr = {5, 2, 8, 1};

Arrays.sort(arr, (a, b) -> b - a);
```

Result:

```text
[8, 5, 2, 1]
```

Better comparator:

```java
Arrays.sort(arr, Comparator.reverseOrder());
```

For records/classes, you can do:

```java
Arrays.sort(arr, Comparator.comparingInt(Person::age));
```

---

# 6. `Arrays.parallelSort()`

Java also provides:

```java
Arrays.parallelSort(arr);
```

It can use multiple threads internally.

For example:

```java
int[] arr = {5, 2, 8, 1, 3};

Arrays.parallelSort(arr);
```

Result:

```text
[1, 2, 3, 5, 8]
```

For small arrays, ordinary:

```java
Arrays.sort(arr);
```

is generally perfectly fine.

---

# 7. `Arrays.binarySearch()`

Searches for an element using **binary search**.

```java
int[] arr = {1, 3, 5, 7, 9};

int index = Arrays.binarySearch(arr, 7);

System.out.println(index);
```

Output:

```text
3
```

### VERY IMPORTANT

The array must be **sorted** before using binary search.

```java
Arrays.sort(arr);
int index = Arrays.binarySearch(arr, 7);
```

Otherwise, the result is not meaningful.

---

## What if the element doesn't exist?

```java
int[] arr = {1, 3, 5, 7, 9};

System.out.println(Arrays.binarySearch(arr, 6));
```

You'll get a **negative number**.

More specifically, Java returns:

```text
-(insertionPoint) - 1
```

For `6`:

```text
insertion point = 3
```

because:

```text
1 3 5 [6] 7 9
      ↑
    index 3
```

So:

```text
-(3) - 1 = -4
```

Result:

```text
-4
```

---

# 8. `Arrays.fill()`

Fills an entire array with a value.

```java
int[] arr = new int[5];

Arrays.fill(arr, 10);
```

Now:

```text
[10, 10, 10, 10, 10]
```

Very useful in competitive programming.

For example:

```java
int[] dist = new int[n];

Arrays.fill(dist, Integer.MAX_VALUE);
```

---

## Fill a range

```java
Arrays.fill(arr, 1, 4, 99);
```

Only indexes:

```text
1, 2, 3
```

are filled.

Again:

```text
[fromInclusive, toExclusive)
```

---

# 9. `Arrays.copyOf()`

Creates a **new array**.

```java
int[] arr = {1, 2, 3};

int[] copy = Arrays.copyOf(arr, arr.length);
```

Now:

```text
arr  → [1, 2, 3]
copy → [1, 2, 3]
```

They are different arrays.

```java
copy[0] = 100;

System.out.println(arr[0]);  // 1
System.out.println(copy[0]); // 100
```

---

## Interesting: change the size

```java
int[] copy = Arrays.copyOf(arr, 5);
```

If:

```text
arr = [1, 2, 3]
```

then:

```text
copy = [1, 2, 3, 0, 0]
```

If you make it smaller:

```java
Arrays.copyOf(arr, 2);
```

you get:

```text
[1, 2]
```

## Copy of Multidimentional Arrays

**`Arrays.copyOf()` works on multidimensional arrays**, but there's an important catch:

> It performs a **shallow copy**, not a deep copy.

Remember that a Java 2D array is actually an **array of arrays**.

### What actually got copied?

Only the **outer array**:

```java
int[][] b = Arrays.copyOf(a, a.length);
```

is roughly equivalent to:

```java
int[][] b = new int[a.length][];

for (int i = 0; i < a.length; i++) {
    b[i] = a[i];  // copies references
}
```

---

### If you want a deep copy

You need to copy each inner array:

```java
int[][] b = new int[a.length][];

for (int i = 0; i < a.length; i++) {
    b[i] = Arrays.copyOf(a[i], a[i].length);
}
```

### The key distinction

```text
Arrays.copyOf(a, ...)
        │
        ▼
   shallow copy
        │
        ├── primitive 1D array → actual values copied
        │
        └── 2D/object array → references copied
                              ↓
                         inner objects shared
```

This same shallow-copy concept is important for `Object[]` as well, not just multidimensional arrays.


---

# 10. `Arrays.copyOfRange()`

Copies a particular range.

```java
int[] arr = {10, 20, 30, 40, 50};

int[] copy = Arrays.copyOfRange(arr, 1, 4);
```

Result:

```text
[20, 30, 40]
```

Again:

```text
[fromInclusive, toExclusive)
```

---

# 11. `Arrays.equals()`

Compares two arrays **element by element**.

```java
int[] a = {1, 2, 3};
int[] b = {1, 2, 3};

System.out.println(Arrays.equals(a, b));
```

Output:

```text
true
```

This is different from:

```java
a == b
```

because `==` checks whether they are the **same array object**.

```java
System.out.println(a == b); // false
```

So:

```text
==                  → same object?
Arrays.equals()     → same contents?
```

---

# 12. `Arrays.deepEquals()`

For multidimensional arrays:

```java
int[][] a = {
    {1, 2},
    {3, 4}
};

int[][] b = {
    {1, 2},
    {3, 4}
};

System.out.println(Arrays.deepEquals(a, b));
```

Output:

```text
true
```

Use:

```java
Arrays.deepEquals()
```

when the arrays may contain nested arrays.

---

# 13. `Arrays.hashCode()`

Generates a hash code based on the **contents** of a 1D array.

```java
int[] arr = {1, 2, 3};

System.out.println(Arrays.hashCode(arr));
```

The important thing is:

```java
Arrays.hashCode(a) == Arrays.hashCode(b)
```

if the arrays have equal contents, assuming the same element values.

This is useful when implementing `hashCode()` for a class containing arrays.

---

# 14. `Arrays.deepHashCode()`

For nested arrays:

```java
int[][] arr = {
    {1, 2},
    {3, 4}
};

int hash = Arrays.deepHashCode(arr);
```

It recursively calculates the hash based on nested contents.

---

# 15. `Arrays.mismatch()`

This is a very useful newer method.

It tells you the **first index where two arrays differ**.

```java
int[] a = {10, 20, 30, 40};
int[] b = {10, 20, 99, 40};

System.out.println(Arrays.mismatch(a, b));
```

Output:

```text
2
```

Because:

```text
index:  0   1   2   3
a:     10  20  30  40
b:     10  20  99  40
                ↑
```

If the arrays are identical:

```java
Arrays.mismatch(a, b)
```

returns:

```text
-1
```

---

# 16. `Arrays.compare()`

Lexicographically compares two arrays.

For example:

```java
int[] a = {1, 2, 3};
int[] b = {1, 2, 4};

System.out.println(Arrays.compare(a, b));
```

Result is negative because:

```text
3 < 4
```

Conceptually:

```text
a < b  → negative
a == b → 0
a > b  → positive
```

This is similar to `String.compareTo()`.

For example:

```text
[1, 2, 3]
[1, 2, 4]
       ↑
```

The first differing element determines the result.

---

# 17. `Arrays.compareUnsigned()`

For integer primitive arrays, Java also has:

```java
Arrays.compareUnsigned(a, b);
```

This compares numeric values as **unsigned** rather than signed.

For example, this matters for values such as:

```java
-1
```

because its bit representation can be interpreted differently when treated as unsigned.

You'll rarely need this in normal application code, but it can matter in low-level/network/binary-data code.

---

# 18. `Arrays.setAll()`

This lets you generate array values using a function.

```java
int[] arr = new int[5];

Arrays.setAll(arr, i -> i * 2);
```

Result:

```text
[0, 2, 4, 6, 8]
```

The lambda receives the **index**.

Another example:

```java
Arrays.setAll(arr, i -> i + 10);
```

Result:

```text
[10, 11, 12, 13, 14]
```

---

# 19. `Arrays.parallelSetAll()`

Same basic idea, but the computation can be performed in parallel:

```java
Arrays.parallelSetAll(arr, i -> i * i);
```

Result:

```text
[0, 1, 4, 9, 16]
```

Useful when generating large arrays with expensive independent computations.

---

# 20. `Arrays.asList()`

This one causes **a lot of confusion**.

```java
String[] arr = {"A", "B", "C"};

List<String> list = Arrays.asList(arr);
```

Now you have a `List` backed by the array.

```java
list.set(0, "X");

System.out.println(arr[0]);
```

Output:

```text
X
```

Because the list and array are connected.

### Important

You cannot change the list's size:

```java
list.add("D");    // ❌ UnsupportedOperationException
list.remove(0);   // ❌ UnsupportedOperationException
```

But you can modify existing elements:

```java
list.set(0, "X"); // ✅
```

---

# 21. The primitive-array trap with `asList()`

This is particularly important:

```java
int[] arr = {1, 2, 3};

List<int[]> list = Arrays.asList(arr);
```

**NOT:**

```java
List<Integer>
```

Why?

Because `int[]` is one object, and Java's generic `asList()` receives it as a single argument.

So:

```java
Arrays.asList(new int[]{1, 2, 3})
```

creates a list containing **one `int[]`**.

For primitive arrays, use streams or manually box the values if you need `List<Integer>`.

---

# 22. `Arrays.stream()`

You can create a Java Stream from an array.

```java
int[] arr = {1, 2, 3, 4, 5};

Arrays.stream(arr)
      .forEach(System.out::println);
```

You can also do:

```java
int sum = Arrays.stream(arr).sum();
```

or:

```java
int max = Arrays.stream(arr).max().getAsInt();
```

or:

```java
int[] result = Arrays.stream(arr)
                     .filter(x -> x % 2 == 0)
                     .toArray();
```

For object arrays:

```java
String[] arr = {"Java", "C++", "Python"};

Arrays.stream(arr)
      .filter(s -> s.length() > 3)
      .forEach(System.out::println);
```

---

# 23. `Arrays.parallelPrefix()`

This is a more advanced one.

It performs a cumulative operation.

```java
int[] arr = {1, 2, 3, 4};

Arrays.parallelPrefix(arr, (a, b) -> a + b);
```

Result:

```text
[1, 3, 6, 10]
```

Because:

```text
1
1 + 2
1 + 2 + 3
1 + 2 + 3 + 4
```

You can use other associative operations too.

---

# 24. `Arrays.parallelPrefix()` with multiplication

```java
int[] arr = {1, 2, 3, 4};

Arrays.parallelPrefix(arr, (a, b) -> a * b);
```

Result:

```text
[1, 2, 6, 24]
```

---

# 25. `Arrays.parallelPrefix()` range version

You can also specify:

```java
Arrays.parallelPrefix(arr, from, to, operation);
```

Again:

```text
[fromInclusive, toExclusive)
```

---

# The methods you should know first

You **don't need to memorize the entire class**.

For coding/DSA, I'd prioritize:

| Method                  | Purpose                  |
| ----------------------- | ------------------------ |
| `Arrays.sort()`         | Sort array               |
| `Arrays.binarySearch()` | Binary search            |
| `Arrays.toString()`     | Print 1D array           |
| `Arrays.deepToString()` | Print nested arrays      |
| `Arrays.fill()`         | Fill values              |
| `Arrays.copyOf()`       | Copy/resize              |
| `Arrays.copyOfRange()`  | Copy a range             |
| `Arrays.equals()`       | Compare 1D arrays        |
| `Arrays.deepEquals()`   | Compare nested arrays    |
| `Arrays.asList()`       | Array → List             |
| `Arrays.stream()`       | Array → Stream           |
| `Arrays.mismatch()`     | First differing index    |
| `Arrays.compare()`      | Lexicographic comparison |

And for more advanced Java:

```text
parallelSort()
setAll()
parallelSetAll()
parallelPrefix()
hashCode()
deepHashCode()
compareUnsigned()
```

### One important mental model

`Arrays` **doesn't represent an array**.

```java
int[] arr = new int[5];
```

`arr` is the actual array object.

```java
Arrays.sort(arr);
```

`Arrays` is simply a **utility class containing static methods that operate on that array**.

This is similar to:

```java
Collections.sort(list);
```

where `Collections` is a utility class and `list` is the actual collection.
