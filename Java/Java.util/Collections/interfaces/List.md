The uploaded file is the `java.util.List` interface source. Here are **all public methods declared in `List<E>`**, grouped by category. 

### Query Operations

```java
int size();
boolean isEmpty();
boolean contains(Object o);
Iterator<E> iterator();
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
boolean addAll(int index, Collection<? extends E> c);
boolean removeAll(Collection<?> c);
boolean retainAll(Collection<?> c);
default void replaceAll(UnaryOperator<E> operator);
default void sort(Comparator<? super E> c);
void clear();
```



### Equality & Hashing

```java
boolean equals(Object o);
int hashCode();
```



### Positional Access

```java
E get(int index);
E set(int index, E element);
void add(int index, E element);
E remove(int index);
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
default Spliterator<E> spliterator();
```



### SequencedCollection Methods (Java 21+)

```java
default void addFirst(E e);
default void addLast(E e);
default E getFirst();
default E getLast();
default E removeFirst();
default E removeLast();
default List<E> reversed();
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



### Count Summary

| Category                       | Methods |
| ------------------------------ | ------- |
| Query                          | 6       |
| Modification                   | 2       |
| Bulk                           | 8       |
| Equality/Hashing               | 2       |
| Positional Access              | 4       |
| Search                         | 2       |
| Iterators                      | 2       |
| Views                          | 2       |
| SequencedCollection (Java 21+) | 7       |
| Static Factory Methods         | 13      |

**Total public methods declared in `List<E>`: 48** (including default and static methods). 


The only inherited methods available on `List` that are **not redeclared anywhere in `List.java`** are:

```java
<T> T[] toArray(IntFunction<T[]> generator);

forEach(Consumer<? super E> action);

removeIf(Predicate<? super E> filter);

Stream<E> stream();

Stream<E> parallelStream();
```

Everything else that `List` inherits from `Iterable`, `Collection`, or `SequencedCollection` is explicitly redeclared or overridden in the `List` source you uploaded. 
