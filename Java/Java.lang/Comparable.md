`Comparable` is a Java interface used to define the **natural ordering** of objects of a class.

It is in the `java.lang` package.

```java
public interface Comparable<T> {
    int compareTo(T o);
}
```

## Who implements it?

All wrapper classes of premitive types implements it.

```java
Integer
Character
Boolean
Double
Float
```

also

```java
String
StringBuilder
StringBuffer
```

## Why use it?

When a class implements `Comparable`, Java collections can automatically sort its objects using:

```java
Collections.sort(list);
Arrays.sort(array);
TreeSet
TreeMap
PriorityQueue
```

without requiring a separate `Comparator`.

## compareTo() Contract

```java
obj1.compareTo(obj2)
```

Returns:

* Negative number → `obj1 < obj2`
* Zero → `obj1 == obj2`
* Positive number → `obj1 > obj2`

Example:

```java
Integer a = 10;
Integer b = 20;

System.out.println(a.compareTo(b)); // -1
System.out.println(b.compareTo(a)); // 1
System.out.println(a.compareTo(10)); // 0
```

## Example: Custom Class

```java
class Employee implements Comparable<Employee> {
    int id;
    String name;

    Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public int compareTo(Employee other) {
        return Integer.compare(this.id, other.id);
    }

    @Override
    public String toString() {
        return id + " " + name;
    }
}
```

Usage:

```java
List<Employee> employees = List.of(
    new Employee(3, "Charlie"),
    new Employee(1, "Alice"),
    new Employee(2, "Bob")
);

List<Employee> list = new ArrayList<>(employees);

Collections.sort(list);

System.out.println(list);
```

Output:

```text
1 Alice
2 Bob
3 Charlie
```

## Comparable vs Comparator

| Comparable               | Comparator                  |
| ------------------------ | --------------------------- |
| Defines natural ordering | Defines custom ordering     |
| Implemented inside class | Separate object/lambda      |
| Single ordering          | Multiple orderings possible |
| `compareTo()`            | `compare()`                 |

Comparable:

```java
class Employee implements Comparable<Employee> {
    @Override
    public int compareTo(Employee o) {
        return Integer.compare(id, o.id);
    }
}
```

Comparator:

```java
Comparator<Employee> byName = Comparator.comparing(emp -> emp.name);
```

```java
employees.sort(byName);
```

## Why did PriorityQueue throw an exception?

If you create:

```java
PriorityQueue<Tuple> pq = new PriorityQueue<>();
```

then `Tuple` **must implement `Comparable<Tuple>`**.

Otherwise:

```text
java.lang.ClassCastException
```

occurs when the queue tries to compare elements.

Example:

```java
record Tuple(int node, int dist)
        implements Comparable<Tuple> {

    @Override
    public int compareTo(Tuple other) {
        return Integer.compare(this.dist, other.dist);
    }
}
```

Or provide a comparator:

```java
PriorityQueue<Tuple> pq =
    new PriorityQueue<>(Comparator.comparingInt(Tuple::dist));
```

In modern Java, using a `Comparator` is often preferred because it keeps the record/class free from a fixed ordering.

## When to implement Comparable?

Implement `Comparable` when your class has a clear, natural ordering:

* `Student` by roll number
* `Employee` by employee ID
* `Date` by chronological order
* `Version` by version number

Use `Comparator` when multiple orderings are possible:

* Employee by ID
* Employee by Name
* Employee by Salary
* Employee by Joining Date

A class can implement only one natural ordering (`Comparable`) but can have unlimited `Comparator`s.
