# Inversion of Control:-
	[https://www.tutorialspoint.com/spring/spring_overview.htm]
	It means giving the control of **creating and instantiating** the spring beans to the Spring IOC container and the only work the developer does is configuring the beans in the spring xml file.

# Dependency Injection:-

	Dependency Injection (DI) is a design pattern in which the dependencies required by a class are provided from the outside rather than being created by the class itself. In Spring, the IoC container creates the required beans and injects them into dependent classes, promoting loose coupling, easier testing, and better maintainability
	 
	..* Spring helps in the creation of loosely coupled applications because of Dependency Injection. 
	..* In Spring Objects defined their association(dependencies) and do not worry how they will get those dependencies. It is the responsibility of Spring to provide the required dependencies for creating objects. 
	
	For example: 
	Suppose we have an object Employee and it has a dependency on object Address. We would define a bean corresponding to Employee that will define its dependency on object Address.
	When Spring tries to create an Employee object, it will see that Employee has a dependency on Address, so it will first create the Address object (dependent object) and then inject it into the Employee object.
	
	..* Inversion of Control (IoC) and Dependency Injection (DI) are used interchangeably. IoC is achieved through DI. DI is the process of providing the dependencies and IoC is the end result of DI. (Note: DI is not the only way to achieve IoC. There are other ways as well.)
	
	..* By DI, the responsibility of creating objects is shifted from our application code to the Spring container; this phenomenon is called IoC. 
	
	Two Types of DI:-
	1) Constructor Based
	2) Setter Based.
	
	1) Constructor Based:-
	Constructor-based DI is accomplished when the container invokes a class constructor with a number of arguments, each representing a dependency on the other class.
	
	2) Setter Based :-
	Setter-based DI is accomplished by the container calling setter methods on your beans after invoking a no-argument constructor or no-argument static factory method to instantiate your bean.
	
	Constructor-based and Setter-based DI but it is a good rule of thumb to use constructor arguments for mandatory dependencies and setters for optional dependencies.

# Spring IOC Container:
	Spring IOC container is at core of spring framework as it creates the object, wires them, configure them and manages their complete life cycle from creation to destruction.
	org.springframework.beans & org.springframeword.context are two base packages.

	2 interface at the heart of spring container:
	1) Bean factory: provides basic configuration framework and basic funtionality capable of managing any type of objects.
	2) Aplication context: extends Beanfactory and adds more enterprise specific funationality such as intergration with Spring AOP, message resource handling, event publication etc.

	The ApplicationContext interface represents the Spring IoC container and is responsible for instantiating, configuring, and assembling the beans by reading configuration metadata. 
	The most commonly used ApplicationContext implementation that use XML configuration sources are:
	- FileSystemXMLApplicationContext
	- ClassPathApplicationContext
	- WebXMLApplicaitonContext
	In case of Annotation based configuration classes:
	- AnnotationConfigApplicationContext: accepts component classes as input- in particular @Configuration annotation classes.
	- AnnotationConfigWebApplicationContext: 

	Creating an ApplicationContext Instance:
	In most application scenarios, explicit user code is not required to instantiate a Spring IoC container. It is handled by the framework itself during the component scanning at the application startup.
	After creating the ApplicationContext, we can use getBean() to retrieve instances of the beans. Ideally, we should not getBean() at all, rather use @Autowired annotation for bean autowiring.

# Spring AOP:
		AOP facilitates the development process by segregating Cross-Cutting Concerns into Aspects, achieving a distinct sepration of modules. 
		It's and interceptor to intercept some processes, for example when a method is executed Spring AOP can hijak the executing method and add extra funtionalitu before and after the method execution.
		For example : Logging, Transaction, cahing, security etc

# Beans:
	Beans are objects that are managed by Spring Ioc Container.

	These beans are created with the configuration metadata that we supply to the container. 
	Following are 3 way to provide configuration metadata to the spring container:
	1. XML based Configuration file
	2. Annotation based Configuration
	3. Java based configuration

