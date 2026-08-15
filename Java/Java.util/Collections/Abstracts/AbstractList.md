### Inherited from Parent (not redeclared here)

```java
// Declared in "Object" & "Collection", Inheriting from "AbstractCollection"
public String toString();

// Declared in "Iterable", Inheriting from "Iterable"
default void forEach(Consumer<? super T> action);

// Declared in "Iterable", Inheriting from "Collection"
default Spliterator<T> spliterator();

// Declared in "Collection", Inheriting from "Collection"
default <T> T[] toArray(IntFunction<T[]> generator);
default boolean removeIf(Predicate<? super E> filter);
default Stream<E> stream();
default Stream<E> parallelStream();

// Declared in "Collection", Inheriting from "AbstractCollection"
public abstract int size();
public boolean isEmpty();
public boolean contains(Object o);
public Object[] toArray();
public <T> T[] toArray(T[] a);
public boolean remove(Object o);
public boolean containsAll(Collection<?> c);
public boolean addAll(Collection<? extends E> c);
public boolean removeAll(Collection<?> c);
public boolean retainAll(Collection<?> c);

// Declared in "SequencedCollection", Inheriting from "List"
default void addFirst(E e);
default void addLast(E e);
default E getFirst();
default E getLast();
default E removeFirst();
default E removeLast();
default List<E> reversed();

// Declared in "List", Inheriting from "List"
default void replaceAll(UnaryOperator<E> operator);
default void sort(Comparator<? super E> c);
static <E> List<E> of(E... elements);
static <E> List<E> copyOf(Collection<? extends E> coll);
```

## `Iterable` Methods

```java
public Iterator<E> iterator();  // Iterable
```

## `Collection` Methods

### Modification Operations

```java
public boolean add(E e);
```

### Bulk Operations

```java
public void clear();
```

### `Object` Methods

```java
boolean equals(Object o);

int hashCode();
```

## `List` Methods

### Positional Access

```java
public abstract E get(int index);

public E set(int index, E element);

public void add(int index, E element);

public E remove(int index);
```

### Bulk Operations

```java
public boolean addAll(int index, Collection<? extends E> c);
```

### Search Operations

```java
public int indexOf(Object o);

public int lastIndexOf(Object o);
```

### Iterators

```java
public ListIterator<E> listIterator();

public ListIterator<E> listIterator(int index);
```

### Views

```java
public List<E> subList(int fromIndex, int toIndex);
```

## `AbstractList` class do not have its own methods.