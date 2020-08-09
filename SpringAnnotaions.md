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
	This is used with field, constructor or method parameter. It signifies the default value experssion for the feild/constructor/parameter.

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

# Spring MVC and Rest Annotations:-
1. @RequestMapping:
	
	

### Ref:- https://springframework.guru/spring-framework-annotations/
		  https://howtodoinjava.com/spring-core/spring-annotations/
		  