# Bean scopes:
	When defining a bean you have the option of declaring a scope for that bean. For example, to force Spring to produce a new bean instance wach time one is needed , you should declare the bean as PROTOTYPE. Similarly if you want Spring to return the same bean instance each time one is needed, you should declare the bean's scope as Singleton
	- Singleton:
	This scopes the bean defination to a single instance perSpring IOC container(default)
	- Prototype:
	This scopes a single bean defination to have any number of object instances
	-Request:
	This scopes a bean defination to an HTTP request. Only valid for web aware Srping Application context
	- Session
	this scopes a bean defination to a HTTP session.
	-Global session:
	This scopes a bean to a global HTTP session.
	-Applicaiton:
	This scopes a bean to a entire web application.
	
	
# Bean Life cycle:
	Application Starts
	        │
	        ▼
	Instantiate Bean
	        │
	        ▼
	Populate Dependencies (Dependency Injection)
	        │
	        ▼
	Aware Interfaces (Optional)
	        │
	        ▼
	BeanPostProcessor (Before Initialization)
	        │
	        ▼
	@PostConstruct / afterPropertiesSet() / initMethod()
	        │
	        ▼
	BeanPostProcessor (After Initialization)
	        │
	        ▼
	Bean Ready for Use
	        │
	        ▼
	Application Running
	        │
	        ▼
	@PreDestroy / destroy() / destroyMethod()
	        │
	        ▼
	Bean Destroyed
-- -----------------------------------------
## Transaction propagation:
	
`@Transactional` **Propagation** defines **what should happen if a method annotated with `@Transactional` is called when another transaction already exists**.

This is one of the most common Spring interview topics.

---

# Simple Definition

Imagine two methods:

```java
methodA()
    └── methodB()
```

Both are annotated with `@Transactional`.

The question is:

> **When `methodB()` is called, should it use the existing transaction or create a new one?**

The answer depends on the **Propagation** level.

---

# Real-world Analogy

Think of a bank transfer.

You transfer ₹10,000.

During the transfer:

* Debit account
* Credit another account
* Write an audit log
* Send notification

Now suppose sending the notification fails.

Should the money transfer also fail?

Different propagation levels answer questions like this.

---

# 1. REQUIRED (Default)

```java
@Transactional(propagation = Propagation.REQUIRED)
```

### Meaning

> **Join the existing transaction. If none exists, create one.**

Example:

```java
@Transactional
public void transferMoney() {
    debit();
    credit();
}
```

```java
@Transactional
public void debit() {
}
```

Flow:

```
transferMoney()
    Transaction T1 starts

        ↓

debit()

Uses Transaction T1
```

Only **one transaction** exists.

---

### If debit() throws exception?

```
transferMoney()

    T1

    debit()

Exception
```

Entire transaction rolls back.

```
Debit ❌

Credit ❌
```

Everything is rolled back.

This is why REQUIRED is the default.

---

# 2. REQUIRES_NEW

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
```

### Meaning

> Suspend the current transaction and create a completely new one.

Example

```java
@Transactional
public void transferMoney() {

    debit();

    auditLog();
}
```

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void auditLog() {
}
```

Flow

```
Transfer()

Transaction T1

    ↓

Audit()

Suspend T1

Create T2
```

```
T1
|
|---paused
|
T2
|
|---commit
|
resume T1
```

---

### Why use this?

Suppose:

```
Transfer Money
```

fails.

But you still want the audit log saved.

```
Transfer()

    T1

Audit()

    T2

T2 commits

T1 rolls back
```

Result

```
Money Transfer ❌

Audit Log ✅
```

Very common for:

* Audit logging
* Error logging
* Notification history

---

# 3. SUPPORTS

```java
@Transactional(propagation = Propagation.SUPPORTS)
```

### Meaning

> If a transaction exists, join it. Otherwise, run **without** a transaction.

Example

```
Transaction exists

↓

Join it
```

No transaction

↓

```
Execute normally
```

Useful for read-only methods that don't require a transaction but can participate in one if available.

