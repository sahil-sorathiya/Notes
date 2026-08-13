From `java.util.SortedSet<E>`, the public methods are: 

---

### Ordering Operations

```java
Comparator<? super E> comparator();

SortedSet<E> subSet(E fromElement, E toElement);

SortedSet<E> headSet(E toElement);

SortedSet<E> tailSet(E fromElement);

E first();

E last();
```

---

### Spliterator

```java
default Spliterator<E> spliterator();
```

---

### SequencedCollection Operations

```java
default void addFirst(E e);

default void addLast(E e);

default E getFirst();

default E getLast();

default E removeFirst();

default E removeLast();

default SortedSet<E> reversed();
```

---

### Inherited from Set

```java
int size();

boolean isEmpty();

boolean contains(Object o);

Iterator<E> iterator();

Object[] toArray();

<T> T[] toArray(T[] a);

boolean add(E e);

boolean remove(Object o);

boolean containsAll(Collection<?> c);

boolean addAll(Collection<? extends E> c);

boolean retainAll(Collection<?> c);

boolean removeAll(Collection<?> c);

void clear();

boolean equals(Object o);

int hashCode();
```

---

### Methods Declared Directly in `SortedSet`

```java
Comparator<? super E> comparator();

SortedSet<E> subSet(E fromElement, E toElement);

SortedSet<E> headSet(E toElement);

SortedSet<E> tailSet(E fromElement);

E first();

E last();

default Spliterator<E> spliterator();

default void addFirst(E e);

default void addLast(E e);

default E getFirst();

default E getLast();

default E removeFirst();

default E removeLast();

default SortedSet<E> reversed();
```

**New operations introduced by `SortedSet` compared to `Set`:**

```java
Comparator<? super E> comparator();

SortedSet<E> subSet(E fromElement, E toElement);

SortedSet<E> headSet(E toElement);

SortedSet<E> tailSet(E fromElement);

E first();

E last();
```

The remaining methods (`spliterator`, `addFirst`, `addLast`, `getFirst`, `getLast`, `removeFirst`, `removeLast`, `reversed`) are implementations or covariant overrides of methods inherited from `SequencedSet` / `SequencedCollection`.
