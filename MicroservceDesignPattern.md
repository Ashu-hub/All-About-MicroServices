# Scale Quebe(The book, The Art of Scalability):
	1) X-axis = Horizental duplication/scale by cloning:
		It means simple running multiple copies of same application behind load balancer. So if there are N copies then each copies hanlde 1/N requests
		Problems:-
		* Because each copies potentially access all data,cache requires more memory
		* Does not tackle the problem of increasing development and appication complexity.
	
	2. Y-axis = Vertical scaling/Functional Decomposition/ Scale by spliting different things
		It basciallly split the application into multiple different services. Each Service is related to one or more closely related functions.
		Types:
		Verb Based decomposition:- Decompisiton of services that implements a single use case or particular action such as **scheckout or Shipping Services.
		Noun Based decomposition:- Decompisiton of Service that is responsible for all operations related to particular entity like customer Management
		Decompose by business capability and define services corresponding to business capabilities. like **Delivery Management, Inventory mangement, Order management.
		Decompose by domain-driven design subdomain
		An Effective application must use both verb and noun based decomposition.
		
	3. Z-axis:- In this each Server runs identical copy of code. Similar to X-axis each server is responsible fo running **one subset of data.
		Z-axis splits are commonly used to scale databases.
		
		Z-axis scaling has a number of benefits.
		Each server only deals with a subset of the data.
		This improves cache utilization and reduces memory usage and I/O traffic.
		It also improves transaction scalability since requests are typically distributed across multiple servers.
		Also, Z-axis scaling improves fault isolation since a failure only makes part of the data in accessible.
	
		Z-axis scaling has some drawbacks.
		One drawback is increased application complexity.
		We need to implement a partitioning scheme, which can be tricky especially if we ever need to repartition the data.
		Another drawback of Z-axis scaling is that doesn’t solve the problems of increasing development and application complexity. To solve those problems we need to apply Y-axis scaling.

#Benefits:

		Enables the continuous delivery and deployment of large, complex applications.
		Improved maintainability - each service is relatively small and so is easier to understand and change
		Better testability - services are smaller and faster to test
		Better deployability - services can be deployed independently
		It enables you to organize the development effort around multiple, autonomous teams. Each (so called two pizza) team owns and is responsible for one or more services. Each team can develop, test, deploy and scale their services independently of all of the other teams.
		Each microservice is relatively small:
		Easier for a developer to understand
		The IDE is faster making developers more productive
		The application starts faster, which makes developers more productive, and speeds up deployments
		Improved fault isolation. For example, if there is a memory leak in one service then only that service will be affected. The other services will continue to handle requests. In comparison, one misbehaving component of a monolithic architecture can bring down the entire system.
		Eliminates any long-term commitment to a technology stack. When developing a new service you can pick a new technology stack. Similarly, when making major changes to an existing service you can rewrite it using a new technology stac

#Drawbacks:
		
		Developers must deal with the additional complexity of creating a distributed system:
		Developers must implement the inter-service communication mechanism and deal with partial failure.
		Implementing requests that span multiple services is more difficult
		Testing the interactions between services is more difficult
		Implementing requests that span multiple services requires careful coordination between the teams
		Developer tools/IDEs are oriented on building monolithic applications and don’t provide explicit support for developing distributed applications.
		Deployment complexity. In production, there is also the operational complexity of deploying and managing a system comprised of many different services.
		Increased memory consumption. The microservice architecture replaces N monolithic application instances with NxM services instances. If each service runs in its own JVM (or equivalent), which is usually necessary to isolate the instances, then there is the overhead of M times as many JVM runtimes. Moreover, 
		if each service runs on its own VM (e.g. EC2 instance), as is the case at Netflix, the overhead is even higher.

# Decomposition Patterns:
		1. Decompose by business capability
		2. Decompose by subdomain
		3. Self-contained Service new
		4. Service per team new

# Refactoring Patterns
 		1. Strangler Application
		2. Anti-corruption layer

##	 [Strangler application](https://microservices.io/patterns/refactoring/strangler-application.html)
		Problem:- How do you migrate a legacy monolithic application to a microservice architecture?
		
		Solution:
		Modernize an application by incrementally developing new(Strangler) application around the legacy application. In this Strangler application has a microservice architecture.
		The Strangler application consists of two types of Services:
		1st Services that already resides in the old monolithic application
		2nd services that implements new features. The Latter are useful as they demonstrate to buisness the value of using microservices.

