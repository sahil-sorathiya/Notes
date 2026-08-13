### Inherited from parent (not redeclared here)

```java
void forEach(Consumer<? super T> action);  // Declared in "Iterable", Inheriting from "Iterable"
Spliterator<T> spliterator();  // Declared in "Iterable", Inheriting from "Collection"
```

## Public Methods in `AbstractList<E>`

### Core List Operations

```java
public boolean add(E e);

public abstract E get(int index);

public E set(int index, E element);

public void add(int index, E element);

public E remove(int index);
```

    

---

### Search Operations

```java
public int indexOf(Object o);

public int lastIndexOf(Object o);
```

 

---

### Bulk Operations

```java
public void clear();

public boolean addAll(int index,
                      Collection<? extends E> c);
```

 

---

### Iterator Operations

```java
public Iterator<E> iterator();  // Iterable

public ListIterator<E> listIterator();

public ListIterator<E> listIterator(int index);
```

  

---

### View Operations

```java
public List<E> subList(int fromIndex,
                       int toIndex);
```



---

### Equality & Hashing

```java
public boolean equals(Object o);

public int hashCode();
```

 

---

## Protected Methods

Not public, but important for subclasses:

```java
protected void removeRange(int fromIndex,
                           int toIndex);
```



---

## Summary

`AbstractList` declares **13 public methods** of its own:

```java
add(E)
get(int)
set(int,E)
add(int,E)
remove(int)

indexOf(Object)
lastIndexOf(Object)

clear()
addAll(int, Collection)

iterator()  // Iterable
listIterator()
listIterator(int)

subList(int,int)

equals(Object)
hashCode()
```

That's actually **15 public methods** if you count `equals()` and `hashCode()` separately in the total. 