---

# 4. NOT_SUPPORTED

```java
@Transactional(propagation = Propagation.NOT_SUPPORTED)
```

### Meaning

> Suspend any existing transaction and execute without a transaction.

Example

```
T1

↓

pause T1

↓

run without transaction

↓

resume T1
```

Useful for operations that should never be part of a transaction, such as long-running reporting queries or external API calls.

---

# 5. MANDATORY

```java
@Transactional(propagation = Propagation.MANDATORY)
```

### Meaning

> A transaction **must already exist**.

If no transaction exists,

Spring throws:

```
IllegalTransactionStateException
```

Example

```
Existing transaction

↓

OK
```

No transaction

↓

```
Exception
```

Used when a method should only ever be called as part of a larger business transaction.

---

# 6. NEVER

```java
@Transactional(propagation = Propagation.NEVER)
```

### Meaning

> There **must NOT** be an existing transaction.

If one exists,

Spring throws an exception.

```
Transaction exists

↓

Exception
```

Useful when you explicitly don't want transactional behavior, perhaps because the operation interacts with a system that doesn't support transactions.

---

# 7. NESTED

```java
@Transactional(propagation = Propagation.NESTED)
```

### Meaning

> Execute inside the existing transaction but create a **savepoint**.

Unlike `REQUIRES_NEW`, it does **not** create a separate physical transaction.

Example

```
Transaction T1

↓

Savepoint

↓

Nested Method
```

If nested method fails

```
Rollback

↓

Back to Savepoint

↓

Continue T1
```

---

Example

```
Transfer()

    Debit()

    Credit()

    Bonus()
```

```
Bonus()

fails
```

Rollback only to the savepoint.

```
Debit ✅

Credit ✅

Bonus ❌
```

Finally

```
Commit T1
```

**Note:** `NESTED` depends on your transaction manager and database supporting savepoints (commonly with JDBC).

---

# Visual Summary

```
Existing Transaction = T1
```

| Propagation   | Behavior                                     |
| ------------- | -------------------------------------------- |
| REQUIRED      | Join T1 or create new                        |
| REQUIRES_NEW  | Suspend T1 and create T2                     |
| SUPPORTS      | Join T1 if present; otherwise no transaction |
| NOT_SUPPORTED | Suspend T1 and run without transaction       |
| MANDATORY     | Must have T1, otherwise exception            |
| NEVER         | Must not have T1, otherwise exception        |
| NESTED        | Use T1 with a savepoint                      |

---


# Which ones are used most in real projects?

For most enterprise Spring Boot applications, you'll primarily see:

1. **`REQUIRED` (90%+)** – Default choice for business services.
2. **`REQUIRES_NEW`** – Audit logs, notifications, error logging, or independent work that should commit even if the main transaction fails.
3. **`SUPPORTS`** – Read-only service methods that can optionally participate in a transaction.

The remaining propagation types (`MANDATORY`, `NOT_SUPPORTED`, `NEVER`, `NESTED`) are much less common and are typically used only for specific architectural or infrastructure requirements.

---

## Interview Answer (30 seconds)

> **Transaction propagation in Spring defines how a transactional method behaves when it is invoked by another transactional method. It determines whether the method joins the existing transaction, creates a new one, executes without a transaction, or throws an exception based on the presence of an existing transaction. The most commonly used propagation is `REQUIRED`, which joins an existing transaction if one exists or creates a new transaction otherwise.**

-- -----------------------

# What is N+1 problem:
	The N+1 problem occurs when Hibernate first executes one query to fetch a list of parent entities and then executes an additional query for each parent entity to fetch its related data, typically because of lazy loading. For example, fetching 100 employees and then accessing each employee's department can result in 101 SQL queries. This causes unnecessary database round trips and poor performance. We can solve it using JOIN FETCH, @EntityGraph, DTO projections, or Hibernate batch fetching to reduce the number of queries.
	
		Imagine you're a teacher.

	You ask the office:

	"Give me all 100 students."

	The office gives you the list.

	Now for each student, you ask:

	"Give me this student's marks."

	So you make:

	1 request to get students
	100 more requests to get marks

	Total:

	1 + 100 = 101 requests

	Instead, you could have asked:

	"Give me all students with their marks."

