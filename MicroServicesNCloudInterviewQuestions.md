# MicroServices Interview Questions

## 1. What is Microservices Architecture?
	It is architectural pattern, which defines services as small, loosely coupled, distributed service.

##2. What are the pros/cons. of the Microservices architecture style?
	
	Pros: 
		* Loose Coupling - Autonomous - the ability to deploy different services independently, and reliability, due to the ability for a service to run even if another service is down.*
		* Improved falut tolerance- Larger application can remian mostly unaffected by the failure of single module.
		* Ease of Understanding -With added simpicity, developer can have better understandibilty of the funtionality.
		* Eliminate Technology lock in- MS provide flexibilty to use different technology stack in different services.
		* Smaller and faster development- smallar and disintegreated codebase = quicker development
		* Scalability- Since your application is small you can scale it seperately, whenever needed.

	Cons:
		* Communication between services is complex/overhead-  Since there are more no of services, effiecient Intercommunication is a challenging task.
		* Global Testing is more complicated.
		* Deployment challenges- One need to deploy every service and it becomes challenging sometimes like generating war and deploying it in server.
	
2.1. Difference between SOA and microservices?
	
	MicroServices:- 
	1. Designed to host services which can function independently.
	2. 	Fine-grained services
	3. 	Each service can have an independent data storage
	4. 	Communicates through an API layer
	5. 	Uses REST and JMS
	6. 	Better for smaller and web-based applications
	
	SOA - 
	1. Designed to share resources across services
	2. Larger, more modular services
	3. Involves sharing data storage between services
	4. Communicates through an ESB
	5. Uses protocols like SOAP and AMQP
	6. Better for large scale integrations

## 2.2. SOA is best suited for?
		SOA is better suited for **large and complex business application** environments that require integration with many heterogeneous application.
	
## 2.3. Domain Driven Design (DDD) is a modelling technique for organized decomposition of complex problem domains.
	DDD technique can be used while partitioning a monolith application into microservices architecture.
	Bounded Context is a central pattern in Domain Driven Design. In Bounded Context, everything related to the domain is visible within context internally but opaque to other bounded contexts.
	
	- Domain Driven Design -
	 1. Identify Problem Space or Business Domain eg:- Credit Card Management
	 2.	Identify Subdomains. - eg - Billing, products, Claims
	 3. Bounded Context:- Design Solution to our identified Business DOmains. It is decided by the Cohesiveness betw your B domains and betw your sub domains.
		For Each Sub domain there is a bOunded Context
	Simple Defination:
	A bounded context is a clearly defined area of responsibility where specific business rules and meanings apply.
	A microservice is a small independent application that is usually built around one bounded context.
		Imagine a School
		A school has different departments:
		Library
		Accounts Office
		Sports Department
		Examination Department
		Each department has its own job and own information.
		For example:
		The Library cares about books, borrowing, and returns.
		The Accounts Office cares about fees and payments.
		The Sports Department cares about teams and tournaments.
		Even though all departments belong to the same school, they don't manage each other's work.
		What is a Bounded Context?
		A bounded context is like one department in the school.
	
## 3. How micro services interact with each other?
	We can use RestTemplate or Webclient-builder. 
	But we should not use them as it has boilerplate-code(lot of code needs to write for a small work) to call and get the response.
	Insted we should use Feign client to use for communication.

### 3.1	Isn’t in process communication in monolithic application faster than tons of remote network calls in microservices architecture?
	Network communication brings some sort of latency to api calls, but that’s not a hurdle for developing a scalable distributed system anymore. VPC (Virtual Private Cloud) can now have inhouse Optical Fibre network with speed of petabyte per second supporting millions of API calls per second between two microservices.

##  **3.2 What is difference between small-services and microservices?**
	Microservices are usually small but not all small services are microservices. If any service is not following **Bounded Context Principle, Single Responsibility Principle, ** etc. then it is not a microservice irrespective of its size. So the size is not the only eligibility criteria for a service to become microservice.
	

##  4. How to maintain logs in microservices architecture
	ans:- By using Correlate Requests With a Unique ID (Sleuth)
	
##  5. Discuss Eureka:

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
		
		
# How to achieve zero-downtime during the deployments?		
	Blue-green deployment
	One way of achieving this is blue/green deployment. In this approach, two versions of a single microservice are deployed at a time. But only one version is taking real requests. 
	Once the newer version is tested to the required satisfaction level, you can switch from older version to newer version.
	You can run a smoke-test suite to verify that the functionality is running correctly in the newly deployed version. 
	Based on the results of smoke-test, newer version can be released to become the live version.

