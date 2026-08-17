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
```

## Constructors

```java
public HashMap(int initialCapacity, float loadFactor)
public HashMap(int initialCapacity)
public HashMap()
public HashMap(Map<? extends K, ? extends V> m)
```

## `Object` Methods
```java
public Object clone();
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
default V getOrDefault(Object key, V defaultValue);
default void forEach(BiConsumer<? super K, ? super V> action);
default void replaceAll(BiFunction<? super K, ? super V, ? extends V> function);
default V putIfAbsent(K key, V value);
default boolean remove(Object key, Object value);
default boolean replace(K key, V oldValue, V newValue);
default V replace(K key, V value);
default V computeIfAbsent(K key, Function<? super K, ? extends V> mappingFunction);
default V computeIfPresent(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction);
default V compute(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction);
default V merge(K key, V value, BiFunction<? super V, ? super V, ? extends V> remappingFunction);

```

## Declared Directly in `WeakHashMap`

```java
public static <K, V> HashMap<K, V> newHashMap(int numMappings);
```