## Inherited from Parent (not redeclared here)

```java
// Declared in "Object", Inheriting from "AbstractMap"
public String toString();

// Declared in both "Object" & "Map", Inheriting from "AbstractMap"
boolean equals(Object o);
int hashCode();

// Declared in "Map", Inheriting from "Map"
static <K, V> Map<K, V> ofEntries(Entry<? extends K, ? extends V>... entries);
static <K, V> Entry<K, V> entry(K k, V v);
static <K, V> Map<K, V> of();
static <K, V> Map<K, V> of(K k1, V v1);
static <K, V> Map<K, V> of(K k1, V v1, K k2, V v2); // ...etc upto k10, v10
static <K, V> Map<K, V> copyOf(Map<? extends K, ? extends V> map);

// Declared in "Map", Inheriting from "HashMap"
int size();
boolean isEmpty();
boolean containsKey(Object key);
V put(K key, V value);
V remove(Object key);
void putAll(Map<? extends K, ? extends V> m);
default V putIfAbsent(K key, V value);
default boolean remove(Object key, Object value);
default boolean replace(K key, V oldValue, V newValue);
default V replace(K key, V value);
default V computeIfAbsent(K key, Function<? super K, ? extends V> mappingFunction);
default V computeIfPresent(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction);
default V compute(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction);
default V merge(K key, V value, BiFunction<? super V, ? super V, ? extends V> remappingFunction);

// Declared in "SequencedMap", Inheriting from "SequencedMap"
default Map.Entry<K,V> firstEntry();
default Map.Entry<K,V> lastEntry();
default Map.Entry<K,V> pollFirstEntry();
default Map.Entry<K,V> pollLastEntry();
```

## Constructors

```java
public LinkedHashMap(int initialCapacity, float loadFactor)
public LinkedHashMap(int initialCapacity)
public LinkedHashMap()
public LinkedHashMap(Map<? extends K, ? extends V> m)
public LinkedHashMap(int initialCapacity, float loadFactor, boolean accessOrder)
```



## `SequencedMap` Methods

```java
SequencedMap<K, V> reversed();

default V putFirst(K k, V v);
default V putLast(K k, V v);

default SequencedSet<K> sequencedKeySet();
default SequencedCollection<V> sequencedValues();
default SequencedSet<Map.Entry<K, V>> sequencedEntrySet();
```

## `Map` Methods

### Query Operations

```java
int size();
boolean isEmpty();
boolean containsKey(Object key);
boolean containsValue(Object value);
V get(Object key);
```

### Modification Operations

```java
V put(K key, V value);
V remove(Object key);
```

### Bulk Operations

```java
void putAll(Map<? extends K, ? extends V> m);
void clear();
```

### Views

```java
Set<K> keySet();
Collection<V> values();
Set<Map.Entry<K, V>> entrySet();
```

### Defaultable Methods

```java
default V getOrDefault(Object key, V defaultValue); //
default void forEach(BiConsumer<? super K, ? super V> action); //
default void replaceAll(BiFunction<? super K, ? super V, ? extends V> function); //
```

## Declared Directly in `LinkedHashMap`

```java
public static <K, V> LinkedHashMap<K, V> newLinkedHashMap(int numMappings)
```