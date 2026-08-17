## Inherited from Parent (not redeclared here)

// Declared in "Map", Inheriting from "Map"

```java
static <K, V> Map<K, V> ofEntries(Entry<? extends K, ? extends V>... entries);
static <K, V> Entry<K, V> entry(K k, V v);
static <K, V> Map<K, V> of();
static <K, V> Map<K, V> of(K k1, V v1);
static <K, V> Map<K, V> of(K k1, V v1, K k2, V v2); // ...etc upto k10, v10
static <K, V> Map<K, V> copyOf(Map<? extends K, ? extends V> map);
```

## Constructors

```java
public Hashtable(int initialCapacity, float loadFactor)
public Hashtable(int initialCapacity)
public Hashtable()
public Hashtable(Map<? extends K, ? extends V> t)
```

## `Object` Methods
```java
synchronized public Object clone();
synchronized String toString()
```

## `Dictionary` Methods

```java
synchronized int size();  // Repeated in this notes
synchronized boolean isEmpty();  // Repeated in this notes
synchronized Enumeration<K> keys();
synchronized Enumeration<V> elements();
synchronized V get(Object key);  // Repeated in this notes
synchronized V put(K key, V value);  // Repeated in this notes
synchronized V remove(Object key);  // Repeated in this notes
```

## `Map` Methods

### Query Operations

```java
synchronized int size();
synchronized boolean isEmpty();
synchronized boolean containsKey(Object key);
synchronized boolean containsValue(Object value);
synchronized V get(Object key);
```

### Modification Operations

```java
synchronized V put(K key, V value);
synchronized V remove(Object key);
```

### Bulk Operations

```java
synchronized void putAll(Map<? extends K, ? extends V> m);
synchronized void clear();
```

### Views

```java
Set<K> keySet();
Collection<V> values();
Set<Map.Entry<K, V>> entrySet();
```

### Comparison and Hashing

```java
synchronized boolean equals(Object o);
synchronized int hashCode();
```

### Defaultable Methods

```java
synchronized default V getOrDefault(Object key, V defaultValue);
synchronized default void forEach(BiConsumer<? super K, ? super V> action);
synchronized default void replaceAll(BiFunction<? super K, ? super V, ? extends V> function);
synchronized default V putIfAbsent(K key, V value);
synchronized default boolean remove(Object key, Object value);
synchronized default boolean replace(K key, V oldValue, V newValue);
synchronized default V replace(K key, V value);
synchronized default V computeIfAbsent(K key, Function<? super K, ? extends V> mappingFunction);
synchronized default V computeIfPresent(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction);
synchronized default V compute(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction);
synchronized default V merge(K key, V value, BiFunction<? super V, ? super V, ? extends V> remappingFunction);
```