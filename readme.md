# Computer Science
## Big O notation
https://www.bigocheatsheet.com/

Describes performance or complexity of an algorithm in the worst case. Relation between 
size of input data and amount of operation for an algorithm.

A binary search only touches a small number of elements. If there's a billion 
elements, the binary search only touches ~30 of them. A quicksort touches every 
single element, a small number of times. If there's a billion elements, 
the quick sort touches all of them, about 30 times: about 30 billion touches total.

### Complexities:
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
      
      
### Sort algorithms (most popular)
**Quicksort** [O(n log(n)) Θ(n log(n)) O(n^2)] \
https://www.programiz.com/dsa/quick-sort

1) Peek a pivot (usually last elem in array).
2) Go through array and compare every element in it with pivot.
3) Pick first bigger element and remember it position (pointer).
4) Keep going.
5) If element is bigger - do nothing.
6) If element is smaller - swap it with the pointer.
7) Pick next pointer.
8) If last element - swap pivot with the pointer.
9) Recursively repeat on right and left sub-arrays.

**Bubble sort** [O(n) Θ(n^2) O(n^2)]
1) Go from the beginning of array 
2) Compare two elements
3) Swap them if needed
4) Go on
5) At the end of iteration the largest element will be on the right
6) Repeat

**Merge sort** [O(n log(n)) Θ(n log(n)) O(n log(n))]
- Divide array on half.
- Repeat recursively.
- Compare items.
- Rearrange them in right order.
- Return subarray.
- Compare two subarrays and rearrange them in right order.
- Return subarray.

![big_o](images/big_o_merge_sort.png)

### Search
**Binary search [O(log N)]** is a technique used to search sorted data sets. It works by selecting the middle element of 
the data set, essentially the median, and compares it against a target value. If the values match it 
will return success. If the target value is higher than the value of the probe element it will take the 
upper half of the data set and perform the same operation against it. Likewise, if the target value is 
lower than the value of the probe element it will perform the operation against the lower half. It will 
continue to halve the data set with each iteration until the value has been found or until 
it can no longer split the data set.

![big_o](images/big_o_binary_search.png)

### Data structures
- Array (fixed length)
- Linked list
- Stack (last in first out)
- Queue (fist in first out)
- Hash table (go map)
- Graph
- Set/HashSet (something from java)
- Binary tree (hierarchical data structures) **TODO**

## OOP
- Inheritance vs Composition\
  (different lifecycles; embedding rather than extending; no `super`; no overwriting)
- Polymorphism (multiple implementations of single interface)
- Encapsulation (access control)

## Design Principles

- KISS - keep it simple, stupid
- DRY - don't repeat yourself
- YAGNI - you're not gonna need it

- Inversion of Control (IoC) is a generic term meaning that rather than having the application 
call the implementations provided by a library (also know as toolkit), a framework calls 
the implementations provided by the application. The term Inversion of Control originally 
meant any sort of programming style where an overall framework or run-time controlled the program flow.

- Dependency Injection (DI) is a form of IoC, where implementations passed into an object 
through constructors/setters/service lookups, which the object will 'depend' on in order to behave correctly.

- Robustness principle https://en.m.wikipedia.org/wiki/Robustness_principle

### Coupling / Cohesion
- Coupling - Strength of the relationships between modules. The lower the better.
To change one module changes in different one are required.
- Cohesion - Strength of the relationships **within** a module or class. Low cohesion 
would mean that the class does a great variety of actions - it is broad, unfocused on 
what it should do. High cohesion means that the class is focused on what it should be 
doing, i.e. only methods relating to the intention of the class. The higher the better.

### SOLID
#### SRP: The Single Responsibility Principle
One class should solve only one problem. \
"A module should have one, and only one, reason to change."

#### OCP: The Open Closed Principle (TODO)
- A software artifact should be open for extension but closed for modification.
- Old: You should be able to extend a class behavior, without modifying it.

#### LSP: The Liskov Substitution Principle
Any interface's realization should be interchangeable in any place.

#### ISP: The Interface Segregation Principle
Don't force clients to implement interface they don't use.

#### DIP: The Dependency Inversion Principle
- Depend on abstractions, not on concretions.

# Go
### Interfaces: Interface Values, Type Assertions
https://research.swtch.com/interfaces\
https://github.com/teh-cmc/go-internals/blob/master/chapter2_interfaces/README.md

Interface is an abstract data type that doesn't expose the representation
of internal structure. When you have a value of an interface type, you know
nothing about what it is, you know only what it can do.