## JOIN FETCH:
	The JOIN FETCH in your query helps the hibernate N+1 problem by ensuring that all required data is fetched in a single query instead of multiple queries.
	eg:
	@Query("SELECT p from Purchase p JOIN FETCH p.material WHERE p.id = :id")
	Purchase findPurchaseWithMaterial(@Param("id") Long id);

	Generated query:
	Select p.*, m** FROM Purchase p JOIN Material m on p.id = m.purchase_id where p.id = ?;

## @EntityGraph
	@EntityGraph(attributePaths = {"materials"})
	@Query("SELECT p from Purchase p WHERE p.id = :id")
	Purchase findPurchaseWithMaterial(@Param("id") Long id);

	@EntityGraph optimizes fetching by forcing Hibernate to fetch entity in a single query. Instead of trigerring multiple lazy loading queries(N+1) problems.


# How would you reduce latency in a GET API returning large transaction history for a dashboard without pagination?
	A GET API returns a customer's transaction history for a dashboard. It is slow because the customer has millions of transactions. How would you reduce latency?

	| Layer          | Optimization                                  |
	| -------------- | --------------------------------------------- |
	| API            | Database pagination, cursor pagination        |
	| Database       | Composite indexes, query tuning, partitioning |
	| ORM            | DTO projections, avoid N+1 queries            |
	| Cache          | Redis for frequently requested pages          |
	| Network        | Response compression                          |
	| Infrastructure | Proper connection pool sizing and monitoring  |

	"To reduce latency, I'd first ensure that pagination is implemented in the database rather than in memory. For large datasets, I'd prefer keyset (cursor) pagination over offset pagination because it scales much better. I'd create a composite index on (customer_id, transaction_date) to support the filter and sort efficiently. Instead of returning full entities, I'd use DTO projections to fetch only the required columns. I'd review the query execution plan using EXPLAIN ANALYZE and optimize indexes if needed. For frequently accessed pages, such as the most recent transactions, I'd introduce Redis caching. I'd also avoid N+1 query issues by using fetch joins or projections where appropriate, enable GZIP compression for large responses, and tune the database connection pool. For very large transaction tables, I'd consider partitioning the data to reduce scan times."

# Offset vs Keyset?
	Offset: GET /transactions?page=2&size=20
	KeySet: 
	GET /transactions?cursor=496
	Response: 	{
	  "transactions": [
	    ...
	  ],
	  "nextCursor": 476
	}
	The client sends 476 in the next request

	| Feature                     | Offset Pagination             | Keyset (Cursor) Pagination                     |
	| --------------------------- | ----------------------------- | ---------------------------------------------- |
	| Performance                 | Slower as offset increases    | Nearly constant                                |
	| Large datasets              | Poor                          | Excellent                                      |
	| Index usage                 | Less efficient for deep pages | Very efficient                                 |
	| Stable with inserts/deletes | No                            | Yes                                            |
	| Jump to page 100            | Yes                           | No (must follow cursors)                       |
	| Best use case               | Admin/reporting UIs           | Feeds, transaction history, infinite scrolling |

	Offset pagination uses LIMIT and OFFSET to skip a specific number of rows before returning the next page. It's simple and allows users to jump to any page, but it becomes slower as the offset grows and can produce duplicate or missing records when data changes.

	Keyset (cursor) pagination uses the last record's unique, ordered value (such as id or transaction_date) as a cursor. Instead of skipping rows, it fetches records after or before that cursor, making it much faster and more consistent for large, frequently updated datasets. That's why cursor pagination is commonly used for transaction histories, activity logs, and social media feeds.

# Bean vs Java Object:
	Every bean is a Java object but every object is not a bean.
