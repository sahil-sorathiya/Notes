## Inherited from Parent (not redeclared here)

```java
// All Declared in "Map", Inheriting from "Map", Except below three
Set<K> keySet();
Collection<V> values();
Set<Map.Entry<K, V>> entrySet();

// All Declared in "Map", Inheriting from "SortedMap"
Set<K> keySet();
Collection<V> values();
Set<Map.Entry<K, V>> entrySet();

// Declared in "SequencedMap", Inheriting from "SequencedMap"
default SequencedSet<K> sequencedKeySet();
default SequencedCollection<V> sequencedValues();
default SequencedSet<Map.Entry<K, V>> sequencedEntrySet();

// Declared in "SequencedMap", Inheriting from "SortedMap"
default V putFirst(K k, V v);
default V putLast(K k, V v);

// Declared in "SortedMap", Inheriting from "SortedMap"
Comparator<? super K> comparator();
K firstKey();
K lastKey();
```



## `SequencedMap` Methods

```java
default NavigableMap<K, V> reversed();

Map.Entry<K,V> firstEntry();
Map.Entry<K,V> lastEntry();

Map.Entry<K,V> pollFirstEntry();
Map.Entry<K,V> pollLastEntry();
```

## `SortedMap` Methods

```java
SortedMap<K,V> subMap(K fromKey, K toKey);
SortedMap<K,V> headMap(K toKey);
SortedMap<K,V> tailMap(K fromKey);
```

## Declared Directly in `NavigableMap`

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