The `Iterable<T>` interface has **3 public methods**.

### 1. `iterator()`

```java
Iterator<T> iterator();
```

* Abstract method.
* Must be implemented by classes implementing `Iterable`.
* Returns an `Iterator<T>`.
* Used by the enhanced for-loop (`for-each`).

Example:

```java
for (String s : list) {
    System.out.println(s);
}
```

Internally becomes roughly:

```java
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    String s = it.next();
    System.out.println(s);
}
```

---

### 2. `forEach(Consumer<? super T> action)`

```java
default void forEach(Consumer<? super T> action)
```

* Default method (introduced in Java 8).
* Iterates through all elements and applies the given action.

Default implementation:

```java
    default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) {
            action.accept(t);
        }
    }
```

Example:

```java
list.forEach(System.out::println);
```

---

### 3. `spliterator()`

```java
default Spliterator<T> spliterator()
```

* Default method (introduced in Java 8).
* Returns a `Spliterator<T>`.
* Primarily used by Streams, especially parallel streams.

Default implementation:

```java
return Spliterators.spliteratorUnknownSize(iterator(), 0);
```

Example:

```java
Spliterator<String> sp = list.spliterator();
```

---

### Summary

| Method                         | Return Type      | Abstract/Default |
| ------------------------------ | ---------------- | ---------------- |
| `iterator()`                   | `Iterator<T>`    | Abstract         |
| `forEach(Consumer<? super T>)` | `void`           | Default          |
| `spliterator()`                | `Spliterator<T>` | Default          |

These are the only public methods declared directly in `Iterable<T>`. The most important one is `iterator()`, because implementing it automatically enables support for the enhanced `for-each` loop.
