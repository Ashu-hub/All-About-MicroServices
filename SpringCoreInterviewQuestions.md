# Spring Core Interview Questions (Top 25)

---

# 1. What is Spring Framework?

### Answer
Spring is a lightweight Java framework that simplifies enterprise application development by providing features like Dependency Injection (DI), Inversion of Control (IoC), Aspect-Oriented Programming (AOP), Transaction Management, Security, and MVC.

### Why was Spring introduced?
- Reduce tight coupling
- Simplify Enterprise Java (EJB) development
- Improve testability and maintainability

---

# 2. What is Inversion of Control (IoC)?

### Answer
IoC is a design principle where the responsibility of creating and managing objects is transferred from the application code to the Spring Container.

Without Spring

```java
Car car = new Car(new Engine());
```

With Spring

```java
@Autowired
private Engine engine;
```

Spring creates and injects the object.

---

# 3. What is Dependency Injection (DI)?

### Answer
Dependency Injection is the process where Spring injects required dependencies into an object instead of the object creating them itself.

### Types

- Constructor Injection ✅ (Preferred)
- Setter Injection
- Field Injection (Avoid)

### Why Constructor Injection?
ex:
```
@Service
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

- Constructor Injection is preferred because it makes dependencies mandatory, creates immutable objects, improves testability, avoids NullPointerException, and follows SOLID principles. It also ensures that an object is always created in a valid state.

- Mandatory dependencies
  ```
  public Car(Engine engine) {
    this.engine = engine;
        }
  ```
  The compiler forces anyone creating a Car to provide an Engine.

  With Setter Injection, someone could forget to set the engine.
  
- Immutable objects
- Easy unit testing
- No reflection
- Prevents NullPointerException

---

# 4. Difference between IoC and DI

| IoC | DI |
|------|----|
| Design Principle | Design Pattern |
| Spring controls object creation | Spring injects dependencies |
| Achieved using Container | Achieved using Constructor/Setter Injection |

---

# 5. Explain Bean Lifecycle

```
Bean Instantiation
        ↓
Dependency Injection
        ↓
@PostConstruct
        ↓
Bean Ready
        ↓
Application Running
        ↓
@PreDestroy
```

### Lifecycle Callbacks

- @PostConstruct
- @PreDestroy
- InitializingBean
- DisposableBean
- initMethod
- destroyMethod

---

# 6. What is a Spring Bean?

A Spring Bean is any Java object managed by the Spring IoC Container.

Example

```java
@Service
public class UserService {

}
```

---

# 7. Bean Scopes

| Scope | Description |
|---------|------------|
| Singleton | One instance per Spring Container |
| Prototype | New instance every request |
| Request | One bean per HTTP request |
| Session | One bean per HTTP session |
| Application | One bean for the application |

---

# 8. Are Singleton Beans Thread Safe?

### Answer

No.

Singleton means only one object exists.

It **does not** guarantee thread safety.

Bad Example

```java
@Service
class CounterService {

    int counter;

}
```

Better

```java
AtomicInteger counter = new AtomicInteger();
```

Best Practice

- Make beans stateless
- Avoid mutable shared variables

---

# 9. What is ApplicationContext?

ApplicationContext is the advanced Spring Container responsible for

- Creating Beans
- Dependency Injection
- Bean Lifecycle
- Event Publishing
- AOP Support
- Resource Loading

---

# 10. BeanFactory vs ApplicationContext

| BeanFactory | ApplicationContext |
|--------------|-------------------|
| Basic Container | Advanced Container |
| Lazy initialization | Singleton beans created eagerly |
| No Event Support | Event Support |
| Limited Features | Full Spring Features |

---

# 11. @Component vs @Service vs @Repository vs @Controller

| Annotation | Usage |
|------------|-------|
| @Component | Generic Bean |
| @Service | Business Layer |
| @Repository | Data Access Layer |
| @Controller | MVC Controller |
| @RestController | REST API |

All are Spring-managed beans.

---

# 12. Explain @Autowired

Automatically injects required dependencies.

Supports

- Constructor Injection
- Setter Injection
- Field Injection

Preferred

```java
@Service
public class UserService {

    private final UserRepository repository;