## Ways to create a bean:
	1. Stereotype Annotations (Automatic) - @Component, @Service, @Repository, or @Controller 
	2. @Bean Annotation inside @Configuration (Manual) : You explicitly write a method that creates the object and tell Spring to manage the returned instance.
	3.  XML Configuration (Legacy)
	
# Spring Core Framework Annotations:-
1.	@Required:-
	This is applied on bean setter method. This enforces that affected bean must be populated at configuration time.

2. @Autowired:-
	This can be applied on Feild, Setter Method or constructor. It Injects object dependency implicitly.

3.	@Qualifier:-
	This annotation is used with @Annotation. When you want more control on dependency injection.
	[More On..](https://springframework.guru/spring-framework-annotations/)

4. @Configuration:-
	This is used on class which defined the beans. It is analog to XML Configuration file.
	
5. @ComponentScan:-
	This annotation allows the spring to know the packages to scan. It by default scans base Package in which @ComponentScan class is defined.
	One can use *basePackages* attribute to define different packages other than base package.

6.	@Bean:-
	@Bean vs @Component:-
	Both the annotations aims to register target type in Spring Container. 
	Diff is @Bean is applicable to methods while @Component is applicable to types(like Classes)
		
7. 	@Lazy:-
	This is used on Component classes. BY Default all autowired dependencies are created  and configured startup, but if you to initialize bean lazyly, you can use this.

8. @Value:-
	//This is used with field, constructor or method parameter. It signifies the default value experssion for the feild/constructor/parameter.
	This is used to read the appplication property.
	like - @Value("${spring.application.name}")
			OR
	give default values as :
	@Value("${spring.application.name:defaultName}")

# Spring Framework Stereotype Annotations
1. @Component:-
	It register the target type in spring Container. Used for Classes.

2. @Controller:
	This annotations is used to indicate Spring Container that this the class is Spring Controller Class.
	It allow Auto-detection of component classes in the classpath and auto regitering bean defination for them.
	This class is capable for handling multiple request mapping.

3. @Service:
	This marks a class that performs a service. It is used in Service layer
	
4. @Repository:
	This annotations used in the class which directly access the database. It is used as marker that fullfill the role of Repository.
	This annotations ha automatic translatioin feature. Eg: when an exception occured in the @Repository Class, there is a handler for that exception. no need for Try catch block.

# Spring Boot Annotations:-

1. @EnableAutoConfiguration:-
	It Automatically configures your spring application based on the dependencies provides in the class path

2.	@SpringBootApplication
	It is entry point of Spring Boot Application. It actually scans the whole package and sub packages.
	It is combination of below 3 annotations:-
	@ComponentScan
	@EnableAutoConfiguration
	@Configuration


3. [@ResponseBody:](https://www.baeldung.com/spring-request-response-body)
	The @ResponseBody annotation tells a controller that the object returned is automatically serialized into JSON and passed back into the HttpResponse object.

4. @RestController - 
	Class Level Annotation. Makes a class Rest Controller. 
	@RestController = @Controller + @ResponseBody
	
	
	
# Spring MVC and Rest Annotations:-
1. @RequestMapping:
		
- **Differenc between @PathVariable and @RequestParam**
	
	**@RequestParam** -
	1.	It is used to extract query parameters.
		example -  http://localhost:8080/eportal/trades?tradeId=2001
		
		@RequestMapping("/trades")
		public String showTradeDetails(@RequestParam String tradeId,
                               Model model){
							   ...
		}
	2. Secondly, the query parameters are used to sort/filter resources. 
	
	**@PathVariable **- 
	1.It is used to extract values from URI
	example - http://localhost:8080/book/9783827319333
	
	RequestMapping(value="/book/{ISBN}", method= RequestMethod.GET)
	public String showBookDetails(@PathVariable("ISBN") String id,
                              Model model){
							  ..
							  }
	
	
	
	2. path parameters are used to identify a specific resource or resources.
### Ref:- https://springframework.guru/spring-framework-annotations/
		  https://howtodoinjava.com/spring-core/spring-annotations/
		  
