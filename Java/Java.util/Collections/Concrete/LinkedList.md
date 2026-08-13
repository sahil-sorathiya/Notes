The file is the source of `java.util.LinkedList<E>` and contains the following **public methods**. 

### Constructors

```java
public LinkedList();

public LinkedList(Collection<? extends E> c);
```

---

### Deque Operations

```java
public E getFirst();

public E getLast();

public E removeFirst();

public E removeLast();

public void addFirst(E e);

public void addLast(E e);

public boolean offerFirst(E e);

public boolean offerLast(E e);

public E peekFirst();

public E peekLast();

public E pollFirst();

public E pollLast();

public void push(E e);

public E pop();

public boolean removeFirstOccurrence(Object o);

public boolean removeLastOccurrence(Object o);

public Iterator<E> descendingIterator();

public LinkedList<E> reversed();
```

---

### Queue Operations

```java
public E peek();

public E element();

public E poll();

public E remove();

public boolean offer(E e);
```

---

### Collection Operations

```java
public boolean contains(Object o);

public int size();

public boolean add(E e);

public boolean remove(Object o);

public boolean addAll(Collection<? extends E> c);

public void clear();

public Object[] toArray();

public <T> T[] toArray(T[] a);

public Spliterator<E> spliterator();

public Object clone();
```

---

### List Operations

```java
public boolean addAll(int index, Collection<? extends E> c);

public E get(int index);

public E set(int index, E element);

public void add(int index, E element);

public E remove(int index);

public int indexOf(Object o);

public int lastIndexOf(Object o);

public ListIterator<E> listIterator(int index);
```

---

### Total Public Methods

* Constructors: **2**
* Deque-specific methods: **17**
* Queue methods: **5**
* Collection methods: **10**
* List methods: **8**

**Total public methods (excluding constructors): 40**
**Including constructors: 42**.