    public UserService(UserRepository repository) {
        this.repository = repository;
    }

}
```

---

# 13. @Qualifier vs @Primary

### Problem

```java
interface PaymentService
```

Implementations

- PaypalService
- StripeService

Spring throws

```
NoUniqueBeanDefinitionException
```

Solution

```java
@Qualifier("paypalService")
```

or

```java
@Primary
```

---

# 14. What is Circular Dependency?

Example

```
A → B
B → A
```

Constructor Injection causes

```
BeanCurrentlyInCreationException
```

Solutions

- Redesign
- @Lazy
- Setter Injection

---

# 15. What is Component Scanning?

Spring automatically scans packages for

- @Component
- @Service
- @Repository
- @Controller

Enabled using

```java
@ComponentScan
```

---

# 16. @Bean vs @Component

| @Bean | @Component |
|---------|------------|
| Method Level | Class Level |
| Third-party Classes | Own Classes |
| Used inside @Configuration | Auto Scanned |

---

# 17. What is @Configuration?

Marks a Java class as a configuration class.

Example

```java
@Configuration
public class AppConfig {

    @Bean
    public Engine engine() {
        return new Engine();
    }

}
```

---

# 18. What is @Lazy?

Bean is created only when first requested.

Useful for

- Heavy objects
- Circular dependencies
- Performance optimization

---

# 19. How does Autowiring work?

Spring resolves dependency by

1. Type
2. Qualifier
3. Primary Bean

If multiple beans exist

```
NoUniqueBeanDefinitionException
```

---

# 20. Explain Spring Boot Startup Flow

```
main()
      ↓
SpringApplication.run()
      ↓
Create ApplicationContext
      ↓
Component Scan
      ↓
Create Singleton Beans
      ↓
Dependency Injection
      ↓
@PostConstruct
      ↓
Embedded Tomcat Starts
      ↓
Application Ready
```

---

# 21. Bean Creation Order

Spring creates beans based on dependency hierarchy.

Example

```
Engine
   ↓
Car
   ↓
Garage
```

Engine is created first.

---

# 22. What are Spring Stereotype Annotations?

- @Component
- @Service
- @Repository
- @Controller
- @RestController

Used to identify Spring-managed components.

---

# 23. How does Spring use Reflection?

Spring uses Reflection for

- Creating Objects
- Dependency Injection
- Invoking @PostConstruct
- Accessing Private Fields
- Creating Proxies

---

# 24. What are Spring Proxies?

Spring creates proxy objects for features like

- @Transactional
- @Cacheable
- @Async
- Security
- AOP

Types

- JDK Dynamic Proxy
- CGLIB Proxy

---

# 25. Responsibilities of Spring Container

- Creates Beans
- Injects Dependencies
- Manages Bean Lifecycle
- Maintains Bean Scopes
- Reads Configuration
- Publishes Events
- Creates Proxies

---

# Frequently Asked Follow-up Questions

1. Why is Constructor Injection preferred?
2. Why is Field Injection discouraged?
3. Are Singleton Beans Thread Safe?
4. What happens if multiple beans have the same type?
5. Difference between @Qualifier and @Primary?
6. How does @Lazy work internally?
7. Explain Bean Lifecycle.
8. Difference between BeanFactory and ApplicationContext?
9. How does Component Scanning work?
10. Explain Circular Dependency and its solutions.
11. Difference between @Bean and @Component?
12. How does Spring use Reflection?
13. Explain JDK Dynamic Proxy vs CGLIB Proxy.
14. What happens internally during `SpringApplication.run()`?
15. Explain the complete Spring Boot startup lifecycle.

---

# Interview Tips

- Prefer **Constructor Injection** over Field Injection.
- Keep singleton beans **stateless**.
- Use **@Qualifier** when multiple beans of the same type exist.
- Use **@Bean** for third-party classes and **@Component** for your own classes.
- Understand the complete **Bean Lifecycle** and **Spring Boot startup flow**.
- Be able to explain **JDK Proxy vs CGLIB Proxy** with real-world examples.
- Know the internal working of **IoC**, **DI**, **Reflection**, and **AOP**, as these are common senior-level interview topics.