# Data management Patterns:
		1. Database per Service
		2. Shared database
		3. Saga
		4. API Composition
		5. CQRS
		6. Domain event
		7. Event sourcing
		
# How to maintain data consistency?
		In order to ensure loose coupling, each service has its own database. Maintaining data consistency between services is a challenge because 2 phase-commit/distributed transactions is not an option for many applications.
		An application must instead use the Saga pattern. A service publishes an event when its data changes. Other services consume that event and update their data. There are several ways of reliably updating data and publishing events including Event Sourcing and Transaction Log Tailing.

## [SAGA Pattern](https://microservices.io/patterns/data/saga.html)

		In microservice architecture, we have databases per service pattern. i.e Each Service has its own databases. But some businees transactions **span multiple service.**
		so we need a mechanism to implement **transactions that span multiple services.**
		
		For example :- while building an e-commerce store where a customer has credit limit. The application must ensures that the order does not exceeds the Credit limits.
		Since Order and Cutomer are two different databases so cant use local ACID transactions.
		
		Solution:
		Saga, implements each business transactions thats spans multiple services.
		A Saga is a sequence of local transactions.
		
		There are two ways of Coordination sagas:-
		1) Choreography:- each local transactions publishes domains events that triggers local transaction in other Service.
		2) Orchestration:-  an Orchestrater tells the participants about what local transaction to executes.
		
		Example Choreography based Saga:-
		1) The OrderService receives the post/order request and created an order in a pending state.
		2) It then emits an Ordere created event.
		3) The Customer's Service event handler attempts to reserve credit.
		4) It then emits the event indicating the outcome.
		5) The Order Service'sevent handler either approves or reject the order.
		
		Example of Orchestrator based Saga:
		1) The OrderService receives the post/order request, it then creates Create Order Saga Orchestrator
		2) This saga Orchestrator created an order in Pending State.
		3) It then sends Reserve Credit command to Customer Service.
		4) Customer Service attempt to reseve credit
		5) It then reply with a message indicatng an Outcome.
		6) The Saga Orchestrator either Approves or reject the order.
		
		BENEFITS:-
		It 	enabales an application to maintain data consistency across Services.
		
		Drawbacks:-
		The programming model is more complex. eg a developer must design a transaction for explicitly **undo changes made earlier in saga**.
		There are also the following issues to address:
		In order to be reliable, a service must atomically update its database and publish a message/event. It cannot use the traditional mechanism of a distributed transaction that spans the database and the message broker
		The following patterns are ways to atomically update state and publish messages/events:
		Event sourcing, Transactional Outbox

## [API Composition](https://microservices.io/patterns/data/api-composition.html)
		Problem: How to implement queries in a microservice architecture?
		
		Solution :- Implement a query by defining an **API Composer**, which invoking the services that own the data and performs an in-memory join of the results.
		An API Gateway often does API composition.
		
		This pattern has the following benefits:
		It a simple way to query data in a microservice architecture

		This pattern has the following drawbacks:
		Some queries would result in inefficient, in-memory joins of large datasets.
			
## [Event Sourcing](https://microservices.io/patterns/data/event-sourcing.html)
		A Service Command need to typically update a database and sends events. In Saga, a service need to atomically update a DB and sends events.
		The Db update and seding messages/ events must be atomic in order to avoid data inconsistancy or bugs.
		
		Problem:- How to atomically update a DB?
		
		Ans: Event Sourcing:-
		Event Sourcing persists the state of business entity(such as ORder or Customer ) as a **sequence of state-changing events.** Whenever the state of buisness entity changes, a **new event is appended in the list of Events.**
		Since saving the state is a single operation, it is inhrently atomic. The application reconstructs the entity's current state by replying the events.
		An Application persists a event in a **event store**, which is a database of Events. The store has an API for adding and retriving an event. It behaves like a message broker.
		It also provide an API that enables **services to Subscribe them,** so when a service saves an event in the event store. it is delivered to all intended Subscriber.
		
		
