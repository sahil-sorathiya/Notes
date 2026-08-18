`Comparator` is an interface used to define a **custom ordering** for objects.

It is in the `java.util` package.

```java
public interface Comparator<T> {
    int compare(T o1, T o2);
}
```

Unlike `Comparable`, the comparison logic is **outside the class**.

---

## Why do we need Comparator?

Suppose you have:

```java
class Employee {
    int id;
    String name;
    int salary;

    Employee(int id, String name, int salary) {
        this.id = id;
        this.name = name;
        this.salary = salary;
    }
}
```

You might want to sort employees:

* By ID
* By Name
* By Salary

A class can have only **one** `compareTo()` implementation, but it can have **many** `Comparator`s.

---

## compare() Contract

```java
compare(a, b)
```

Returns:

* Negative → `a < b`
* Zero → `a == b`
* Positive → `a > b`

Example:

```java
Comparator<Integer> c =
    (a, b) -> Integer.compare(a, b);

System.out.println(c.compare(10, 20)); // negative
```

---

## Example: Sort by Salary

```java
Comparator<Employee> bySalary =
    (e1, e2) -> Integer.compare(e1.salary, e2.salary);
```

Usage:

```java
employees.sort(bySalary);
```

or

```java
Collections.sort(employees, bySalary);
```

---

## Using Comparator.comparing

Modern Java provides factory methods.

Sort by name:

```java
Comparator<Employee> byName =
    Comparator.comparing(emp -> emp.name);
```

or

```java
Comparator<Employee> byName =
    Comparator.comparing(Employee::getName);
```

(if getter exists)

---

## Comparing Primitive Fields

Avoid:

```java
(e1, e2) -> e1.salary - e2.salary
```

because of integer overflow.

Prefer:

```java
Comparator<Employee> bySalary =
    Comparator.comparingInt(emp -> emp.salary);
```

or

```java
(e1, e2) -> Integer.compare(e1.salary, e2.salary)
```

---

## Reverse Order

Ascending:

```java
Comparator<Employee> bySalary =
    Comparator.comparingInt(emp -> emp.salary);
```

Descending:

```java
Comparator<Employee> bySalaryDesc =
    Comparator.comparingInt((Employee emp) -> emp.salary)
              .reversed();
```

---

## Multiple Fields

Sort by salary, then by name:

```java
Comparator<Employee> cmp =
    Comparator.comparingInt((Employee e) -> e.salary)
              .thenComparing(e -> e.name);
```

Example:

```text
Salary  Name
50000   Alice
50000   Bob
70000   Charlie
```

---

## PriorityQueue Example

Min-heap by distance:

```java
record Node(int vertex, int dist) {}
```

```java
PriorityQueue<Node> pq =
    new PriorityQueue<>(
        Comparator.comparingInt(Node::dist)
    );
```

Equivalent to:

```java
PriorityQueue<Node> pq =
    new PriorityQueue<>(
        (a, b) -> Integer.compare(a.dist(), b.dist())
    );
```

Max-heap:

```java
PriorityQueue<Node> pq =
    new PriorityQueue<>(
        Comparator.comparingInt(Node::dist)
                  .reversed()
    );
```

---

## TreeSet Example

```java
TreeSet<Employee> set =
    new TreeSet<>(
        Comparator.comparingInt(emp -> emp.id)
    );
```

The comparator determines both:

* ordering
* duplicate detection

If:

```java
compare(a, b) == 0
```

then `TreeSet` treats them as the same element.

---

## Common Utility Methods

### Natural order

```java
Comparator<Integer> c =
    Comparator.naturalOrder();
```

### Reverse order

```java
Comparator<Integer> c =
    Comparator.reverseOrder();
```

### Handle nulls

```java
Comparator<String> c =
    Comparator.nullsFirst(
        Comparator.naturalOrder()
    );
```

```java
Comparator<String> c =
    Comparator.nullsLast(
        Comparator.naturalOrder()
    );
```

---

## Comparable vs Comparator

| Feature                                         | Comparable    | Comparator      |
| ----------------------------------------------- | ------------- | --------------- |
| Package                                         | `java.lang`   | `java.util`     |
| Method                                          | `compareTo()` | `compare()`     |
| Defined in                                      | Class itself  | Separate object |
| Natural ordering                                | Yes           | No              |
| Multiple orderings                              | No            | Yes             |
| Used by `Collections.sort()` without extra args | Yes           | No              |
| Lambda friendly                                 | No            | Yes             |

### Comparable

```java
class Employee implements Comparable<Employee> {
    @Override
    public int compareTo(Employee o) {
        return Integer.compare(id, o.id);
    }
}
```

### Comparator

```java
Comparator<Employee> bySalary =
    Comparator.comparingInt(Employee::salary);
```

### Rule of Thumb

* Use **Comparable** when there is a single obvious natural order.
* Use **Comparator** when you need alternative sorting criteria.
* In modern Java code, `Comparator.comparing(...)`, `comparingInt(...)`, `thenComparing(...)`, and `reversed()` are used very frequently.
