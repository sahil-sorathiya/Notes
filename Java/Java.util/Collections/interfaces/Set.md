From `java.util.Set<E>` interface, the public methods are: 

---

### Query Operations

```java
int size();

boolean isEmpty();

boolean contains(Object o);

Iterator<E> iterator();

Object[] toArray();

<T> T[] toArray(T[] a);
```

---

### Modification Operations

```java
boolean add(E e);

boolean remove(Object o);
```

---

### Bulk Operations

```java
boolean containsAll(Collection<?> c);

boolean addAll(Collection<? extends E> c);

boolean retainAll(Collection<?> c);

boolean removeAll(Collection<?> c);

void clear();
```

---

### Equality & Hashing

```java
boolean equals(Object o);

int hashCode();
```

---

### Default Methods

```java
default Spliterator<E> spliterator();
```

---

### Static Factory Methods

```java
static <E> Set<E> of();

static <E> Set<E> of(E e1);

static <E> Set<E> of(E e1, E e2);

static <E> Set<E> of(E e1, E e2, E e3);

static <E> Set<E> of(E e1, E e2, E e3, E e4);

static <E> Set<E> of(E e1, E e2, E e3, E e4, E e5);

static <E> Set<E> of(E e1, E e2, E e3, E e4, E e5, E e6);

static <E> Set<E> of(E e1, E e2, E e3, E e4, E e5, E e6, E e7);

static <E> Set<E> of(E e1, E e2, E e3, E e4, E e5, E e6, E e7, E e8);

static <E> Set<E> of(E e1, E e2, E e3, E e4, E e5, E e6, E e7, E e8, E e9);

static <E> Set<E> of(
    E e1, E e2, E e3, E e4, E e5,
    E e6, E e7, E e8, E e9, E e10);

static <E> Set<E> of(E... elements);

static <E> Set<E> copyOf(Collection<? extends E> coll);
```

### Summary

* **Inherited from `Collection`**: all instance methods except the static factories and `spliterator()` redeclaration.
* **Declared directly in `Set`**:

  ```java
  default Spliterator<E> spliterator();

  static <E> Set<E> of(...);   // 12 overloads including varargs

  static <E> Set<E> copyOf(Collection<? extends E> coll);
  ```
