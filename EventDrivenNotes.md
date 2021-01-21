Message Driven MicroServices:-
Logical coupling - If we say two are logial coupled, it means both shouldnot know whom to send message.




- Domain Driven Design -
 1. Identify Problem Space or Business Domain eg:- Credit Card Management
 2.	Identify Subdomains. - eg - Billing, products, Claims
 3. Bounded Context:- Design Solution to our identified Business DOmains. It is decided by the Cohesiveness betw your B domains and betw your sub domains.
	For Each Sub domain there is a bOunded Context

Aggregate:- A graph of objects that can be treated as a unit.
Commands:- Any Req which result in state change of an aggregate, is called commands. eg:- Create, Update, Delete Operations.
Query Resq	uest:- Which which fetched the state, does not lead to any state change. eg:- Read Operations

-Domain Model - IT is a mplementation of core business logic with a speific bounded context.
Identification of Domain model involves two main sets of artifacts:
	Core Domain Model- Aggeregates, Entities, Value Objects
	Domain model Operations- Command,s queries, Evenets,
	
	
Cargo Tracker:-
Booking - Booking of Cargos, Assigning a route to cargo, modification in cargo, Cancellation of cargo
Routing - Optimal Itineary Allocation based on route specification, Voyage maintenance, Have all the information of vehcile movement 
Handling - As the cargo progressed it will need handling at various ports of transit. Take care of all the info related to handling
Tracking - unique tracking ID is generated and giving to Customer for tracking purposed.


##
 Events are not presisted in message broker. 
 Whenever a event occurs it should be persisted in some Event Source(db). So that whenever we want current state of Aggregate, we can directly query the Event Source.
 Like Account Aggregate 
 