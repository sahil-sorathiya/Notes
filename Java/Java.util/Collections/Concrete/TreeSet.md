From `java.util.TreeSet<E>` 

### Constructors

```java
public TreeSet();

public TreeSet(Comparator<? super E> comparator);

public TreeSet(Collection<? extends E> c);

public TreeSet(SortedSet<E> s);
```

---

### Iteration & Views

```java
public Iterator<E> iterator();

public Iterator<E> descendingIterator();

public NavigableSet<E> descendingSet();

public Spliterator<E> spliterator();
```

---

### Query Operations

```java
public int size();

public boolean isEmpty();

public boolean contains(Object o);

public Comparator<? super E> comparator();

public E first();

public E last();
```

---

### Modification Operations

```java
public boolean add(E e);

public boolean remove(Object o);

public void clear();

public boolean addAll(Collection<? extends E> c);
```

---

### NavigableSet Operations

```java
public E lower(E e);

public E floor(E e);

public E ceiling(E e);

public E higher(E e);

public E pollFirst();

public E pollLast();
```

---

### Range Views

```java
public NavigableSet<E> subSet(E fromElement,
                              boolean fromInclusive,
                              E toElement,
                              boolean toInclusive);

public NavigableSet<E> headSet(E toElement,
                               boolean inclusive);

public NavigableSet<E> tailSet(E fromElement,
                               boolean inclusive);

public SortedSet<E> subSet(E fromElement,
                           E toElement);

public SortedSet<E> headSet(E toElement);

public SortedSet<E> tailSet(E fromElement);
```

---

### SequencedSet Operations (Java 21+)

```java
public void addFirst(E e);

public void addLast(E e);
```

---

### Object Methods

```java
public Object clone();
```

---

### Complete Public Method List (excluding constructors)

```java
public Iterator<E> iterator();

public Iterator<E> descendingIterator();

public NavigableSet<E> descendingSet();

public int size();

public boolean isEmpty();

public boolean contains(Object o);

public boolean add(E e);

public boolean remove(Object o);

public void clear();

public boolean addAll(Collection<? extends E> c);

public NavigableSet<E> subSet(E fromElement,
                              boolean fromInclusive,
                              E toElement,
                              boolean toInclusive);

public NavigableSet<E> headSet(E toElement,
                               boolean inclusive);

public NavigableSet<E> tailSet(E fromElement,
                               boolean inclusive);

public SortedSet<E> subSet(E fromElement,
                           E toElement);

public SortedSet<E> headSet(E toElement);

public SortedSet<E> tailSet(E fromElement);

public Comparator<? super E> comparator();

public E first();

public E last();

public E lower(E e);

public E floor(E e);

public E ceiling(E e);

public E higher(E e);

public E pollFirst();

public E pollLast();

public void addFirst(E e);

public void addLast(E e);

public Object clone();

public Spliterator<E> spliterator();
```
