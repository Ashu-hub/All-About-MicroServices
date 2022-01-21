1. 3 Microservices
2. Intercommunication b/w them
3. Service Registry using Eureka
4. Circuit breaker using Hystrix
4. Sring cloud config for strict seperation of config from code.

	Need to Implement:-
5. Api Gateway(ZUUL) - Single entry point into the system, used to handle request by routing them to appropriate service.		(Reverse proxy??) allow/ block
			ZULL is proxy server provided by Netflix. It provides features such as dynamic routing, filtering requests etc
			In MS Arch, it acts as API gateway. that means all microservices will be exposed to external entity via ZUll proxy,
	Gateway can use a client side load balancer library (Ribbon) to distribute load across instances based on round-robin fashion
			
	Reverse Proxy:-
	It hides the services/servers the rest of the world. It recieves the requests on behalf of Server from the client.
	It is responsibility of reverse proxy to delegate the client requests to the relevant service/server.

	Sometimes there may be case that serveral instance is running for a server behind the reverse proxy which is known as Clustering. In this case the reverse proxy determine the appropriate instance by something called load balancing.	

	Regarding the differences(Api getway and reverse proxy), they are very similar. It's just nomenclature. As you take a basic reverse proxy setup and start bolting on more pieces like authentication, rate limiting, dynamic config updates, and service discovery, 
	people are more likely to call that an API gateway.

- Advantages of API gateway-
	1. Authentication- Instead of having authentication for every services we can have authentication only @ API.
	2. SSL Termination- We can use ALL interrnal calls to use HTTP/Web Socket/rPC etc, no need for HTTPS call from API Gateway to services.
	3. Load Balncce- It can also act as a load balancer to balanced the load between diff service instances.
	4. Insulation- No client can access any service directly. It makes system to be loosely couple.
	
-Disadvantages- 
	1. Increased no of hops -> Increased Latency
	2. System becomes complicated.
	
	
6. Hystrix:- It is implementation of Circuit breaker pattern, which gives you control over latency and failures ouccuring in the appication.
			The main idea is to stop cascading failure in the distributed enviornment in a large number of Microservices application. This helps in fail- fast and recover asap.
			It also provides one to add fallback methods that can be called in case of command failures.	
			
	Feature of Circuit Breaker: Cached Response, Fallback-mechnaism(3rd party microservices), give time to Recover

	Half open state: After a threshold time the circuit breaker will check if the service is up or not. This state is called as half open state.
	
	Configuration:
	How Hystrix Circuit-Breaker operates: Hystrix does not offer a circuit breaker which breaks after a given number of failures. The Hystrix circuit will break if:
	within a timespan of duration metrics.rollingStats.timeInMilliseconds, the percentage of actions resulting in a handled exception exceeds errorThresholdPercentage
	
	hystrix.command.default.circuitBreaker.errorThresholdPercentage=50
	hystrix.command.default.fallback.enabled=true
	hystrix.command.default.circuitBreaker.enabled=true
	metrics.rollingStats.timeInMilliseconds=10000
	
	or
	
	 @HystrixCommand(commandProperties=Array(new HystrixProperty(name="execution.isolation.thread.timeoutInMilliseconds", value="800")))
	 
7. Eureka(Netflix) as a service discovery:- 
	It allows automatic detection of network locations for each service instance. Service Discovery is a pattern to identifythe network address of all instances of services.
	Eureka is a good example of client side discovery pattern, where the client is reponsible for detemining the location of available services(using a registry servers).and load balancing(by default Ribbon setting) the requests between them,
	
	Server Side Discovery Pattern:- When making a request to a service, the client makes a request via a router (a.k.a load balancer) that runs at a well known location. The router queries a service registry, which might be built into the router, and forwards the request to an available service instance.
	example- An AWS Elastic Load Balancer (ELB) is an example of a server-side discovery router. A client makes HTTP(s) requests (or opens TCP connections) to the ELB, which load balances the traffic amongst a set of EC2 instances
	
	@LaodBalanced :- this is default Ribbon Setting in Eureka, used as a marker annotation that defines annotated RestTemplate should use RibbonloadBanlacer Client for interacting with the server
			
8. Cloud config Server:-
		It acts as a centralized configuration service. It supports Git and SVN 
		
6. Feign Client - You have a full suite of a load balancer, circuit breaker, and HTTP client with a sensible ready-to-go default configuration.
7. Ribbon - client side load balancer(Diff bet client and server side load balancer)

8. Inter- Service communication:
	It can be using any protocol like HTTP/HTTPS/rPC/WebSocket.
	It can be synchronous or asynchoronous.
	
	Synchronous Communication:
	Advantages: Simple,Easy, Realtime.
	Disadvantages: Service Availability, respsonse Time(if hop is too many, latency is increased).
	
	Asynchronous Communication:
	Using queues. 
	Advantages: Faster way of communication, loosely coupled, works even service are down, No need of service Discovery.
	Disadvantages: Complex design, Process Latency, Monitoring calls(Increase cost for queues).
	

