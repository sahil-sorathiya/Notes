# `Iterator` Methods

```java
public interface Iterator<E> {
    // Not throw any exception - only returns true or false
    boolean hasNext();
    
    // Throws: 
    // NoSuchElementException – if the iteration has no more elements
    E next();

    // Throws: 
    // UnsupportedOperationException – if the remove operation is not supported by this iterator 
    // IllegalStateException – if the next method has not yet been called, or the remove method has already been called after the last call to the next method
    default void remove();

    // Throws:
    // NullPointerException – if the specified action is null
    default void forEachRemaining(Consumer<? super E> action);
}
```


## What is an Iterator in Java?

An **Iterator** is an object that allows you to traverse (iterate over) the elements of a collection one by one without exposing the underlying implementation.

It is defined in the `java.util` package:

```java
public interface Iterator<E>
```

## Why do we need it?

Suppose you have an `ArrayList`:

```java
List<String> list = new ArrayList<>();

list.add("A");
list.add("B");
list.add("C");
```

Without an iterator, you might use:

```java
for (int i = 0; i < list.size(); i++) {
    System.out.println(list.get(i));
}
```

But this only works efficiently for index-based collections like `ArrayList`.

What about:

```java
HashSet<String>
LinkedHashSet<String>
TreeSet<String>
ArrayDeque<String>
```

These collections don't provide indexed access.

An `Iterator` gives a **uniform way** to traverse all collections:

```java
Iterator<String> it = list.iterator();

while (it.hasNext()) {
    System.out.println(it.next());
}
```

## Methods of Iterator

```java
public interface Iterator<E> {
    boolean hasNext();
    E next();
    default void remove();
    default void forEachRemaining(Consumer<? super E> action);
}
```

### 1. `hasNext()`

Checks whether another element exists.

```java
it.hasNext();
```

Returns:

```java
true
```

if there is another element.

### 2. `next()`

Returns the next element and advances the cursor.

```java
String s = it.next();
```

Example:

```java
Iterator<String> it = list.iterator();

System.out.println(it.next()); // A
System.out.println(it.next()); // B
System.out.println(it.next()); // C
```

### 3. `remove()`

Removes the element returned by the most recent `next()` call.

```java
Iterator<String> it = list.iterator();

while (it.hasNext()) {
    String s = it.next();

    if (s.equals("B")) {
        it.remove();
    }
}
```

Result:

```java
[A, C]
```

## Why not use `list.remove()` inside iteration?

This causes a fail-fast exception:

```java
for (String s : list) {
    if (s.equals("B")) {
        list.remove(s); // BAD
    }
}
```

Output:

```java
ConcurrentModificationException
```

Correct way:

```java
Iterator<String> it = list.iterator();

while (it.hasNext()) {
    if (it.next().equals("B")) {
        it.remove();
    }
}
```

## Enhanced for-loop uses Iterator internally

This:

```java
for (String s : list) {
    System.out.println(s);
}
```

is roughly compiled as:

```java
Iterator<String> it = list.iterator();

while (it.hasNext()) {
    String s = it.next();
    System.out.println(s);
}
```

This is why all collections implementing `Iterable` can be used in a for-each loop.

## Relationship with Iterable

`Collection` extends `Iterable`.

```java
Iterable<E>
    ^
    |
Collection<E>
    ^
    |
List, Set, Queue, ...
```

`Iterable` defines:

```java
Iterator<T> iterator();
```

This is why every collection has:

```java
collection.iterator();
```

## Fail-Fast Behavior

Most collection iterators are **fail-fast**.

If the collection is structurally modified outside the iterator:

```java
Iterator<String> it = list.iterator();

list.add("D");

it.next(); // Exception
```

Java throws:

```java
ConcurrentModificationException
```

This is implemented using the collection's `modCount`.

## Where is Iterator used in Collections?

All major collections provide iterators:

```java
ArrayList
LinkedList
Vector
ArrayDeque
HashSet
LinkedHashSet
TreeSet
HashMap.keySet()
HashMap.values()
HashMap.entrySet()
PriorityQueue
```

Example:

```java
Map<String, Integer> map = new HashMap<>();

Iterator<Map.Entry<String, Integer>> it =
        map.entrySet().iterator();
```

## Summary

* `Iterator` provides a standard way to traverse collections.
* Main methods:

  ```java
  hasNext()
  next()
  remove()
  ```
* Obtained via:

  ```java
  collection.iterator()
  ```
* Used internally by the enhanced `for-each` loop.
* Allows safe removal during iteration.
* Most iterators are fail-fast and throw `ConcurrentModificationException` if the collection is modified outside the iterator.
* `ListIterator` is a more powerful iterator available only for `List` implementations.
