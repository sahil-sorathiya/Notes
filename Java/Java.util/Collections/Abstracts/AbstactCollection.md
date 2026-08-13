### Inherited from parent (not redeclared here)

```java
void forEach(Consumer<? super T> action);  // Declared in "Iterable", Inheriting from "Iterable"
Spliterator<T> spliterator();  // Declared in "Iterable", Inheriting from "Collection"
```

### Abstract Methods

```java
public abstract Iterator<E> iterator();  // Iterable

public abstract int size();
```

---

### Query Operations

```java
public boolean isEmpty();

public boolean contains(Object o);

public Object[] toArray();

public <T> T[] toArray(T[] a);
```

---

### Modification Operations

```java
public boolean add(E e);

public boolean remove(Object o);
```

---

### Bulk Operations

```java
public boolean containsAll(Collection<?> c);

public boolean addAll(Collection<? extends E> c);

public boolean removeAll(Collection<?> c);

public boolean retainAll(Collection<?> c);

public void clear();
```

---

### Object Methods Overridden

```java
public String toString();
```

---

### Summary

| Method                            | Return Type   |
| --------------------------------- | ------------- |
| `iterator()`                      | `Iterator<E>` |
| `size()`                          | `int`         |
| `isEmpty()`                       | `boolean`     |
| `contains(Object)`                | `boolean`     |
| `toArray()`                       | `Object[]`    |
| `toArray(T[])`                    | `T[]`         |
| `add(E)`                          | `boolean`     |
| `remove(Object)`                  | `boolean`     |
| `containsAll(Collection<?>)`      | `boolean`     |
| `addAll(Collection<? extends E>)` | `boolean`     |
| `removeAll(Collection<?>)`        | `boolean`     |
| `retainAll(Collection<?>)`        | `boolean`     |
| `clear()`                         | `void`        |
| `toString()`                      | `String`      |

**Total public methods declared:** **14** 

**Abstract methods:** 2 (`iterator`, `size`) 

**Concrete methods:** 12  

Note that methods such as `removeIf()`, `stream()`, `parallelStream()`, `spliterator()`, `forEach()`, etc., are **not declared in `AbstractCollection`**. They are inherited as default methods from `Collection` and `Iterable`.