9. Service Mesh:
	A service mesh is an abstracted layer which sits on top of the microservices and handles service-to-service communication. Service Mesh Implements Proxy and Side Car Pattern, which means for every MicroService Instance there will be a service Mesh Instance.
	The mesh tracks, secures, and relays all data flow between services.
	It helps in :
	a. Load Balancing.
	b. Service Discovery.
	c. Metrics.
	d. Retries.
	e. Circuit Braker.
	f. TimeOut.

	In Service mesh there are 2 major components- 1. Control Pane 2. Data Pane.
	1. Control PAne- It is a central palce where we can acutally load all the configurations or all of the proxies which are side loaded alaong with every instance.
	2. Data Pane: Is a group of all these proxies. The important poitn is every proxy does not have idea about other proxy in the same cluster.
	Proxy are the one which does all of the functions metioned above like load balancing.



10. Deployemnt Patterns:


###How did you do in Jpop Project?

	1.  **Spring Cloud config:-**
		a. Add dependecy - *spring-cloud-config-server* in pom.xml
		b. @EnableConfigServer in SpringbootApplication class.
		c. In prop file add below:-
			spring.cloud.config.server.git.uri = https://github.com/Ashu-hub/configrepo
			server.port = 8888

	2.	**API Gateway ZULL :-**
		a. Add Dependency as :-
			*spring-cloud-starter-netflix-zuul*, *spring-cloud-starter-config*, *spring-cloud-starter-netflix-eureka-client* 
		b. Add below annotation in SpringbootApplication:
			@EnableEurekaClient
			@EnableZuulProxy
		c. Add below in prop file
			server.port = 6060
			spring.application.name = zuul-service
			eureka.client.service-url.default-zone=http://localhost:8761/eureka
			spring.cloud.config.uri= http://localhost:8888

			zuul.prefix = /api
			zuul.routes.LibraryService.path=/LibraryService/**
			zuul.routes.LibraryService.service-id=LIBRARY-SERVICE

			zuul.routes.bookService.path=/bookService/**
			zuul.routes.bookService.service-id=BOOK-SERVICE

			zuul.routes.userService.path=/userService/**
			zuul.routes.userService.service-id=USER-SERVICE
		
	3.  **Eurerka Service Discovery :-**
		a. add dependecy pom xml - *spring-cloud-starter-netflix-eureka-server*
		b. Add @EnableEurekaServer in SpringBoot Application
		c. Add below in prop file
			server.port = 8761
			eureka.client.register-with-eureka = false
			eureka.client.fetch-registry = false
	
	4. **Book Service:**
		a. Add below in pom xml
		 spring-cloud-starter-netflix-eureka-client
		 spring-cloud-starter-config
		 spring-cloud-starter-netflix-hystrix (for @HystrixCommand(fallbackMethod = "defaultGreeting"))
		 
		b. Add below annotation in SpringBootapplication main class
			@EnableEurekaClient
			@EnableCircuitBreaker
		c. Add below in prop file
			spring.application.name = BOOK-SERVICE
			spring.cloud.config.uri= http://localhost:8888
			eureka.client.service-url.default-zone
			server.port= 3030

Q) How to configure service to use Eureka, which is running at port 1001
	eureka.client.service-url.default-zone=http://localhost:1001/eureka

***************************************
# RestTutorial
1. What is the difference between @Controller and @RestController?

2. What does parameter in JpaRepository defines?

3. Why to use ResponseEntity?
	to set the body, status, and headers of an HTTP response 

4. What does @profile do?

5. Feign Client:-
	It is a Service Registry, provided by Netflix which integrate with Spring Framework.
	Purpose:- MicroService Registry and discovery by Spring cloud.

	Fault tolerance:- If there is a falut, how much tolerant does the system is
	Resilience:- How many Falut a system can tolerate before going down. and if it's goes down, how can a system can bounce back from failure state?
	Say 1 MS is slow.
	Solution:-
	TimeOut(partly)

	Best Solution- Circuit breaker
	what does it do:- 
	1)) Detect something is wrong
	2) take temporal steps to avoid the situation:- like not sending requests for a while
	3) Decactivate the faulty componenet so as it does not affect other downstream componenets
	-- this is circuit breaker pattern
	Circuit breaker can be visualize as MCB of a house.

## Curcuit Breaker parameters:- 
	--when does circuit trip?
	-last n req to consider  decision
	- how many of those fail?
	- timeout duration

	--when does circuit un-trip?
	how long after CB before try again

#Fallback- when CB what to do

