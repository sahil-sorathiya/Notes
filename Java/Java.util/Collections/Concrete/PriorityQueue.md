### Inherited from parent (not redeclared here)

```java

```

### Constructors

```java
public PriorityQueue();

public PriorityQueue(int initialCapacity);

public PriorityQueue(Comparator<? super E> comparator);

public PriorityQueue(int initialCapacity,
                     Comparator<? super E> comparator);

public PriorityQueue(Collection<? extends E> c);

public PriorityQueue(PriorityQueue<? extends E> c);

public PriorityQueue(SortedSet<? extends E> c);
```

---

### Queue Operations

```java
public boolean add(E e);

public boolean offer(E e);

public E peek();

public E poll();
```

---

### Collection Operations

```java
public boolean remove(Object o);

public boolean contains(Object o);

public Object[] toArray();

public <T> T[] toArray(T[] a);

public int size();

public void clear();
```

---

### Ordering

```java
public Comparator<? super E> comparator();
```

---

### Iterator Operations

```java
public Iterator<E> iterator();  // Iterable
public final Spliterator<E> spliterator();  // Iterable
public void forEach(Consumer<? super E> action);  // Iterable
```

---

### Bulk Operations

```java
public boolean removeIf(Predicate<? super E> filter);

public boolean removeAll(Collection<?> c);

public boolean retainAll(Collection<?> c);
```

---

### Summary

* Constructors: **7**
* Public methods: **17**
* Total public members (including constructors): **24**

Source: `java.util.PriorityQueue<E>` 