# [CQRS](https://microservices.io/patterns/data/cqrs.html)
		It Stands for Command Query Responsibility Seperation
		
		Problem- How to implement a query that retrive data from multiple services in MS architecture?
		
		solution:
		Define a **view database,** which is a read-only replica(of that service) that is designed to support that query.
		The application keeps the replica up to data by **subscribing to Domain events** published by the service that own the data.
		
		This pattern has the following benefits:
		Supports multiple denormalized views that are scalable and performant
		Improved separation of concerns = simpler command and query models
		Necessary in an event sourced architecture

		This pattern has the following drawbacks:
		Increased complexity
		Potential code duplication
		Replication lag/eventually consistent views
		
#	Service Discovery Patterns:
		1. Client-side discovery
		2. Server-side discovery
		3. Service registry
		4. Self registration
		5. 3rd party registration	

## [Client-side Discovery](https://microservices.io/patterns/client-side-discovery.html):-
		A modern microservice-based application typically runs in a virtualized or containerized environments where the number of instances of a service and their locations changes dynamically
		How does the client of a service - the API gateway or another service - discover the location of a service instance?
		
		Problem:
		Each instance of a service exposes a remote API such as HTTP/REST, or Thrift etc. at a particular location (host and port)
		The number of services instances and their locations changes dynamically.
		Virtual machines and containers are usually assigned dynamic IP addresses.
		The number of services instances might vary dynamically. For example, an EC2 Autoscaling Group adjusts the number of instances based on load.
		
		Solution:
		When making a request to a service, the client obtains the location of a service instance **by querying a Service Registry, which knows the locations of all service instances.**
		The client also responsible for managing load balancing requests across services.
		eg: Eureka Service-Discovery
		
		Benefits:
		Simple and straightforward implementation
		Fewer moving parts and network hops compared to Server-side Discovery
		
		Drawbacks:
		This pattern couples the client to the Service Registry
		You have to implement discovery logic for each service because the services may use different programming language.
	
		[More to read](https://www.dineshonjava.com/microservice-discovery-patterns-and-registry/)
		
##[Server Side Discovery](https://microservices.io/patterns/server-side-discovery.html)	
		When making a request to a service, the client makes a request via a router (a.k.a load balancer) that runs at a well known location. 
		The router queries a service registry, which might be built into the router, and forwards the request to an available service instance.
		Example of Server side Discovery router - AWS Elastic Load Balancer (ELB)
		
		In this Pattern the client is not aware of the service registry. The client request service using a load balancer, which then queries the Service- Registry.
		In this pattern, the client is not worry about managing the load balancing.
		
		An AWS Elastic Load Balancer (ELB) is an example of a server-side discovery router. A client makes HTTP(s) requests (or opens TCP connections) to the ELB, which load balances the traffic amongst a set of EC2 instances. An ELB can load balance either external traffic from the Internet or, when deployed in a VPC, load balance internal traffic. An ELB also functions as a Service Registry.
		EC2 instances are registered with the ELB either explicitly via an API call or automatically as part of an auto-scaling group.
		
		Server-side service discovery has a number of benefits:
		Compared to client-side discovery, the client code is simpler since it does not have to deal with discovery. Instead, a client simply makes a request to the router
		Some cloud environments provide this functionality, e.g. AWS Elastic Load Balancer
		
		It also has the following drawbacks:
		Unless it’s part of the cloud environment, the router must is another system component that must be installed and configured. It will also need to be replicated for availability and capacity.
		The router must support the necessary communication protocols (e.g HTTP, gRPC, Thrift, etc) unless it is TCP-based router
		More network hops are required than when using Client Side Discovery.
		
# Cross cutting concerns Patterns:

		1. Microservice chassis
		2. Externalized configuration		
		
# 2 Phase commit:
			It is a standarized protocol that ensures that databased commit is implementated in such way that commit happen into two seperate steps.
			A Special object known as Coordinator is required in a distributed transactions.
			A Cordinator arranges activity and syncronizes between different distributed Servers/dbs.
			The two phase commit is implemented in 2 steps:-
			1)  Prepare Phase - in which the transaction mangers(cordinator )cordinates with all the transaction resources to commit.
				This phase requires OK or ABORT response from resources.
			2) Commit phase- If It receives OK ,IT commits and IF it receives ABORT IT rollbacks,
			
			Drawbacks:- What happens when Cordinator is down?
			
# 3 Phase Commit:-
				It Includes 3 Steps:
				1. Can Commit
				2. Pre Commit
				3. Do Commit 