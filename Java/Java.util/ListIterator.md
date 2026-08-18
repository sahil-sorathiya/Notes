# `Iterator` Methods

```java
public interface Iterator<E> {
    // --------  Inherited from Iterator -------- //
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

    // -------- Own Methods -------- //
    // Not throw any exception - only returns true or false
    boolean hasPrevious();

    // Throws: 
    // NoSuchElementException – if the iteration has no more elements
    E previous();

    // Not throw any exception - only returns int
    // Returns :
    // List size if curr element is last element of list
    int nextIndex();

    // Not throw any exception - only returns int
    // Returns :
    // -1 if curr element is first element of list
    int previousIndex();

    // Throws:
    // UnsupportedOperationException – if the set operation is not supported by this list iterator
    // ClassCastException – if the class of the specified element prevents it from being added to this list
    // IllegalArgumentException – if some aspect of the specified element prevents it from being added to this list
    // IllegalStateException – if neither next nor previous have been called, or remove or add have been called after the last call to next or previous
    void set(E e);

    // Throws:
    // UnsupportedOperationException – if the add method is not supported by this list iterator
    // ClassCastException – if the class of the specified element prevents it from being added to this list
    // IllegalArgumentException – if some aspect of this element prevents it from being added to this list
    void add(E e);
}
```

## What is `ListIterator` in Java?

`ListIterator` is a **specialized iterator for List implementations** that allows:

* Forward traversal
* Backward traversal
* Element modification
* Element insertion
* Access to current index positions

It extends `Iterator`.

```java
public interface ListIterator<E> extends Iterator<E>
```

## Why do we need ListIterator?

A normal `Iterator` can only move forward:

```java
Iterator<String> it = list.iterator();

it.next();
it.next();
```

You cannot:

* Move backwards
* Add elements during iteration
* Replace elements during iteration
* Get current position

`ListIterator` solves these problems.

## Creating a ListIterator

```java
List<String> list = new ArrayList<>();

ListIterator<String> it = list.listIterator();
```

Or start at a specific index:

```java
ListIterator<String> it = list.listIterator(3);
```

The cursor starts before index 3.

## New Methods in ListIterator

### 1. `hasPrevious()`

Checks if there is an element before the cursor.

```java
if (it.hasPrevious()) {
    ...
}
```

### 2. `previous()`

Moves backward and returns the previous element.

```java
String s = it.previous();
```

Example:

```java
List<String> list = List.of("A", "B", "C");

ListIterator<String> it =
        new ArrayList<>(list).listIterator(3);

while (it.hasPrevious()) {
    System.out.println(it.previous());
}
```

Output:

```text
C
B
A
```

### 3. `nextIndex()`

Returns the index of the element that would be returned by `next()`.

```java
System.out.println(it.nextIndex());
```

Example:

```java
[A, B, C]

Cursor before A

nextIndex() = 0
```

### 4. `previousIndex()`

Returns the index of the element that would be returned by `previous()`.

```java
previousIndex()
```

Initially:

```text
nextIndex()     = 0
previousIndex() = -1
```

### 5. `set(E e)`

Replaces the last element returned by `next()` or `previous()`.

```java
List<String> list =
        new ArrayList<>(List.of("A", "B", "C"));

ListIterator<String> it = list.listIterator();

while (it.hasNext()) {
    String s = it.next();

    if (s.equals("B")) {
        it.set("X");
    }
}

System.out.println(list);
```

Output:

```text
[A, X, C]
```

### 6. `add(E e)`

Inserts an element at the cursor position.

```java
List<String> list =
        new ArrayList<>(List.of("A", "C"));

ListIterator<String> it = list.listIterator();

it.next();       // A
it.add("B");

System.out.println(list);
```

Output:

```text
[A, B, C]
```

## Iterator vs ListIterator

| Feature                  | Iterator | ListIterator |
| ------------------------ | -------- | ------------ |
| Forward traversal        | ✅        | ✅            |
| Backward traversal       | ❌        | ✅            |
| Remove element           | ✅        | ✅            |
| Replace element          | ❌        | ✅            |
| Add element              | ❌        | ✅            |
| Get current index        | ❌        | ✅            |
| Works on all collections | ✅        | ❌            |
| Works only on List       | ❌        | ✅            |

## Which collections support ListIterator?

Since it is list-specific:

```java
ArrayList
LinkedList
Vector
Stack
```

support:

```java
list.listIterator();
```

But:

```java
HashSet
TreeSet
ArrayDeque
PriorityQueue
HashMap
```

do **not** support `ListIterator`.

## Internal Use in ArrayList vs LinkedList

A common interview question:

```java
list.listIterator(index);
```

### ArrayList

Fast:

```java
O(1)
```

because it can jump directly to the index.

### LinkedList

Needs traversal to reach that node:

```java
O(min(index, n-index))
```

because it walks from the nearest end.

### When should you use ListIterator?

Use `ListIterator` when you need to:

* Traverse a list in both directions
* Modify elements while iterating
* Insert elements during iteration
* Know the current index position

Otherwise, a simple `Iterator` or enhanced `for` loop is usually cleaner.
