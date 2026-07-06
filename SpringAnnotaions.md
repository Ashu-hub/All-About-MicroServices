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
	1. Instantiation
	2. Populate properties
	3. BeanNameAware(setBeanName())
	4. BeanFactoryAware(setBeanFactory())
	5. Pre-Initialization BeanPostProcessor
	6. InitializingBean(after property set)
	7. Custom Init-method
	8. Post-initialization BeanPostProcessor
	9. Bean is ready to use
	10. Destruction
	

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
		  
