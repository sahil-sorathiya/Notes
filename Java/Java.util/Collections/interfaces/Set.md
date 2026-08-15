## Inherited from Parent (not redeclared here)

```java
// Declared in "Iterable", Inheriting from "Iterable"
default void forEach(Consumer<? super T> action);

// Declared in "Collection", Inheriting from "Collection"
default <T> T[] toArray(IntFunction<T[]> generator);
default boolean removeIf(Predicate<? super E> filter);
default Stream<E> stream();
default Stream<E> parallelStream();
```

## `Iterable` Methods

```java
Iterator<E> iterator();  // Iterable

Spliterator<E> spliterator(); // Iterable
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

boolean retainAll(Collection<?> c);

boolean removeAll(Collection<?> c);

void clear();
```

### 'Object' Methods

```java
boolean equals(Object o);

int hashCode();
```


## Declared Directly in `Set`

### Static Factory Methods

```java
static <E> Set<E> of();

static <E> Set<E> of(E e1);

static <E> Set<E> of(E e1, E e2);

static <E> Set<E> of(E e1, E e2, E e3);

static <E> Set<E> of(E e1, E e2, E e3, E e4);

static <E> Set<E> of(E e1, E e2, E e3, E e4, E e5);

static <E> Set<E> of(E e1, E e2, E e3, E e4, E e5, E e6);

static <E> Set<E> of(E e1, E e2, E e3, E e4, E e5, E e6, E e7);

static <E> Set<E> of(E e1, E e2, E e3, E e4, E e5, E e6, E e7, E e8);

static <E> Set<E> of(E e1, E e2, E e3, E e4, E e5, E e6, E e7, E e8, E e9);

static <E> Set<E> of(
    E e1, E e2, E e3, E e4, E e5,
    E e6, E e7, E e8, E e9, E e10);

static <E> Set<E> of(E... elements);

static <E> Set<E> copyOf(Collection<? extends E> coll);
```

