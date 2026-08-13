The uploaded file is the source of `java.util.Vector<E>`. 

### Public Constructors

```java
Vector(int initialCapacity, int capacityIncrement)
Vector(int initialCapacity)
Vector()
Vector(Collection<? extends E> c)
```

### Public Methods

```java
void copyInto(Object[] anArray)

void trimToSize()

void ensureCapacity(int minCapacity)

void setSize(int newSize)

int capacity()

int size()

boolean isEmpty()

Enumeration<E> elements()

boolean contains(Object o)

int indexOf(Object o)

int indexOf(Object o, int index)

int lastIndexOf(Object o)

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

Object clone()

Object[] toArray()

<T> T[] toArray(T[] a)

E get(int index)

E set(int index, E element)

boolean add(E e)

boolean remove(Object o)

void add(int index, E element)

E remove(int index)

void clear()

boolean containsAll(Collection<?> c)

boolean addAll(Collection<? extends E> c)

boolean removeAll(Collection<?> c)

boolean retainAll(Collection<?> c)

boolean removeIf(Predicate<? super E> filter)

boolean addAll(int index, Collection<? extends E> c)

boolean equals(Object o)

int hashCode()

String toString()

List<E> subList(int fromIndex, int toIndex)

ListIterator<E> listIterator(int index)

ListIterator<E> listIterator()

Iterator<E> iterator()

void forEach(Consumer<? super E> action)

void replaceAll(UnaryOperator<E> operator)

void sort(Comparator<? super E> c)

Spliterator<E> spliterator()
```

### Protected Methods

```java
void removeRange(int fromIndex, int toIndex)
```

Total:

* **4 public constructors**
* **45 public methods**
* **1 protected method**  
