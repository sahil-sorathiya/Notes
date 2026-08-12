**try-with-resources** is a special form of `try` used when you're working with objects that need to be **closed after use**.

Typical examples:

* Files (`FileInputStream`, `FileReader`, `BufferedReader`)
* Database connections
* Sockets
* Streams
* Anything implementing `AutoCloseable`

### The problem without try-with-resources

```java
BufferedReader br = new BufferedReader(new FileReader("data.txt"));

try {
    String line = br.readLine();
    System.out.println(line);
} finally {
    br.close();
}
```

You have to manually remember to call `close()`.

With try-with-resources:

```java
try (BufferedReader br =
         new BufferedReader(new FileReader("data.txt"))) {

    String line = br.readLine();
    System.out.println(line);
}
```

Java automatically calls:

```java
br.close();
```

when execution leaves the `try` block.

---

## What exactly happens?

This:

```java
try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
    System.out.println(br.readLine());
}
```

is conceptually similar to:

```java
BufferedReader br = new BufferedReader(new FileReader("data.txt"));

try {
    System.out.println(br.readLine());
} finally {
    br.close();
}
```

So **try-with-resources is essentially automatic resource cleanup**.

---

## Why is it better?

Consider:

```java
try {
    // read file
    // something throws exception
} finally {
    br.close();
}
```

There are tricky situations where both the main operation and `close()` can throw exceptions.

Try-with-resources handles this properly using **suppressed exceptions**.

For example:

```java
try (MyResource r = new MyResource()) {
    // ...
}
```

If both:

```text
operation → throws Exception A
close()   → throws Exception B
```

Java keeps **A as the primary exception** and attaches **B as a suppressed exception**.

You can inspect them with:

```java
catch (Exception e) {
    for (Throwable suppressed : e.getSuppressed()) {
        System.out.println(suppressed);
    }
}
```

---

## What qualifies as a resource?

The object must implement `AutoCloseable`:

```java
public interface AutoCloseable {
    void close() throws Exception;
}
```

For example:

```java
class MyResource implements AutoCloseable {

    @Override
    public void close() {
        System.out.println("Closing...");
    }
}
```

Then:

```java
try (MyResource r = new MyResource()) {
    System.out.println("Using resource");
}
```

Output:

```text
Using resource
Closing...
```

---

## Multiple resources

You can open multiple resources:

```java
try (
    FileReader fr = new FileReader("a.txt");
    BufferedReader br = new BufferedReader(fr)
) {
    System.out.println(br.readLine());
}
```

They are closed automatically in **reverse order**:

```text
br.close()
fr.close()
```

This is important: **resources are closed in reverse order of their declaration.**

---

### One more useful feature

Since Java 9, you can use an already-existing effectively-final variable:

```java
BufferedReader br = new BufferedReader(new FileReader("data.txt"));

try (br) {
    System.out.println(br.readLine());
}
```

You don't have to redeclare `br` inside the parentheses.

So the easiest way to remember it is:

> **try-with-resources = `try` + automatic `close()` for `AutoCloseable` resources.**
