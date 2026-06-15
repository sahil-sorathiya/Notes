### What it is

`Optional<T>` is a **container class** in `java.util` that holds either one value of type `T`, or nothing. It was introduced in Java 8 to replace the pattern of returning `null` from methods.

```java
import java.util.Optional;
```

---

### Creating an Optional

```java
// 1. With a value — use of()
Optional<String> opt = Optional.of("hello");
// ⚠️ of() throws NullPointerException if you pass null

// 2. Empty — no value inside
Optional<String> empty = Optional.empty();

// 3. Value that might be null — use ofNullable()
Optional<String> maybeNull = Optional.ofNullable(someStringThatCouldBeNull);
// If null  → empty Optional
// If value → Optional with that value
```

---

### Checking presence

```java
Optional<String> opt = Optional.of("hello");

opt.isPresent();  // true  — value exists
opt.isEmpty();    // false — (Java 11+) inverse of isPresent()

Optional<String> empty = Optional.empty();
empty.isPresent(); // false
empty.isEmpty();   // true
```

---

### Extracting the value — all methods

#### `.get()` — raw extract, unsafe
```java
Optional<String> opt = Optional.of("hello");
String val = opt.get(); // "hello"

Optional<String> empty = Optional.empty();
empty.get(); // 💥 NoSuchElementException — never use without isPresent() check
```

---

#### `.orElse()` — fallback value
```java
String val = Optional.of("hello").orElse("default");
// → "hello"

String val = Optional.empty().orElse("default");
// → "default"

// ⚠️ The fallback is ALWAYS evaluated, even if value is present
String val = Optional.of("hello").orElse(expensiveMethod());
// expensiveMethod() runs even though "hello" exists — wasteful
```

---

#### `.orElseGet()` — fallback via lambda (lazy)
```java
String val = Optional.empty().orElseGet(() -> "computed default");
// → "computed default"

// ✅ Fallback only evaluated if Optional is empty
String val = Optional.of("hello").orElseGet(() -> expensiveMethod());
// expensiveMethod() never runs — "hello" is returned immediately
```

**`orElse` vs `orElseGet`:**
```java
// orElse  — always computes fallback (eager)
// orElseGet — only computes fallback if needed (lazy)
// For cheap values: orElse is fine
// For expensive operations (DB call, network): always use orElseGet
```

---

#### `.orElseThrow()` — extract or throw ✅ most common
```java
// Java 10+ — default throws NoSuchElementException
String val = Optional.of("hello").orElseThrow();

// Custom exception — use this in practice
String val = Optional.empty()
                     .orElseThrow(() -> new IllegalArgumentException("Not found"));
// 💥 throws IllegalArgumentException

// Your URL shortener uses this correctly
UrlMapping mapping = urlRepository.findByShortCode(shortCode)
                                  .orElseThrow(() -> new IllegalArgumentException("Short code not found: " + shortCode));
```

---

### Transforming the value — without unwrapping

#### `.map()` — transform if present
```java
Optional<String> name = Optional.of("sahil");
Optional<String> upper = name.map(s -> s.toUpperCase());
// → Optional["SAHIL"]

Optional<String> empty = Optional.empty();
Optional<String> result = empty.map(s -> s.toUpperCase());
// → Optional.empty()  — map is skipped entirely

// Chain with orElseThrow at the end
String url = urlRepository.findByShortCode("abc")
                          .map(UrlMapping::getOriginalUrl)
                          .orElseThrow(() -> new IllegalArgumentException("Not found"));
```

---

#### `.flatMap()` — when your transform also returns an Optional
```java
// Problem: map() would create Optional<Optional<String>>
Optional<String> shortCode = Optional.of("abc123");
Optional<Optional<String>> nested = shortCode.map(code -> urlRepository.findOriginalUrl(code));
// Ugly — nested Optionals

// flatMap() flattens it
Optional<String> url = shortCode.flatMap(code -> urlRepository.findOriginalUrl(code));
// → Optional<String>  clean
```

---

#### `.filter()` — keep value only if condition passes
```java
Optional<Integer> age = Optional.of(17);

Optional<Integer> adult = age.filter(a -> a >= 18);
// → Optional.empty()  — 17 fails the condition

Optional<Integer> age2 = Optional.of(25);
Optional<Integer> adult2 = age2.filter(a -> a >= 18);
// → Optional[25]  — passes the condition

// Practical use
urlRepository.findByShortCode(code)
             .filter(mapping -> mapping.isActive())
             .orElseThrow(() -> new IllegalArgumentException("Link expired"));
```

---

### Consuming the value — side effects

#### `.ifPresent()` — run code if value exists
```java
Optional<String> opt = Optional.of("hello");
opt.ifPresent(val -> System.out.println("Found: " + val));
// prints "Found: hello"

Optional.empty().ifPresent(val -> System.out.println("Found: " + val));
// nothing happens
```

---

#### `.ifPresentOrElse()` — Java 9+ handle both cases
```java
urlRepository.findByShortCode("abc")
             .ifPresentOrElse(
                 mapping -> log.info("Found: {}", mapping.getOriginalUrl()),
                 ()      -> log.warn("Short code not found")
             );
```

---

### Full method reference

| Method | Returns | Empty case | Notes |
|---|---|---|---|
| `Optional.of(val)` | `Optional<T>` | — | NPE if val is null |
| `Optional.ofNullable(val)` | `Optional<T>` | — | Safe for nulls |
| `Optional.empty()` | `Optional<T>` | — | Creates empty |
| `.isPresent()` | `boolean` | `false` | |
| `.isEmpty()` | `boolean` | `true` | Java 11+ |
| `.get()` | `T` | 💥 NoSuchElementException | Avoid |
| `.orElse(default)` | `T` | returns default | Eager evaluation |
| `.orElseGet(() -> fn)` | `T` | calls fn | Lazy evaluation |
| `.orElseThrow(() -> ex)` | `T` | 💥 your exception | Most common |
| `.map(fn)` | `Optional<U>` | empty Optional | Transform value |
| `.flatMap(fn)` | `Optional<U>` | empty Optional | fn returns Optional |
| `.filter(predicate)` | `Optional<T>` | empty Optional | Keep or discard |
| `.ifPresent(fn)` | `void` | nothing | Side effect |
| `.ifPresentOrElse(fn, fn)` | `void` | calls else fn | Java 9+ |

---

### Common mistakes

```java
// ❌ Mistake 1 — using Optional as a null check, same as before
if (opt.isPresent()) {
    String val = opt.get(); // back to old style, defeats the purpose
}
// ✅ Use .map() or .orElseThrow() instead

// ❌ Mistake 2 — Optional as a field in an entity
public class UrlMapping {
    private Optional<String> description; // don't do this
}
// Optional is for return types only, not fields

// ❌ Mistake 3 — Optional as a method parameter
public void process(Optional<String> name) { } // don't do this
// Just use null check or overloading for parameters

// ✅ Optional belongs only on method return types
public Optional<UrlMapping> findByShortCode(String code) { }
```

---

### How it all connects to your code

```java
// resolve() in your UrlShortenerService — uses 3 Optional methods together
return redisService.get(shortCode)          // Optional<String>
       .orElseGet(() -> {                   // if Redis empty, go to DB
           String originalUrl = urlRepository
               .findByShortCode(shortCode)  // Optional<UrlMapping>
               .map(UrlMapping::getOriginalUrl) // Optional<String>
               .orElseThrow(() -> new IllegalArgumentException("Not found"));
           redisService.put(shortCode, originalUrl);
           return originalUrl;
       });
```

Each `Optional` method chains cleanly — no null checks, no if-else ladder, no NullPointerException risk.