## Arrays in Java

An **array** is a fixed-size collection of elements of the **same type**, stored under one variable name.

### 1. Declaration

```java
int[] arr;
```

or

```java
int arr[];
```

Both are valid, but `int[] arr` is generally preferred.

### 2. Creation

```java
int[] arr = new int[5];
```

This creates an array of size `5`:

```text
index:  0   1   2   3   4
value:  0   0   0   0   0
```

For `int`, the default value is `0`.

Other defaults:

| Type             | Default    |
| ---------------- | ---------- |
| `int`            | `0`        |
| `long`           | `0L`       |
| `double`         | `0.0`      |
| `boolean`        | `false`    |
| `char`           | `'\u0000'` |
| Object/reference | `null`     |

### 3. Initialization

You can directly initialize an array:

```java
int[] arr = {10, 20, 30, 40};
```

Equivalent to:

```java
int[] arr = new int[]{10, 20, 30, 40};
```

But this is **not valid**:

```java
int[] arr;
arr = {10, 20, 30}; // ❌
```

You need:

```java
arr = new int[]{10, 20, 30}; // ✅
```


## Multidimensional arrays

Java technically has **arrays of arrays**.

```java
int[][] matrix = new int[3][4];
```

Think of it as:

```text
matrix
  |
  +--> [0][0] [0][1] [0][2] [0][3]
  |
  +--> [1][0] [1][1] [1][2] [1][3]
  |
  +--> [2][0] [2][1] [2][2] [2][3]
```

Access:

```java
matrix[1][2] = 10;
```

## Jagged arrays:

```java
int[][] arr = new int[3][];

arr[0] = new int[2];
arr[1] = new int[5];
arr[2] = new int[3];
```

You can also initialize it directly:
```java
int[][] arr = {
    {1, 2},
    {3, 4, 5, 6},
    {7, 8, 9}
};
```

So unlike some languages, Java's multidimensional arrays don't have to be rectangular.

### Useful `Arrays` class methods (Find more on java.lang.Arrays notes)

Java provides `java.util.Arrays`.

```java
import java.util.Arrays;
```

Some commonly used methods:

```java
Arrays.sort(arr);
```

```java
Arrays.binarySearch(arr, 20);
```

```java
Arrays.fill(arr, 5);
```

```java
Arrays.copyOf(arr, 10);
```

```java
Arrays.equals(a, b);
```

```java
Arrays.toString(arr);
```

For example:

```java
int[] arr = {3, 1, 2};

Arrays.sort(arr);

System.out.println(Arrays.toString(arr));
```

Output:

```text
[1, 2, 3]
```

### One thing to remember

```text
Array
 ├── fixed size
 ├── same element type
 ├── zero-indexed
 ├── .length
 ├── can contain primitives
 └── is itself an object
```