## How do you secure your microservices?
	https://dzone.com/articles/how-do-you-secure-microservices		
		Securing microservices requires a defense-in-depth approach because each service becomes a potential attack surface.

	1. Secure Service-to-Service Communication
	Use TLS/mTLS (Mutual TLS) between services.
	Encrypt all traffic in transit.
	Verify both client and server identities.
	Common tools:
	Istio
	Linkerd
	Consul
	2. Centralized Authentication & Authorization
	Authenticate users via:
	OAuth 2.0
	OpenID Connect (OIDC)
	SAML (enterprise environments)
	Issue short-lived JWT access tokens.
	Each service should validate tokens independently.
	Implement:
	Role-Based Access Control (RBAC)
	Attribute-Based Access Control (ABAC) for finer permissions
	3. API Gateway Security

	Place an API gateway in front of external-facing services.

	Responsibilities:

	Authentication
	Rate limiting
	Request validation
	Logging
	DDoS protection

	Examples:

	Kong
	NGINX
	Apigee
	4. Apply Zero Trust Principles

	Assume:

	No service is automatically trusted.
	Internal networks are not safe by default.

	Every request should be:

	Authenticated
	Authorized
	Audited
	5. Protect Secrets

	Never hardcode:

	Passwords
	API keys
	Certificates
	Database credentials

	Use secret managers:

	HashiCorp Vault
	AWS Secrets Manager
	Azure Key Vault
	6. Secure Containers and Kubernetes

	If using containers:

	Use minimal base images.
	Scan images for vulnerabilities.
	Run containers as non-root.
	Enforce pod security policies.
	Limit container capabilities.
	Keep images patched.

	Useful scanners:

	Trivy
	Snyk
	7. Network Segmentation

	Restrict communication:

	Service A should only talk to required services.
	Use network policies/firewall rules.
	Deny-by-default networking.

	In Kubernetes:

	Use Network Policies.
	Separate environments (dev/test/prod).
	8. Validate All Inputs

	Protect against:

	SQL injection
	NoSQL injection
	Command injection
	Deserialization attacks
	Cross-site scripting (for frontend-facing APIs)

	Best practices:

	Input validation
	Parameterized queries
	Schema validation
	9. Logging, Monitoring, and Auditing

	Collect:

	Authentication events
	Authorization failures
	API requests
	Configuration changes

	Monitor with:

	Prometheus
	Grafana
	Elastic Stack
	10. Rate Limiting and Abuse Protection

	Implement:

	Per-user limits
	Per-IP limits
	API quotas
	Bot detection

	This helps prevent:

	Credential stuffing
	Brute-force attacks
	Resource exhaustion

# Difference bet TLS and mTLS?
 	Think of TLS and mTLS like entering a secure office building.

	What is TLS?

	Imagine you call your bank.

	You ask, "Are you really the bank?"
	The bank shows its official ID.
	You verify it.
	Now you talk over a private line that no one else can hear.

	That's exactly what TLS (Transport Layer Security) does.

	In microservices

	Suppose you have:

	Order Service  ----->  Payment Service

	Without TLS:

	Order Service ----(plain text)----> Payment Service

	Someone on the network could potentially read:

	Credit card number
	Customer details
	JWT token
	Passwords

	With TLS:

	Order Service ====(encrypted)===> Payment Service

	Even if someone intercepts the traffic, it appears as unreadable encrypted data.

	What is mTLS (Mutual TLS)?

	TLS only verifies the server.

	With mTLS, both sides verify each other.

	Imagine entering a secure office.

	TLS

	You ask the receptionist:

	"Are you really from Company ABC?"

	The receptionist shows an ID card.

	You trust them and enter.

	But the receptionist never checks who you are.

	mTLS

	Now both sides verify identity.

	You show your employee badge.

	The receptionist also shows their badge.

	Only if both are valid can you enter.

	You  <----verify----> Receptionist

	This is Mutual TLS.

	Microservice Example

	Suppose you have:

	Order Service
	Inventory Service
	Payment Service
	Notification Service

	Only the Order Service should be allowed to call the Payment Service.

	With mTLS:

	Order Service
	   |
	   |  "Here's my certificate."
	   |
	Payment Service
	   |
	   |  "Certificate verified."
	   |
	"Access granted."

	If an attacker creates a fake service:

	Fake Payment Service

	The conversation becomes:

	Fake Service
	   |
	   | "Trust me."
	   |
	Payment Service
	   |
	   | "Show your certificate."
	   |
	Fake Service
	   |
	   | "I don't have one."
	   |
	Connection rejected.

	The attacker cannot communicate because it doesn't have a valid certificate.

	What is a Certificate?

	A certificate is like a digital ID card.

	It contains:

	The service's identity (e.g., "Payment Service")
	A public key used for encryption
	A trusted authority's digital signature proving the certificate is genuine

	When another service receives the certificate, it checks:

	Is it issued by a trusted authority?
	Has it expired?
	Does it belong to the expected service?

	If all checks pass, the connection proceeds.

	TLS vs mTLS
	Feature	TLS	mTLS
	Server proves identity	✅	✅
	Client proves identity	❌	✅
	Data encrypted	✅	✅
	Prevents fake server	✅	✅
	Prevents fake client/service	❌	✅
	Why is mTLS important in microservices?

	In a large system with dozens or hundreds of services, you don't want any internal service to call every other service.

	For example:

	Order Service  ---> Payment Service   ✅

	Inventory Service ---> Payment Service   ❌

	Random Pod ---> Payment Service   ❌

	Compromised Service ---> Payment Service   ❌

	With mTLS, only services with valid identities can communicate, significantly reducing the risk of unauthorized access.

	How is this implemented in Spring Boot?

	You usually don't write TLS or mTLS logic yourself. Instead:

	Each service gets a certificate.
	Services are configured to use HTTPS.
	The server is configured to require client certificates (client-auth=need).
	During the TLS handshake, both services exchange and verify certificates before any application data is sent.

	In Kubernetes environments, a service mesh such as Istio or Linkerd can automatically enable mTLS between services, so developers often don't need to modify application code.
	
# On what principle microservices are build upon?

# What is RATE Limiting/ API Throttling?

	Rate Limiting is a technique that allow a service to control the consumption of resources used by an application instance.
	eg:
	If you exceeds twitter API limit:
	It will shows somethinglike: 
	```json
	{"errors":[{"code:88, "message": "Rate limit is exceeded" "}]}
	```
	Within API we can send Repsonse code 429- Too Many Requests.
