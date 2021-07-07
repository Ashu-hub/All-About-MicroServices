#Zipkin
---
Zipkin server is a open source distributed tracing system. It helps gather timing data needed to troubleshoot latency problems in service architectures. Features include both the collection and lookup of this data.

HandsOn:
1. Create a maven project -> add below 2 dependecy in pom xml
	```
		<dependency>
			<groupId>io.zipkin.java</groupId>
			<artifactId>zipkin-server</artifactId>
			<version>2.12.1</version>
		</dependency>
		<dependency>
			<groupId>io.zipkin.java</groupId>
			<artifactId>zipkin-autoconfigure-ui</artifactId>
			<version>2.12.1</version>
			<scope>runtime</scope>
```
2. Add @EnableZipkinServer to main file.

3. Add below dependecy in your microService(Book-SERVICE):
```
		<!-- Send logs to the zipkin server -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-zipkin</artifactId>
		</dependency>

		<!-- log correlation -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-sleuth</artifactId>
		</dependency>
```

4. Add in application.property file
	spring.zipkin.base-url=http://localhost:9412/ --default port is 9411
	spring.sleuth.sampler.probability=1.0

	Done!!
# Spring Sleuth
---
This is useful in tracing requests that span multiple microservices in the Spring Cloud ecosystem.
It introduces unique IDs to your logging which are consistent between microservice calls which makes it possible to find how a single request travels from one microservice to the next.
Spring Cloud Sleuth adds two types of IDs to your logging, one called a trace ID and the other called a **span ID.**
The span ID represents a basic unit of work, for example sending an HTTP request. i.e Per request
he trace ID contains a set of span IDs, forming a tree-like structure. The trace ID will remain the same as one microservice calls the next. i.e per microservice.


# Spring Rest Docs:
Similar like Swagger but better than swagger as swagger fails in case of circular dependency. SRD can be seen as extended version of swagger.

