## Inherited from Parent (not redeclared here)

```java
// Declared in "Object" & "Collection", Inheriting from "AbstractList"
boolean equals(Object o);
int hashCode();

// Declared in "Iterable", Inheriting from "Iterable"
default void forEach(Consumer<? super T> action);

// Declared in "Iterable", Inheriting from "AbstractSequencialList"
Iterator<E> iterator();

// Declared in "Collection", Inheriting from "Collection"
default <T> T[] toArray(IntFunction<T[]> generator);
default boolean removeIf(Predicate<? super E> filter);
default Stream<E> stream();
default Stream<E> parallelStream();

// Declared in "Collection", Inheriting from "AbstractCollection"
boolean isEmpty();
boolean containsAll(Collection<?> c);
boolean removeAll(Collection<?> c);
boolean retainAll(Collection<?> c);

// Declared in "List", Inheriting from "List"
default void replaceAll(UnaryOperator<E> operator);
default void sort(Comparator<? super E> c);
static <E> List<E> of(E... elements);
static <E> List<E> copyOf(Collection<? extends E> coll);

// Declared in "List", Inheriting from "AbstractList"
ListIterator<E> listIterator();
List<E> subList(int fromIndex, int toIndex);
```

## Constructors

```java
LinkedList();

LinkedList(Collection<? extends E> c);
```

## `Object` Methods

```java
Object clone();
```

## `Iterable` Methods

```java
Spliterator<E> spliterator();  // Iterable
```

## `Collection` Methods

### Query Operations

```java
int size();

boolean contains(Object o);

Object[] toArray();

<T> T[] toArray(T[] a);
```

### Modification Operations

```java
boolean add(E e);

boolean remove(Object o);
```

### Bulk Operations

```java
boolean addAll(Collection<? extends E> c);

void clear();
```

## `SequencedCollection` Methods

```java
LinkedList<E> reversed();

E getFirst();

E getLast();

E removeFirst();

E removeLast();

void addFirst(E e);

void addLast(E e);

```

## `List` Methods

### Positional Access

```java
E get(int index);

E set(int index, E element);

void add(int index, E element);

E remove(int index);
```

### Bulk Operations

```java
boolean addAll(int index, Collection<? extends E> c);
```

### Search Operations

```java
int indexOf(Object o);

int lastIndexOf(Object o);
```

### Iterators

```java
ListIterator<E> listIterator(int index);
```

## `Queue` Methods
```java
boolean offer(E e);

E remove();

E poll();

E element();

E peek();
```

## `Deque` Methods

```java
boolean offerFirst(E e);

boolean offerLast(E e);

E pollFirst();

E pollLast();

E peekFirst();

E peekLast();

boolean removeFirstOccurrence(Object o);

boolean removeLastOccurrence(Object o);

void push(E e);

E pop();

Iterator<E> descendingIterator();
```