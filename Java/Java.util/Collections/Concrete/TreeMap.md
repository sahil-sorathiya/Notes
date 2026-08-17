## Inherited from Parent (not redeclared here)

```java
// Declared in "Object", Inheriting from "AbstractMap"
public String toString();

// Declared in both "Object" & "Map", Inheriting from "AbstractMap"
boolean equals(Object o);
int hashCode();

// Declared in "Map", Inheriting from "Map"
default V getOrDefault(Object key, V defaultValue);
default boolean remove(Object key, Object value);
static <K, V> Map<K, V> ofEntries(Entry<? extends K, ? extends V>... entries);
static <K, V> Entry<K, V> entry(K k, V v);
static <K, V> Map<K, V> of();
static <K, V> Map<K, V> of(K k1, V v1);
static <K, V> Map<K, V> of(K k1, V v1, K k2, V v2); // ...etc upto k10, v10
static <K, V> Map<K, V> copyOf(Map<? extends K, ? extends V> map);

// Declared in both "Map", Inheriting from "AbstractMap"
boolean isEmpty();

// Declared in "SequencedMap", Inheriting from "NavigableMap"
default SequencedMap<K, V> reversed();

// Declared in "SequencedMap", Inheriting from "SequencedMap"
default SequencedSet<K> sequencedKeySet();
default SequencedCollection<V> sequencedValues();
default SequencedSet<Map.Entry<K, V>> sequencedEntrySet();
```

## Constructors

```java
public TreeMap()
public TreeMap(Comparator<? super K> comparator)
public TreeMap(Map<? extends K, ? extends V> m)
public TreeMap(SortedMap<K, ? extends V> m)
```


## `Object` Methods
```java
public Object clone();
```

## `Map` Methods

### Query Operations

```java
int size();
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
default void forEach(BiConsumer<? super K, ? super V> action);
default void replaceAll(BiFunction<? super K, ? super V, ? extends V> function);
default V putIfAbsent(K key, V value);
default boolean replace(K key, V oldValue, V newValue);
default V replace(K key, V value);
default V computeIfAbsent(K key, Function<? super K, ? extends V> mappingFunction);
default V computeIfPresent(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction);
default V compute(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction);
default V merge(K key, V value, BiFunction<? super V, ? super V, ? extends V> remappingFunction);
```

## `SequencedMap` Methods

```java
default Map.Entry<K,V> firstEntry();
default Map.Entry<K,V> lastEntry();

default Map.Entry<K,V> pollFirstEntry();
default Map.Entry<K,V> pollLastEntry();

default V putFirst(K k, V v);
default V putLast(K k, V v);
```

## `SortedMap` Methods

```java
Comparator<? super K> comparator();

SortedMap<K,V> subMap(K fromKey, K toKey);
SortedMap<K,V> headMap(K toKey);
SortedMap<K,V> tailMap(K fromKey);

K firstKey();
K lastKey();
```

## `NavigableMap` Methods

```java
K lowerKey(K key);
K higherKey(K key);
K floorKey(K key);
K ceilingKey(K key);

Map.Entry<K,V> lowerEntry(K key);
Map.Entry<K,V> higherEntry(K key);
Map.Entry<K,V> floorEntry(K key);
Map.Entry<K,V> ceilingEntry(K key);

NavigableMap<K,V> descendingMap();
NavigableSet<K> navigableKeySet();
NavigableSet<K> descendingKeySet();

NavigableMap<K,V> subMap(K fromKey, boolean fromInclusive, K toKey, boolean toInclusive);
NavigableMap<K,V> headMap(K toKey, boolean inclusive);
NavigableMap<K,V> tailMap(K fromKey, boolean inclusive);
```