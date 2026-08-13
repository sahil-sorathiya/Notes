### Constructors

```java
public LinkedHashSet(int initialCapacity, float loadFactor);

public LinkedHashSet(int initialCapacity);

public LinkedHashSet();

public LinkedHashSet(Collection<? extends E> c);
```

### Iteration

```java
public Spliterator<E> spliterator();
```

### SequencedSet Operations

```java
public void addFirst(E e);

public void addLast(E e);

public E getFirst();

public E getLast();

public E removeFirst();

public E removeLast();

public SequencedSet<E> reversed();
```

### Static Factory Method

```java
public static <T> LinkedHashSet<T> newLinkedHashSet(int numElements);
```
