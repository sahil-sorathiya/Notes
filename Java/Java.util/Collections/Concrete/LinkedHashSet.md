## Inherited from Parent (not redeclared here)

```java
// Declared in "Object" & "Collection", Inheriting from "AbstractSet"
boolean equals(Object o);
int hashCode();

// Declared in "Object", Inheriting from "AbstractCollection"
String toString();

// Declared in "Iterable", Inheriting from "Iterable"
default void forEach(Consumer<? super T> action);

// Declared in "Collection", Inheriting from "Collection"
default <T> T[] toArray(IntFunction<T[]> generator);
default boolean removeIf(Predicate<? super E> filter);
default Stream<E> stream();
default Stream<E> parallelStream();

// Declared in "Collection", Inheriting from "AbstractCollection"
boolean containsAll(Collection<?> c);
boolean addAll(Collection<? extends E> c);
boolean retainAll(Collection<?> c);

// Declared in "Collection", Inheriting from "AbstractSet"
boolean removeAll(Collection<?> c);

// Declared in "Set", Inheriting from "Set"
static <E> Set<E> of(E... elements);
static <E> Set<E> copyOf(Collection<? extends E> coll);

// Declared in "HashSet", Inheriting from "HashSet"
static <T> HashSet<T> newHashSet(int numElements);
```

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

### `SequencedCollection` Operations

```java
public SequencedSet<E> reversed();

public void addFirst(E e);

public void addLast(E e);

public E getFirst();

public E getLast();

public E removeFirst();

public E removeLast();
```

### Static Factory Method

```java
public static <T> LinkedHashSet<T> newLinkedHashSet(int numElements);
```