```go
// src/runtime/runtime2.go
type iface struct {
    tab  *itab
    data unsafe.Pointer // underlying data
}

type itab struct {
    inter *interfacetype
    _type *_type     // metadata describing type
    hash  uint32     // copy of _type.hash. Used for type switches.
    _     [4]byte
    fun   [1]uintptr // variable sized. fun[0]==0 means _type does not implement inter.
}

// src/runtime/type.go
type interfacetype struct {
    typ     _type
    pkgpath name
    mhdr    []imethod // probably methods and their types
}
```

Interface values may be compared using == and !=. Two interface values are equal if both are
nil, or if their dynamic types are identical and their dynamic values are equal according to the
usual behavior of == for that type. Because interface values are comparable, they may be used
as the keys of a map or as the operand of a switch statement. However, if two interface values
are compared and have the same dynamic type, but that type is not comparable (a slice, map or
function for instance), then the comparison fails with a panic.

A nil interface value, which contains no value at all, is not the same as an interface value
containing a pointer that happens to be nil:

``` go
type xi interface{}
var a *string = nil
var x xi = a
fmt.Println(x == nil) // false
```

### Maps
https://dave.cheney.net/2018/05/29/how-the-go-runtime-implements-maps-efficiently-without-generics

https://github.com/golang/go/blob/master/src/runtime/map.go
```go
// A map is just a hash table. The data is arranged
// into an array of buckets. Each bucket contains up to
// 8 key/elem pairs. The low-order bits of the hash are
// used to select a bucket. Each bucket contains a few
// high-order bits of each hash to distinguish the entries
// within a single bucket.
```

Default underlying array size == 8, so default LOB == 3 (2^3 = 8), as map grows
it doubles it underlying array and increments LOB (2^4 = 16) and so on.

```go
// A bucket for a Go map.
type bmap struct {
	// tophash generally contains the top byte of the hash value
	// for each key in this bucket. If tophash[0] < minTopHash,
	// tophash[0] is a bucket evacuation state instead.
	tophash [bucketCnt]uint8
	// Followed by bucketCnt keys and then bucketCnt elems.
	// NOTE: packing all the keys together and then all the elems together makes the
	// code a bit more complicated than alternating key/elem/key/elem/... but it allows
	// us to eliminate padding which would be needed for, e.g., map[int64]int8.
	// Followed by an overflow pointer.
}
```
**Why are they unordered?**
- It's more efficient, runtime don't need to remember order.
- They intentionally made it random starting with Go 1 to make developers not rely on it. Since the release of Go 1.0, the runtime has randomized map iteration order. Programmers had begun to rely on the stable iteration order of early versions of Go, which varied between implementations, leading to portability bugs. Iteration order which order may change from release-to-relase, from platform-to-platform, or may even change during a single runtime of an app when map internals change due to accommodating more elements.
- Regarding the last point: during map growth its buckets gets reordered that would cause to different orders during runtime. Anyway, even without reordering if it would loop like `bucket -> elements -> next bucket -> elements...`, than after adding new element it would appear in the middle of order because you newer know in which bucket it will go.

**Where actual values are stored in memory?**
The "where" is specified by hmap.buckets. This is a pointer value, it points to an array in memory, an array holding the buckets. (NOTICE: it isn't `*bmap` or `bmap` it's `unsafe.Pointer` what says that it a bit more complicated than i thought)

```
[8]tophash -> [8]key -> [8]value -> overflowpointer
```

### Scheduler

![scheduler](images/go_scheduler.jpg)

https://www.ardanlabs.com/blog/2018/08/scheduling-in-go-part2.html \
https://www.youtube.com/watch?v=YHRO5WQGh0k&ab_channel=GopherAcademy

```
There are hardware threads and software threads.

+ Software threads are threads of execution managed by the operating system.
+ Hardware threads are a feature of some processors that allow better utilisation 
  of the processor under some circumstances. They may be exposed to/by the operating 
  system as appearing to be additional cores ("hyperthreading").
```

If processor has multiple hardware threads per physical core (Hyper-Threading), 
each hardware thread will be presented to Go program as a virtual core. Go Scheduler 
operates many OS threads, it can create and decide how to use them. But it can 
asynchronously run no more threads than virtual cores.

```
When goroutine makes synchronous syscall, OS blocks that thread.
```

