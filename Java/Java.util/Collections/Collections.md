## What is `Collections` in Java?

`Collections` is a **utility class** in the `java.util` package that provides static methods for working with collections such as:

* `List`
* `Set`
* `Queue`
* `Map` (some methods)

```java
import java.util.Collections;
```

It is similar to:

* `Arrays` → utility methods for arrays
* `Collections` → utility methods for collection objects

The class cannot be instantiated.

```java
public class Collections {
    private Collections() {}
}
```

---

## Common Operations

### 1. Sorting

```java
List<Integer> list = new ArrayList<>(
    List.of(5, 3, 1, 4, 2)
);

Collections.sort(list);

System.out.println(list);
```

Output:

```text
[1, 2, 3, 4, 5]
```

Using a comparator:

```java
Collections.sort(
    list,
    Comparator.reverseOrder()
);
```

---

### 2. Binary Search

Works on a sorted list.

```java
List<Integer> list = List.of(10, 20, 30, 40);

int idx = Collections.binarySearch(list, 30);

System.out.println(idx);
```

Output:

```text
2
```

---

### 3. Reverse

```java
List<Integer> list =
    new ArrayList<>(List.of(1, 2, 3));

Collections.reverse(list);
```

Result:

```text
[3, 2, 1]
```

---

### 4. Shuffle

Randomly rearranges elements.

```java
Collections.shuffle(list);
```

Example:

```text
[2, 1, 3, 5, 4]
```

---

### 5. Swap

```java
Collections.swap(list, 0, 2);
```

Before:

```text
[1, 2, 3]
```

After:

```text
[3, 2, 1]
```

---

### 6. Fill

Replace all elements.

```java
List<Integer> list =
    new ArrayList<>(List.of(1, 2, 3));

Collections.fill(list, 10);
```

Result:

```text
[10, 10, 10]
```

---

### 7. Copy

```java
List<Integer> src =
    List.of(1, 2, 3);

List<Integer> dest =
    new ArrayList<>(List.of(0, 0, 0));

Collections.copy(dest, src);
```

Result:

```text
[1, 2, 3]
```

**Important:** destination must already have sufficient size.

---

### 8. Min and Max

```java
Collections.min(list);
Collections.max(list);
```

Example:

```java
System.out.println(Collections.min(list));
System.out.println(Collections.max(list));
```

---

### 9. Frequency

Count occurrences.

```java
List<Integer> list =
    List.of(1, 2, 2, 2, 3);

int count =
    Collections.frequency(list, 2);
```

Output:

```text
3
```

---

### 10. Disjoint

Checks whether two collections share elements.

```java
Collections.disjoint(
    List.of(1, 2),
    List.of(3, 4)
);
```

Output:

```text
true
```

---

## Immutable Collection Wrappers

### Empty Collections

```java
Collections.emptyList();
Collections.emptySet();
Collections.emptyMap();
```

Example:

```java
List<String> list =
    Collections.emptyList();
```

---

### Singleton Collections

Contain exactly one element.

```java
Collections.singleton("A");
Collections.singletonList("A");
Collections.singletonMap("k", "v");
```

---

### nCopies

```java
List<String> list =
    Collections.nCopies(5, "Hello");
```

Result:

```text
[Hello, Hello, Hello, Hello, Hello]
```

The returned list is immutable.

---

## Synchronized Wrappers

Before concurrent collections existed, Java provided synchronized wrappers.

```java
List<Integer> syncList =
    Collections.synchronizedList(
        new ArrayList<>()
    );
```

Similarly:

```java
Collections.synchronizedSet(...)
Collections.synchronizedMap(...)
Collections.synchronizedCollection(...)
```

---

## Unmodifiable Wrappers

Prevent modification through the returned reference.

```java
List<Integer> list =
    new ArrayList<>();

List<Integer> view =
    Collections.unmodifiableList(list);
```

```java
view.add(10); // throws exception
```

Supported versions:

```java
Collections.unmodifiableList(...)
Collections.unmodifiableSet(...)
Collections.unmodifiableMap(...)
Collections.unmodifiableCollection(...)
```

---

## Why do we still use Collections after Java 8?

Some methods have moved to interfaces:

```java
list.sort(...)
list.forEach(...)
list.removeIf(...)
```

For example:

```java
Collections.sort(list);
```

can now be written as:

```java
list.sort(null);
```

But `Collections` is still useful for:

* `binarySearch`
* `shuffle`
* `reverse`
* `swap`
* `min`
* `max`
* `frequency`
* `unmodifiableXXX`
* `synchronizedXXX`
* `emptyXXX`
* `singletonXXX`
* `nCopies`

---

## Collections vs Collection

Many developers confuse these:

| Type                   | What is it?                       |
| ---------------------- | --------------------------------- |
| `Collection`           | Interface                         |
| `Collections`          | Utility class                     |
| `List`, `Set`, `Queue` | Interfaces extending `Collection` |
| `ArrayList`, `HashSet` | Implementations                   |

```java
Collection<Integer> c = new ArrayList<>();
```

Here `Collection` is an interface.

```java
Collections.sort(list);
```

Here `Collections` is a utility class.

---

### Interview Question

**Why is `Collections.sort()` able to sort a list if `ArrayList` is not Comparable?**

Because `Collections.sort(List<T>)` sorts the **elements** of the list, not the list object itself.

```java
List<Integer> list = new ArrayList<>();
Collections.sort(list);
```

Here `Integer` implements `Comparable<Integer>`, so sorting works even though `ArrayList` does not implement `Comparable`.
