The file is the source of `java.util.ArrayList`. 

### Public constructors

```java
ArrayList(int initialCapacity)
ArrayList()
ArrayList(Collection<? extends E> c)
```

### Public methods

```java
void trimToSize()
void ensureCapacity(int minCapacity)

int size()
boolean isEmpty()
boolean contains(Object o)

int indexOf(Object o)
int lastIndexOf(Object o)

Object clone()

Object[] toArray()
<T> T[] toArray(T[] a)

E get(int index)
E getFirst()
E getLast()

E set(int index, E element)

boolean add(E e)
void add(int index, E element)
void addFirst(E element)
void addLast(E element)

E remove(int index)
boolean remove(Object o)
E removeFirst()
E removeLast()

boolean equals(Object o)
int hashCode()

void clear()

boolean addAll(Collection<? extends E> c)
boolean addAll(int index, Collection<? extends E> c)

boolean removeAll(Collection<?> c)
boolean retainAll(Collection<?> c)

ListIterator<E> listIterator(int index)
ListIterator<E> listIterator()

Iterator<E> iterator()

List<E> subList(int fromIndex, int toIndex)

void forEach(Consumer<? super E> action)

Spliterator<E> spliterator()

boolean removeIf(Predicate<? super E> filter)

void replaceAll(UnaryOperator<E> operator)

void sort(Comparator<? super E> c)
```

### Protected methods

```java
void removeRange(int fromIndex, int toIndex)
```

