### Inherited from parent (not redeclared here)

```java
void forEach(Consumer<? super T> action);  // Declared in "Iterable", Inheriting from "Iterable"
```

### Constructors

```java
public HashSet();

public HashSet(Collection<? extends E> c);

public HashSet(int initialCapacity, float loadFactor);

public HashSet(int initialCapacity);
```

   

---

### Query Operations

```java
public int size();

public boolean isEmpty();

public boolean contains(Object o);
```

  

---

### Iteration

```java
public Iterator<E> iterator();  // Iterable

public Spliterator<E> spliterator(); // Iterable
```

 

---

### Modification Operations

```java
public boolean add(E e);

public boolean remove(Object o);

public void clear();
```

  

---

### Array Conversion

```java
public Object[] toArray();

public <T> T[] toArray(T[] a);
```

 

---

### Object Methods

```java
public Object clone();
```



---

### Static Factory Method

```java
public static <T> HashSet<T> newHashSet(int numElements);
```



---

### Complete Public Method List (excluding constructors)

```java
public Iterator<E> iterator();  // Iterable

public int size();

public boolean isEmpty();

public boolean contains(Object o);

public boolean add(E e);

public boolean remove(Object o);

public void clear();

public Object clone();

public Spliterator<E> spliterator();  // Iterable

public Object[] toArray();

public <T> T[] toArray(T[] a);

public static <T> HashSet<T> newHashSet(int numElements);
```

           