- **Context-Switch** - pulling executing goroutine/thread off the core and replacement it with runnable goroutine/thread
- **Runqueue** - local per-thread (but there is global as well) **FIFO** queue of waiting goroutines. 
Global runqueue has low priority and it's checked by threads less frequently than local queues. Scheduler 
detects CPU-Bound goroutines and puts them to global queue.
- **Parking** - "caching" of empty threads for future using to avoid destruction and creation of new threads since thats expensive operation.
- **Handoff** - transferring of blocked by IO-Bound operation thread's runqueue to other thread (parked or new).
- **Work Stealing** - if thread has empty queue but there is idle goroutines in other queues it picks goroutine from random runqueue.

```
If thread has no job, OS will context-switch it off the Core.
```

When Goroutine_1 makes synchronous syscall it's **Thread_1 being blocked by the OS**, at this point, Scheduler 
detaches Thread_1 from the P with the blocking Goroutine_1 still attached. Then Scheduler brings in another Thread_2 
(parked or new), transfers runqueue from old Thread to the new one (**handoff**), and picks next Goroutine_2 from 
the queue (**work-stealing**). When blocking syscall is finished, previous Goroutine_1 can move back into the runqueue 
(to the very end (FIFO)). Thread_1 is going to park. [Thread_2 replaces Thread_1].

### Channels
```go
type hchan struct {
	qcount   uint           // total data in the queue
	dataqsiz uint           // size of the circular queue
	buf      unsafe.Pointer // points to an array of dataqsiz elements
	elemsize uint16
	closed   uint32
	elemtype *_type // element type
	sendx    uint   // send index
	recvx    uint   // receive index
	recvq    waitq  // list of recv waiters
	sendq    waitq  // list of send waiters
	lock     mutex
}
```

- `buf` - array with size
- `closed` - flag
- `lock` - embedded structure
- `sendq` and `recvq` - goroutines queue referenced to this channel

```go
// allocates hchan in heap
func makechan(t *chantype, size int64) *hchan {/*...*/}
```

`makechan` returns pointer to channel, so it's possible to pass them as it is.

### Race Condition vs Data Race (TODO)
Race condition - state of program that produces non-deterministic staff.
Data Race - panic that happens if multiple goroutines access same data and at least one writes.

### Deadlock (TODO)

### Go concurrency patterns
#### Pipelines
Informally, a pipeline is a series of stages connected by channels, where each stage is a group
of goroutines running the same function. In each stage, the goroutines
- receive values from upstream via inbound channels
- perform some function on that data, usually producing new values
- send values downstream via outbound channels

```go
// this function is cool, because it produces a channel, so the work may be distributed between few goroutines easily.
func gen(nums ...int) <-chan int {
    out := make(chan int)
    go func() {
        for _, n := range nums {
            out <- n
        }
        close(out)
    }()
    return out
}

func sq(in <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        for n := range in {
            out <- n * n
        }
        close(out)
    }()
    return out
}

func main() {
    // Set up the pipeline and consume the output.
    for n := range sq(sq(gen(2, 3))) {
        fmt.Println(n) // 16 then 81
    }
}
```

#### Workers
```go
// out is passed as argument instead of being created
func sq(in <-chan int, out chan int, wg *sync.WaitGroup) {
	go func() {
		for n := range in {
			out <- n * n
		}
		wg.Done()
	}()
}

func main() {
	// GEN IS TAKEN FROM PIPELINE EXAMPLE
	job := gen(1, 2, 3, 4, 5)
	res := make(chan int)
	var wg sync.WaitGroup

	for i := 0; i < runtime.NumCPU(); i++ {
		wg.Add(1)
		go sq(job, res, &wg)
	}

	// wait on the workers to finish and close the result channel
	go func() {
		wg.Wait()
		close(res)
	}()

	for r := range res {
		fmt.Println(r)
	}
}
```

#### Semaphore
```go
func do(jobs []job) error {
	var (
		wg   sync.WaitGroup
		sem  = make(chan int, runtime.NumCPU()) // create semaphore with buffer size
		errs = make(chan error)                 // non-buffered
	)

	for _, j := range jobs {
		wg.Add(1)

		go func(j job) {
			sem <- 1 // write to sem

			err := doSomething(j)
			if err != nil {
				select {
				case errs <- err:
				default:
				}
			}

			<-sem // release sem
			wg.Done()
		}(j)
	}

	wg.Wait()   // wait
	close(errs) // close errs - needed if there is no errors

	return <-errs // first error from errs
}
```

#### Cancellation
When main decides to exit without receiving all the values from out, it must tell
the goroutines in the upstream stages to abandon the values they're trying to send.
1. Additional channel done that will be closed when receiver don't need values
2. Context `Cancel()` `Done()`

