# Computer Science
### Big O notation
https://www.bigocheatsheet.com/

Describes performance or complexity of an algorithm in the worst case. Relation between 
size of input data and amount of operation for an algorithm.

A binary search only touches a small number of elements. If there's a billion 
elements, the binary search only touches ~30 of them. A quicksort touches every 
single element, a small number of times. If there's a billion elements, 
the quick sort touches all of them, about 30 times: about 30 billion touches total.

- Complexities:
    - **O(1)** describes an algorithm that will always execute in the same time 
    (or space) regardless of the size of the input data set. (map)
      
    - **O(N)** describes an algorithm which performance will grow linearly and in direct 
    proportion to the size of the input data set. (loop)
    
    - **O(N^2)** represents an algorithm whose performance is directly proportional to the 
    square of the size of the input data set. This is common with algorithms that involve 
    nested iterations over the data set. Deeper nested iterations will result in O(N^3), O(N^4) etc.
      
    - **O(2^N)** exponential, usually recursive algorithm whose size **growth doubles with 
    each addition to the input data set**.

    - **O(log N)** doubling the size of the input data set has little effect on its growth as after a single
    iteration of the algorithm the data set will be halved and therefore on a par with an input 
    data set half the size. This makes algorithms like binary search extremely efficient when 
    dealing with large data sets.
    
    - **O(N * Log N)** describes an algorithm that contains O(N log N) nested in O(N) loop.
    
    - **O(N!)** permutations algorithms.
      
      
- Sort algorithms (most popular):
    - **Quicksort** [O(n log(n)) Θ(n log(n)) O(n^2)] https://www.programiz.com/dsa/quick-sort
        - Peek a pivot (usually last elem in array).
        - Go through array and compare every element in it with pivot.
        - Pick first bigger element and remember it position (pointer).
        - Keep going.
        - If element is bigger - do nothing.
        - If element is smaller - swap it with the pointer.
        - Pick next pointer.
        - If last element - swap pivot with the pointer.
        - Recursively repeat on right and left sub-arrays.

    -  Bubble sort [O(n) Θ(n^2) O(n^2)]
        - Go from the beginning of array 
        - Compare two elements
        - Swap them if needed
        - Go on
        - At the end of iteration the largest element will be on the right
        - Repeat

    - **Merge sort** [O(n log(n)) Θ(n log(n)) O(n log(n))]
        - Divide array on half.
        - Repeat recursively.
        - Compare items.
        - Rearrange them in right order.
        - Return subarray.
        - Compare two subarrays and rearrange them in right order.
        - Return subarray.
        
        ![big_o](images/big_o_merge_sort.png)

- Search:
    - **Binary search [O(log N)]** is a technique used to search sorted data sets. It works by selecting the middle element of 
    the data set, essentially the median, and compares it against a target value. If the values match it 
    will return success. If the target value is higher than the value of the probe element it will take the 
    upper half of the data set and perform the same operation against it. Likewise, if the target value is 
    lower than the value of the probe element it will perform the operation against the lower half. It will 
    continue to halve the data set with each iteration until the value has been found or until 
    it can no longer split the data set.

    ![big_o](images/big_o_binary_search.png)

<!-- 

### OOP
- Inheritance
- Polymorphism 
- Encapsulation

### Design Principles
- Coupling
- Cohesion
- SOLID
- KISS
- DRY
- YAGNI
- Inversion of Control
- Dependency Injection

# Go
- Interface internals
- Map internals
- Goroutine internals
- Scheduler
- Chan internals <!-- make; clone; how to tell if buffered channel is full? -->
- Race Condition vs Data Race
- Deadlock
- Concurrency patterns
- Mutex & RWMutex
- sync/atomic
- Testing
- Benchmarks
- Pprof
- Race Detector
- Context
- io.Copy
- io.Reader
- io.Writer
- Inheritance vs Composition
- SOLID <!-- dave chaney -->

# HTTP 
### Internals
- HTTP1 <!-- web sockets; how to send a file; cookies; CORS -->
- HTTPS
- HTTP2 <!-- compression, bidirectional channels, multiplexing -->
- DNS <!-- dns in browser -->

