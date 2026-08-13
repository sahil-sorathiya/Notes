## Inherited from Parent (not redeclared here)

```java
// Declared in "Iterable", Inheriting from "Iterable"
void forEach(Consumer<? super T> action);

// Declared in "Collection", Inheriting from "Collection"
<T> T[] toArray(IntFunction<T[]> generator);
boolean removeIf(Predicate<? super E> filter);
Stream<E> stream();
Stream<E> parallelStream();
```

## `Iterable` Methods

```java
Iterator<E> iterator();  // Iterable
default Spliterator<E> spliterator();  // Iterable
```

## `Collection` Methods

### Query Operations

```java
int size();

boolean isEmpty();

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
boolean containsAll(Collection<?> c);

boolean addAll(Collection<? extends E> c);

boolean removeAll(Collection<?> c);

boolean retainAll(Collection<?> c);

void clear();
```

### Comparison & Hashing

```java
boolean equals(Object o);

int hashCode();
```

## `SequencedCollection` Methods (Java 21+)

```java
default void addFirst(E e);

default void addLast(E e);

default E getFirst();

default E getLast();

default E removeFirst();

default E removeLast();

default List<E> reversed();
```

## Declared Directly in `List`

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

default void replaceAll(UnaryOperator<E> operator);

default void sort(Comparator<? super E> c);
```

### Search Operations

```java
int indexOf(Object o);

int lastIndexOf(Object o);
```

### Iterators

```java
ListIterator<E> listIterator();

ListIterator<E> listIterator(int index);
```

### Views

```java
List<E> subList(int fromIndex, int toIndex);
```

### Static Factory Methods

```java
static <E> List<E> of();
static <E> List<E> of(E e1);
static <E> List<E> of(E e1, E e2);
static <E> List<E> of(E e1, E e2, E e3);
static <E> List<E> of(E e1, E e2, E e3, E e4);
static <E> List<E> of(E e1, E e2, E e3, E e4, E e5);
static <E> List<E> of(E e1, E e2, E e3, E e4, E e5, E e6);
static <E> List<E> of(E e1, E e2, E e3, E e4, E e5, E e6, E e7);
static <E> List<E> of(E e1, E e2, E e3, E e4, E e5, E e6, E e7, E e8);
static <E> List<E> of(E e1, E e2, E e3, E e4, E e5, E e6, E e7, E e8, E e9);
static <E> List<E> of(E e1, E e2, E e3, E e4, E e5, E e6, E e7, E e8, E e9, E e10);
static <E> List<E> of(E... elements);

static <E> List<E> copyOf(Collection<? extends E> coll);
```