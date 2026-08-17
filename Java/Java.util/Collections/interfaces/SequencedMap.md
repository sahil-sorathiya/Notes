## Inherited from Parent (not redeclared here)

```java
// All methods Declared in "Map", Inheriting from "Map"
```


## Declared Directly in `SequencedMap`

```java
SequencedMap<K, V> reversed();

default Map.Entry<K,V> firstEntry();
default Map.Entry<K,V> lastEntry();

default Map.Entry<K,V> pollFirstEntry();
default Map.Entry<K,V> pollLastEntry();

default V putFirst(K k, V v);
default V putLast(K k, V v);

default SequencedSet<K> sequencedKeySet();
default SequencedCollection<V> sequencedValues();
default SequencedSet<Map.Entry<K, V>> sequencedEntrySet();
```