### API
- CRUD
- CQRS API
- Webhooks
- Throttling
- Rate Limiting
- SOAP
- REST
- OpenAPI
- GRPC
- Richardson maturity model

### Servers
- Servlets API, Filters, Listeners, Deployment Descriptor
- ClassLoader Architecture for Application Server
- Sessions, Safe Multithreading
- Tomcat/Jetty/Netty thread model and connectors
<!-- Describe relation between Tomcat and Servlet API (interaction, spec, impl)?" -->

### Web frameworks

- Spring + SpringBoot (Application Context, Configurations, MVC, I18N, Theming and Templating: Freemaker; Mustache; Apache Tiles)
- Spring5 functional web framework: Concept, HandlerFunction, RouterFunctions, Extended WebClient
- Dropwizard
- JEE JAX-RS: Jersey
- Describe servlet API, dispatcher servlet, why do we have filters? What frameworks & libs are available to implement REST web services? 
- Describe Spring MVC request lifecycle? Explain Model, ModelMap and ModelAndView? How does Spring5 define routings?"									

### golang net/http
									
# Storage & Databases
- Normalization <!-- denormalization, normal forms, relations -->
- SQL
  - Join
  - Union
  - Function
  - Grouping
  - Limit/Offset
  - Optimization
  - Query execution plan
  - Analytical function
- ACID 
- Isolation levels
- Deadlock
- Indexes <!-- clustered, non clustered (btree, hash, gin, gist, functional, bitmap, etc.) -->
- Partitioning
- Sharding
- Replication
- Scalability
- Views <!-- materialized, non-materialized -->
- NoSQL <!-- key-value, document oriented, column based, graph db, real time, timeseries -->

<!-- 

Explain why specific database
is fits certain requirements better
(i.e.: cassandra for scenarios where high availability) 
and what would be downsides. 
explain what consistency does nosql database of your choice provides?

-->

