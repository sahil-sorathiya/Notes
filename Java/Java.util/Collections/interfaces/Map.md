## Declared Directly in `Map`

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

### Comparison and Hashing

```java
boolean equals(Object o);
int hashCode();
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

### Construction Methods

```java
static <K, V> Map<K, V> of();
static <K, V> Map<K, V> of(K k1, V v1);
.
.
.
static <K, V> Map<K, V> of(K k1, V v1, K k2, V v2, K k3, V v3, K k4, V v4, K k5, V v5,
                               K k6, V v6, K k7, V v7, K k8, V v8, K k9, V v9, K k10, V v10);

static <K, V> Map<K, V> ofEntries(Entry<? extends K, ? extends V>... entries);
static <K, V> Entry<K, V> entry(K k, V v);
static <K, V> Map<K, V> copyOf(Map<? extends K, ? extends V> map);
```