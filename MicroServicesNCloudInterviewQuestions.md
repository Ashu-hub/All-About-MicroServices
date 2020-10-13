# MicroServices Interview Questions

1. What is Microservices Architecture?
	It is architectural pattern, which defines services as small, loosely coupled, distributed service.

2. What are the pros/cons. of the Microservices architecture style?
	Pros: 
		* Improved falut tolerance- Larger application can remian mostly unaffected by the failure of single module.
		* Ease of Understanding -With added simpicity, developer can have better understandibilty of the funtionality.
		* Eliminate Technology lock in- MS provide flexibilty to use different technology stack in different services.
		* Smaller and faster development- smallar and disintegreated codebase = quicker development
		* Scalability- Since your application is small you can scale it seperately, whenever needed.
		
	Cons:
		* Communication between services is complex/overhead-  Since there are more no of services, effiecient Intercommunication is a challenging task.
		* Global Testing is more complicated.
		* Deployment challenges- One need to deploy every service and it becomes challenging sometimes like generating war and deploying it in server.
		
3. How micro services interact with each other?
	We can use RestTemplate or Webclient-builder. 
	But we should not use them as it has boilerplate-code(lot of code needs to write for a small work) to call and get the response.
	Insted we should use Feign client to use for communication.

4. How to maintain logs in microservices architecture
	ans:- By using Correlate Requests With a Unique ID (Sleuth)
	
5. Discuss Eureka:

	Eureka(Netflix) as a service discovery:- 
			It allows automatic detection of network locations for each service instance.
			Eureka is a good example of client side discovery pattern, where the client is reponsible for detemining the location of available services(using a registry servers).and load balancing(by default Ribbon setting) the requests between them,
			@LaodBalanced :- this is default Ribbon Setting in Eureka,used as a marker annotation that defines annotated RestTemplate should use RibbonloadBanlacer Client for interacting with the server
			
	Eureka Server - Add Dependency to the pom..xml called "spring-cloud-starter-netflix-eureka-server" and add eureka.client.fetch-Regisry = false and eureka.client.register-with-eureka =false
	Eureka Client - Add Dependency to the pom..xml called "spring-cloud-starter-netflix-eureka-client" and add eureka.client.service-url.defaultZone =  http://localhost:5001/eureka (Address of Eureka Server)
	Client usually cached the registry server details.
	
	Eureka Client sends HeartBeats singals every 30 Sec to Eureka Server. This is basically Put Requests to Eureka Server with Delta. 
	If There is no HeartBeats till 90 Seconds , then server marks the entry as Ready for Eviction.(Eviction Timer Thread, runs for every 60 sec). 
	So If client does not sends HeartBeats for (90+60)150 sec then server going to Evict this Service form Service Registry.
	
	Self Preservation Mode of Eureka:
	Eureka Server will enter into Self Preservation mode by default if they detect that a larger than expected no of clients have terminated their connections in ungraceful way and pending eviction at the same time
	In Self Preservation mode Eureka will not Evict the clients. You can change this setting in prop file by Eureka.client.enable.self-preservation.mode = false.
	
	**Cluster of Eureka Server:**
	Cluster is set of nodes which behaves as Single Node. Generally Eureka Cluster is deployed zone wise.
	
	How to do Configuration for clusters:-
	- Create 3 diff active. profile in localhost with diff port.
	- Add in prop file -   eureka.client.fetch-Regisry = true and eureka.client.register-with-eureka =false, eureka.client.service-url = {Other two Eureka's address}
	Cluster automatically syncup between them.
	So If a service is register in  e1 Eureka, e2,e3 eureka will have its information too. All the Repicas can be seen in DS REplica section of Eureka UI.
	
	** Lookup for the service**
	- In Controller autowire DiscoveryClient and then get the SereviceInstance and from there get the URI.
	like
```java	
	@Autowired
	private DiscoveryClient discoveryClient;
	
	List<ServiceInstance> serviceInstance = discoveryClient.getInstance("Book-Service");
	URI uri = 	serviceInstance.get(0).getUri();
```	
- 	Difference between @EnableEurekaClient and @EnableDiscoveryClient.
	@EnableDiscoveryClient is generic annotation to discover any Discovery Clients, be  it Eureka or Consul, while @EnableEurekaClient is specific to Eureka
	
## Ribbon:
	What for? Client Side load Balancing
	
	How?
	Steps:
	1. Add dependency spring-cloud-starter-netflix-ribbon
	2. In Controller autowire LoadBalancerCLient
```java
@Autowired
	private LoadBalancerCLient loadBalancerCLient;
	
	ServiceInstance serviceInstance = loadBalancerCLient.choose("Booking-Service");
	URI uri = 	serviceInstance.getUri();
```

	- Rest Template also user Interceptor and based on that it load balanced before sending requests to other services.
		We can use @LoanBalanced in the RestTemplate bean, and use convention "http://"+Book-Service+"/books"
		
	- What if one of the instance that Ribbon is routing is down?
		Ribbon will retry.
	- Configure Retry related props: (If you want this prop for all services, remove Book-Service below)
		Book-Service.ribbon.ReadTimeOut = 2000,
		Book-Service.ribbon.ConnectionTimeOut = 1000
		Book-Service.ribbon.MaxAutoRetiresNextServer = 4
		Book-Service.ribbon.MaxAutoReties = 2
		
# How do you secure your microservices?
https://dzone.com/articles/how-do-you-secure-microservices		