### golang database/sql
- Connection pool
- Ping <!-- what is the ping? why do you need to implement it? -->
- Rollback <!-- how to implement rollback? (https://github.com/golang/go/wiki/SQLInterface) -->

# Security
### Cryptography
- Encryption vs Hashing
- Symmetric/Asymmetric
- Salt
- Rainbow tables
- PKI
- Key exchange methods

### Authorization, Authentication and Accounting
- Authentication
- Authorization
- Accounting
- RBAC access control
- UBAC; ABAC; ACL; Rule-based

### Oauth 2.0 / Open ID Connect (OIDC) / SAML
- SSO 
- OAuth 2.0 <!-- its purpose, usecases, flows, tokens (access/refresh) -->
- OpenId Connect <!-- purpose, flows, tokens (access/refresh) -->
- JWT
- SAML

### OWASP top 10
- OWASP top 10
- Injection
- XSS
- Broken Authentication
- Sensitive Data Exposure
- Broken Access Control
- Insecure Deserialization

### Secrets management
- Hashicorp Vault
- Puppet Hierra
- DLP API

<!-- How do you inspect your project to see if there are any secrets that are stored insecurly in the code? 
Which secrets management best practices you know/use?
What will you do if you find a prod DB password in your project code on GitHub? 
How will you store secrets in your next/imaginary project? Which tools are you going to use and why?
-->

# Microservices & IPC
### Microservices architecture
- Pros/cons <!-- when to split; microservices vs SOA, DDD in microservices, scaling -->

<!-- 

how to decide what part of code to consider a microservice?

when do you usually consider splitting monolitic app to microservices?
describe approaches to handle cross cutting concerns in microservices?
what are typical microservice related patterns?
describe patterns of decomposition of monolitic application

-->
									
### Integration patterns
- Enterprise integration patterns <!-- examples -->
- Saga <!-- Compare orchestration vs choreography in saga implementation -->
- Shared DB <!-- cons/pros -->
- Transactional outbox
- Transactional log
- Sidecar

### Message queues/Streaming queues
- Message Queue  <!-- RabbitMQ, ActiveMQ, ZeroMQ, Kafka, Duplicates handling, ordering, error handling -->
- Streaming Queue <!-- How to handle message ordering in both queue types? -->
- Stream <!-- structured and unstructured streams -->
- Pub/Sub
- Events <!-- Pub/sub model vs Events stream model? -->
- Dead letter <!-- Pattern -->
- Duplicates <!-- How to process duplicates? How they can happen? -->
- Kafka consumer group
- Kafka topic
- Kafka partition <!-- How to calculate amount of partitions in Kafka for proper setup? -->
- Zookeeper in kafka

### Service Discovery (SD)
- DS
- Approaches
- Client side discovery
- Server side discovery
- Service registry
- Gossip vs Raft
- K8S SD vs Eureka or Consul

### API Gateway/BFF (Backend for frontend)									
- API Gateway in microservices
- Nginx
									
### Reliability
- Circuit breaker
- Retry strategy <!-- Should we impliment both Circual bracker and Retry strategy? -->
- Throttling
- Fallbacks (Graceful Degradation, Cache, Functional redundancy, Stubbed data)
- Service reliability and fault tolerance?
									
### Service Mesh									
- Service mesh <!-- What should be handled by service mesh? Frameworks-->
- Istio
- LinkerD
- Istio vs LinkerD

<!--
What is service mesh? How it hepls: Advanced routing, Deployment strategies, Testing, local Envs., Tracing. Frameworks: 
Istio, linkerd, AWS Service Mash, Kong Service Mesh, Envoy									

How service mesh can be integrated to the system? Which patterns are used?
How service mesh can help with releases?
How service mesh can help in testing?
Which trace frameworks are supported by Istio?
How to load balance gRPC? How service mesh can help?
How to configure Tracing in Istio?
What is mTLS?  How to configure mTLS in Istio?
How service mesh can help to organize local envs?
Can authorization be handled by service mesh?
Load balancing should be part of service mesh?"									
-->
									
### Distributed Tracing/Logging/Monitoring									
- OpenTracing <!-- Zipkin, Jaeger, Aws X-Ray. ELK (EFK - filebeat instead logstash) Stack, Prometheus, Datadog, Grafana, custom metrics. What should be monitored? Alerts -->
- OpenTelemetry
									
### Testing in microservices									
- Chaos testing
- Contract testing

# DevOps
### Docker and docker-compose									
- Docker and docker-compose (Best practices) <!-- image, layers, mount, multistage build -->
- Containerization vs Virtualization

### Kubernetes
- Architecture of kubernetes, Kubernetes components, Configuration management in kubernetes cluster, Container management.
- Node, pod, namespace, kubectl, deploy, minikube, secrets.

### CI/CD

### Load Balancing									
- Load Balancing, types of load balancing, tools
									
### Deployment strategies									
No downtime deployment benefits and implementation complexity. Rolling , blue/green canary deployments.									

- No downtime deployment
- Rolling deployment
- Blue/greeen deployment
- Canarry deployment

### Infrastructure as a code									
- IaC concepts, Terraform, AWS Cloud Formation, Azure AMR, GCP Cloud Deployment Manager 										
									
### Configuration management
- Configuration management (push vs pull)
- Ansible, Puppet, Solt, Chef

### Binary repository management									
- Artifactory, Nexus

### "Logging, Monitoring, Alerting
- Logging best practices. System and application monitoring. Alterting.
- Tooling: ELK, Prometheus, Grafana or similar

# Practices, patterns and tools									

### Design patterns									
- GoF
- DDD
- CQRS
- Event Sourcing

### Software Architecture Patterns									
- Layered architecture
- Event-Driven architecture
- Microkernel architecture
- Space-Based architecture

# AWS 

### AWS fundamentals									
- Key components (EC2, AMI, IAM, Storages, Queues etc.)
- EC2 instance types and scaling (on-demand/reserved/spot)
- Availability Zones and Regions

### Serverless									
- AWS Lambda deployment
- AWS Lambda limitations 
- AWS Lambda retry policies
- Cold start and scaling
- Execution model
- Usecases
								
### Compute									
- ElasticBeanStalk
- ElasticSearch
- EKS, ECS, ECR