From `java.util.NavigableSet<E>`, the public methods are: 

---

### Navigation Operations

```java
E lower(E e);

E floor(E e);

E ceiling(E e);

E higher(E e);
```

---

### Endpoint Operations

```java
E pollFirst();

E pollLast();
```

---

### Iteration & Views

```java
Iterator<E> iterator();

NavigableSet<E> descendingSet();

Iterator<E> descendingIterator();

default NavigableSet<E> reversed();
```

---

### Range View Operations

```java
NavigableSet<E> subSet(E fromElement,
                       boolean fromInclusive,
                       E toElement,
                       boolean toInclusive);

NavigableSet<E> headSet(E toElement,
                        boolean inclusive);

NavigableSet<E> tailSet(E fromElement,
                        boolean inclusive);

SortedSet<E> subSet(E fromElement,
                    E toElement);

SortedSet<E> headSet(E toElement);

SortedSet<E> tailSet(E fromElement);
```

---

### Sequenced Collection Operations

```java
default E removeFirst();

default E removeLast();
```

---

### Inherited from SortedSet

```java
Comparator<? super E> comparator();

E first();

E last();

default Spliterator<E> spliterator();

default void addFirst(E e);

default void addLast(E e);

default E getFirst();

default E getLast();
```

---

### Inherited from Set

```java
int size();

boolean isEmpty();

boolean contains(Object o);

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

### Methods Declared Directly in `NavigableSet`

```java
E lower(E e);

E floor(E e);

E ceiling(E e);

E higher(E e);

E pollFirst();

E pollLast();

Iterator<E> iterator();

NavigableSet<E> descendingSet();

Iterator<E> descendingIterator();

NavigableSet<E> subSet(E fromElement,
                       boolean fromInclusive,
                       E toElement,
                       boolean toInclusive);

NavigableSet<E> headSet(E toElement,
                        boolean inclusive);

NavigableSet<E> tailSet(E fromElement,
                        boolean inclusive);

SortedSet<E> subSet(E fromElement,
                    E toElement);

SortedSet<E> headSet(E toElement);

SortedSet<E> tailSet(E fromElement);

default E removeFirst();

default E removeLast();

default NavigableSet<E> reversed();
```

### New operations introduced by `NavigableSet`

```java
E lower(E e);

E floor(E e);

E ceiling(E e);

E higher(E e);

E pollFirst();

E pollLast();

NavigableSet<E> descendingSet();

Iterator<E> descendingIterator();

NavigableSet<E> subSet(E fromElement,
                       boolean fromInclusive,
                       E toElement,
                       boolean toInclusive);

NavigableSet<E> headSet(E toElement,
                        boolean inclusive);

NavigableSet<E> tailSet(E fromElement,
                        boolean inclusive);
```

The methods `subSet(E,E)`, `headSet(E)`, `tailSet(E)`, `removeFirst()`, `removeLast()`, and `reversed()` are redeclarations/overrides of methods inherited from parent interfaces.