```go
for n := range c {
	select {
	case out <- n: // write to chan
	case <-done:   // receive from done
		return
	}
}
```

#### Subscription
Kinda useless pattern, but the main idea is next:

- function `Subscribe(r Resource) s Subscription {}`
- `Resource` responsible to produce `Items` (e.g. `database/sql` query result)
- `Subscription` - interface with method `Updates() <-chan Items` and `Close()`

`Subscribe` function starts goroutine under the hood that writes to `Subscription`'s internal channel. 
So that logic is hidden behind "simple" `Subscription` interface.

#### Ping-pong
Even more useless than subscription.

Create two channels `ping` and `pong` (unexpected), then start two 
goroutines `pinger` and `ponger` that receive ping and pong, each goroutine
starts endless loop where reads from `ping` does something and writes to `pong` and vice versa.

#### Fan-out
Multiple functions can read from the same channel until that channel is closed; this is called fan-out.

#### Fan-in
A function can read from multiple inputs and proceed until all are closed by multiplexing 
the input channels onto a single channel that's closed when all the inputs are closed. 
This is called fan-in.

- Distribute work between few pipelines, then merge the output (in goroutine) 
and return resulting channel.

```go
func merge(cs ...<-chan int) <-chan int {
    var wg sync.WaitGroup
    out := make(chan int)

    // Start an output goroutine for each input channel in cs.  output
    // copies values from c to out until c is closed, then calls wg.Done.
    output := func(c <-chan int) {
        for n := range c {
            out <- n
        }
        wg.Done()
    }

    wg.Add(len(cs))
    for _, c := range cs {
        go output(c)
    }

    // Start a goroutine to close out once all the output goroutines are
    // done.  This must start after the wg.Add call.
    go func() {
        wg.Wait()
        close(out)
    }()
    return out
}
```

#### Parallel for-loop
**TODO**

### Mutex & RWMutex (TODO)

### sync/atomic (TODO)

### Race detector
https://blog.golang.org/race-detector

```sh
go run -race main.go # will run race detector. same for build, get, test and install
```

### Profiling tools (pprof, http/pprof, profile)
There are two pprof implementations available:

- net/http/pprof
- runtime/pprof

``` go
import "runtime/pprof"

file, _ := os.Create(filepath.Join("cpu.pprof")) // create a file for profiler output
pprof.StartCPUProfile(file)                      // start CPU Profiler
pprof.StopCPUProfile()                           // write results to the file
```

Web view:

``` shell
go tool pprof -http=:8080 cpu.pprof
```

### Flappy (flaky) tests
A flaky test is a test which could fail or pass for the same configuration.
- Concurrency
- Time bombs: Does your test requests for the current time?

to find them:
```sh
go test -count=20
```

### Benchmarks
https://dave.cheney.net/2013/06/30/how-to-write-benchmarks-in-go

```go
// from fib_test.go
func BenchmarkFib10(b *testing.B) {
        // run the Fib function b.N times
        for n := 0; n < b.N; n++ {
                Fib(10)
        }
}
```
Each benchmark must execute the code under test b.N times. The for loop in BenchmarkFib10 will be present in every benchmark function.

```sh
# run benchmarks:
go test -bench=.

# flags:
-benchmem # shows memory allocations
-benchtime=20s # b.N execution time.
-cpuprofile=cpu.out # save cpu profile
-memprofile=mem.out # save memory profile
```

By default b.N runs benchmark for one second this may be changed with `-benchtime=20s`
Methods:
- b.ResetTimer()
- b.StopTimer()
- b.StartTimer()

### Stack and heap
https://segment.com/blog/allocation-efficiency-in-high-performance-go-services/

Heap - global program memory. If a function creates a variable and returns reference to it,
the variable allocates in heap.

Stack - local memory allocated per function. It has its own top that moves up and down for each nested
call. This memory is freed once function is returned.

Go prefers allocation on the stack — most of the allocations within a given Go program will be on the
stack. It’s cheap because it only requires two CPU instructions: one to push onto the stack for allocation,
and another to release from the stack. On the other side, heap allocations are expensive, `malloc` has to
search for a chunk of free memory large enough to hold the new value and the garbage collector
scans the heap for objects which are no longer referenced.

Compiler performs `escape analysis` - set of rules that variable must pass on **compilation** stage
to be allocated in stack. Stack allocation requires that the lifetime and memory footprint of a
variable can be determined at compile time.

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