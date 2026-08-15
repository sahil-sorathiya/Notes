## Inherited from Parent (not redeclared here)

```java
// Declared in "Object", Inherited from "AbstractCollection"
String toString();

// Declared in "Collection", Inherited from "Collection"
default <T> T[] toArray(IntFunction<T[]> generator);
default Stream<E> stream();
default Stream<E> parallelStream();

// Declared in "Collection", Inherited from "AbstractCollection"
boolean containsAll(Collection<?> c);

// Declared in "SequencedCollection", Inherited from "List"
default SequencedCollection<E> reversed();

// Declared in "List", Inherited from "List"
static <E> List<E> of(E... elements);
static <E> List<E> copyOf(Collection<? extends E> coll);
```

## Constructors

```java
ArrayList(int initialCapacity)
ArrayList()
ArrayList(Collection<? extends E> c)
```

## `Object` Methods

```java
Object clone()
```

## `Iterable` Methods

```java
Iterator<E> iterator()  // Iterable

default void forEach(Consumer<? super E> action)  // Iterable

Spliterator<E> spliterator()  // Iterable
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
boolean addAll(Collection<? extends E> c);

boolean removeAll(Collection<?> c);

default boolean removeIf(Predicate<? super E> filter);

boolean retainAll(Collection<?> c);

void clear();
```

### 'Object' Methods

```java
boolean equals(Object o);

int hashCode();
```

## `SequencedCollection` Methods
```java
default void addFirst(E e)

default void addLast(E e)

default E getFirst()

default E getLast()

default E removeFirst()

default E removeLast()
```

## `List` Methods

### Positional Access

```java
abstract E get(int index);

E set(int index, E element);

void add(int index, E element);

E remove(int index);
```

### Bulk Operations

```java
boolean addAll(int index, Collection<? extends E> c);

void replaceAll(UnaryOperator<E> operator)

void sort(Comparator<? super E> c)
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


## Declared Directly in `ArrayList`

```java
void trimToSize()
void ensureCapacity(int minCapacity)
```

