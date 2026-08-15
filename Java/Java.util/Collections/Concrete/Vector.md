## Inherited from Parent (not redeclared here)

```java
// Declared in "Collection", Inherited from "Collection"
default <T> T[] toArray(IntFunction<T[]> generator);
default Stream<E> stream();
default Stream<E> parallelStream();

// Declared in "SequencedCollection", Inherited from "List"
default SequencedCollection<E> reversed();
default void addFirst(E e)
default void addLast(E e)
default E getFirst()
default E getLast()
default E removeFirst()
default E removeLast()

// Declared in "List", Inherited from "List"
static <E> List<E> of(E... elements);
static <E> List<E> copyOf(Collection<? extends E> coll);
```

### Constructors

```java
Vector(int initialCapacity, int capacityIncrement)
Vector(int initialCapacity)
Vector()
Vector(Collection<? extends E> c)
```

## `Object` Methods

```java
String toString()
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
boolean containsAll(Collection<?> c);

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

## Declared Directly in `Vector`

```java
void trimToSize()

void ensureCapacity(int minCapacity)

int capacity()

void copyInto(Object[] anArray)

void setSize(int newSize)

Enumeration<E> elements()

int indexOf(Object o, int index)

int lastIndexOf(Object o, int index)

E elementAt(int index)

E firstElement()

E lastElement()

void setElementAt(E obj, int index)

void removeElementAt(int index)

void insertElementAt(E obj, int index)

void addElement(E obj)

boolean removeElement(Object obj)

void removeAllElements()
```
