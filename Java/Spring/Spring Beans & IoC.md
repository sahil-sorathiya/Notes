# Spring IoC (Inversion of Control)

## What is IoC?

Inversion of Control is a design principle where the control of object creation and dependency management is transferred from the application code to a container/framework. Instead of objects creating their own dependencies, the framework *injects* them.

**Traditional approach:** `UserService service = new UserService(new UserRepository());`
**With IoC:** The container creates and wires everything for you.

---

## The Spring IoC Container

The container is responsible for instantiating, configuring, and assembling beans. Two core interfaces:

- **`BeanFactory`** — Basic container, lazy initialization, lightweight
- **`ApplicationContext`** — Extends BeanFactory; adds AOP, event propagation, i18n, eager loading. Preferred in most cases.

Common `ApplicationContext` implementations:

| Implementation | Use Case |
|---|---|
| `ClassPathXmlApplicationContext` | XML config from classpath |
| `AnnotationConfigApplicationContext` | Java-based config |
| `WebApplicationContext` | Web applications |

---

## Dependency Injection (DI) — The Mechanism of IoC

### 1. Constructor Injection ✅ (Recommended)
```java
@Component
public class OrderService {
    private final PaymentService paymentService;

    @Autowired  // optional if single constructor
    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```
- Promotes immutability (`final` fields)
- Dependencies are explicit and required
- Easier to unit test

### 2. Setter Injection
```java
@Component
public class OrderService {
    private PaymentService paymentService;

    @Autowired
    public void setPaymentService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```
- Good for optional dependencies
- Allows reconfiguration after creation

### 3. Field Injection ⚠️ (Avoid in production)
```java
@Component
public class OrderService {
    @Autowired
    private PaymentService paymentService;
}
```
- Concise but hides dependencies
- Hard to unit test (requires reflection)
- Discourages immutability

---

## Beans

A **bean** is any object managed by the Spring IoC container.

### Defining Beans

**Annotation-based:**
```java
@Component       // Generic
@Service         // Service layer
@Repository      // Data/persistence layer
@Controller      // MVC controller
```

**Java config:**
```java
@Configuration
public class AppConfig {
    @Bean
    public PaymentService paymentService() {
        return new PaymentService();
    }
}
```

### Bean Scopes

| Scope | Description |
|---|---|
| `singleton` | One instance per container *(default)* |
| `prototype` | New instance every time requested |
| `request` | One per HTTP request (web) |
| `session` | One per HTTP session (web) |
| `application` | One per `ServletContext` (web) |

```java
@Bean
@Scope("prototype")
public MyBean myBean() { return new MyBean(); }
```

---

## Component Scanning

Spring automatically discovers beans using `@ComponentScan`:

```java
@Configuration
@ComponentScan(basePackages = "com.example")
public class AppConfig { }
```

Or with Spring Boot, `@SpringBootApplication` includes component scanning automatically.

---

## Bean Lifecycle

```
Container starts
    → Bean instantiated
    → Dependencies injected
    → @PostConstruct called
    → Bean ready for use
    → @PreDestroy called (on shutdown)
    → Bean destroyed
```

```java
@Component
public class MyBean {
    @PostConstruct
    public void init() { /* setup logic */ }

    @PreDestroy
    public void destroy() { /* cleanup logic */ }
}
```

---

## Key Annotations Summary

| Annotation | Purpose |
|---|---|
| `@Component` | Mark class as a Spring bean |
| `@Autowired` | Inject a dependency |
| `@Qualifier("name")` | Disambiguate when multiple beans of same type exist |
| `@Primary` | Prefer this bean when multiple candidates exist |
| `@Lazy` | Initialize bean only when first requested |
| `@Value("${prop}")` | Inject a property value |
| `@Configuration` | Mark class as a source of bean definitions |
| `@Bean` | Declare a bean inside a `@Configuration` class |

---

## Important Concepts

**Circular Dependencies** — When Bean A depends on Bean B and B depends on A. Spring can resolve these with setter injection but constructor injection will throw an error. Best practice: redesign to avoid them.

**`@Qualifier` vs `@Primary`** — When multiple beans of the same type exist, use `@Primary` to set a default, or `@Qualifier` at the injection point for explicit selection.

**`ApplicationContext` vs `BeanFactory`** — Prefer `ApplicationContext`; `BeanFactory` is only useful in extremely memory-constrained environments.