1) 3 Microservices
2) Intercommunication b/w them
3) Service Registry using Eureka
4) Circuit breaker using Hystrix
4) Sring cloud config for strict seperation of config from code/

Need to Implement:-
5) Api Gateway(ZUUL) - Single entry point into the system, used to handle request by routing them to appropriate service.		(Reverse proxy??) allow/ block
			ZULL is proxy server provided by Netflix. It provides features such as dynamic routing, filtering requests, server side laod balancing etc
			In MS Arch, it acts as API gateway. that means all microservices will be exposed to external entity via ZUll proxy,
			
Reverse Proxy:-
It hides the server/services the rest of the world.	it recieves the requests on behalf of Server from  the client.
It is responsibility of reverse proxy to delegate the client requests to the relevant service/server.

Sometimes there may be case that serveral instance is running for a server behind the reverse proxy which is known as Clustering.In this case the reverse proxy determine the appropriate instance by something called load balancing.

Regarding the differences, they are very similar. It's just nomenclature. As you take a basic reverse proxy setup and start bolting on more pieces like authentication, rate limiting, dynamic config updates, and service discovery, 
people are more likely to call that an API gateway.

6) Hystrix:- It is implementation of Circuit breaker pattern, which gives you control over latency and failures ouccuring in the appication.
			The main idea is to stop cascading failure in the distributed enviornment in a large number if Microservices application. This helps in fail- fast and recover asap.
			It also provides one to add fallback methods that can be called in case of command failures.
				

7) Eureka(Netflix) as a service discovery:- 
			It allows automatic detection of network locations for each service instance.
			Eureka is a good example of client side discovery pattern, where the client is reponsible for detemining the location of available services(using a registry servers).and load balancing(by default Ribbon setting) the requests between them,
			@LaodBalanced :- this is default Ribbon Setting in Eureka,used as a marker annotation that defines annotated RestTemplate should use RibbonloadBanlacer Client for interacting with the server
			
8) cloud config Server:-
		It acts as a centralized configuration service. It supports Git and SVN 

			
6) Feign Client -   you have a full suite of a load balancer, circuit breaker, and HTTP client with a sensible ready-to-go default configuration.
7) Ribbon - client side load balancer(Diff bet client and server side load balancer)
***************************************
# RestTutorial
1. What is the difference between @Controller and @RestController?

2. What does parameter in JpaRepository defines?

3. Why to use ResponseEntity?
	to set the body, status, and headers of an HTTP response 

4. What does @profile do?

Feign Client:-
It is a Service Registry, provided by Netflix which integrate with Spring Framework.
Purpose:- MicroService Registry and discovery by Spring cloud.

Fault tolerance:- If there is a falut\re, how much tolerant does the system has
Resilience:- How many Falut a system can tolerate before going down. and if its goes down, in hiw can a sysytem can bounce back from toerant state?
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

