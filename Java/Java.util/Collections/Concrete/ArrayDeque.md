This file is the source of **`java.util.ArrayDeque<E>`**. The following are all the **public methods and constructors** declared in the class. 

---

### Constructors

```java
public ArrayDeque();

public ArrayDeque(int numElements);

public ArrayDeque(Collection<? extends E> c);
```

---

### Deque Operations

```java
public void addFirst(E e);

public void addLast(E e);

public boolean offerFirst(E e);

public boolean offerLast(E e);

public E removeFirst();

public E removeLast();

public E pollFirst();

public E pollLast();

public E getFirst();

public E getLast();

public E peekFirst();

public E peekLast();

public boolean removeFirstOccurrence(Object o);

public boolean removeLastOccurrence(Object o);

public Iterator<E> descendingIterator();
```

---

### Queue Operations

```java
public boolean add(E e);

public boolean offer(E e);

public E remove();

public E poll();

public E element();

public E peek();
```

---

### Stack Operations

```java
public void push(E e);

public E pop();
```

---

### Collection Operations

```java
public int size();

public boolean isEmpty();

public Iterator<E> iterator();

public Spliterator<E> spliterator();

public void forEach(Consumer<? super E> action);

public boolean removeIf(Predicate<? super E> filter);

public boolean removeAll(Collection<?> c);

public boolean retainAll(Collection<?> c);

public boolean contains(Object o);

public boolean remove(Object o);

public void clear();

public Object[] toArray();

public <T> T[] toArray(T[] a);
```

---

### Object Methods

```java
public ArrayDeque<E> clone();
```

---

### Summary

| Category                                          |  Count |
| ------------------------------------------------- | -----: |
| Constructors                                      |      3 |
| Deque methods                                     |     15 |
| Queue methods                                     |      6 |
| Stack methods                                     |      2 |
| Collection methods                                |     13 |
| Object methods                                    |      1 |
| **Total (including constructors)**                | **40** |
| **Total public methods (excluding constructors)** | **37** |
