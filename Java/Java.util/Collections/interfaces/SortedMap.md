## Inherited from Parent (not redeclared here)

```java
// All methods Declared in "Map", Inheriting from "Map", Except below three
Set<K> keySet();
Collection<V> values();
Set<Map.Entry<K, V>> entrySet();

// All methods Declared in "SequencedMap", Inheriting from "SequencedMap", Except below three
default SortedMap<K, V> reversed();
default V putFirst(K k, V v);
default V putLast(K k, V v);
```

## `Map` Methods

```java
Set<K> keySet();
Collection<V> values();
Set<Map.Entry<K, V>> entrySet();
```

## `SequencedMap` Methods

```java
default SortedMap<K, V> reversed();

default V putFirst(K k, V v);
default V putLast(K k, V v);
```

## Declared Directly in `SortedMap`

```java
Comparator<? super K> comparator();

SortedMap<K,V> subMap(K fromKey, K toKey);
SortedMap<K,V> headMap(K toKey);
SortedMap<K,V> tailMap(K fromKey);

K firstKey();
K lastKey();
```