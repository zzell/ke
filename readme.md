# Engineering Management
## Process Planning (SDLC)
## Estimation

# Requirements
## Software Requirements Engineering

# Design
## OOD :bulb:
### GoF Design Patterns
#### Creational
##### Singleton
Asserts that the whole application will have single instance of certain object
and provides global access point to that object.
The access to the object goes through the "GetInstance" method.

```go
func GetInstance() *Singleton {
	once.Do(func() {
		instance = &Singleton{}
	})
	return instance
}
```

##### Factory Method
Defines an interface (with method "Create") that allows to create different
implementations of object (Product).

##### Builder
Allows to create objects with different options. Every Builder's method
returns Builder itself, so it's convenient to chain methods. There is
also might be a Director - struct that contains builder and has method "Construct"
that creates Builder's instance with predefined options, but this is optional.

##### Prototype
Allows to copy object with it's state. Defines an interface with method "Clone"
that returns same interface. Under the hood implementation creates new instance with
same fields as Prototype.

##### Abstract Factory
Just like Factory Method, AF defines an interface, but instead of single method it has
bunch of methods like "CreateCarcass", "CreateEngine", "CreateWheel" of course
it has to be implemented in various ways (sportcar, pickup, truck).

The main point is that end users are not aware of what exact implementation they use.
Instead, correct concrete factory will be chosen at runtime at the initialization stage.
The app must select the factory type depending on the configuration or the environment settings.

#### Structural
##### Adapter
The main point: it transforms object of one type to another.

Adapter doesn't have any strict form (interface or something). And can be implemented
in various ways.

E.g it might be a struct that contains existing and target objects via composition
and owerrides target object's methods, after that access to the target performs through
that struct.

Can be a struct that implements target interface and contains existing. (most popular)
Can be a simple function that accepts existing and returns target.

```go
// Target provides an interface with which the system should work.
type Target interface {
	Request() string
}

// Adaptee implements system to be adapted.
type Adaptee struct {}

// SpecificRequest implementation.
func (a *Adaptee) SpecificRequest() string {
	return "Request"
}

// NewAdapter is the Adapter constructor.
func NewAdapter(adaptee *Adaptee) Target {
	return &Adapter{adaptee}
}

// Adapter implements Target interface and is an adapter.
type Adapter struct {
	*Adaptee
}

// Request is an adaptive method.
func (a *Adapter) Request() string {
	return a.SpecificRequest()
}
```

##### Bridge
Dependencie injection.

Lets split a large class or a set of closely related classes into separate
hierarchies-abstraction and implementation which can be developed independently of each other.

Structure contains different objects over composition and interacts with them. Use the pattern
when you need to extend a class in several orthogonal (independent) dimensions.

##### Composite
Filetree.
Fuck you composite

```go
// Component provides an interface for branches and leaves of a tree.
type Component interface {
	Add(child Component)
	Name() string
	Child() []Component
	Print(prefix string) string
}
```

##### Decorator
Lets attach new behaviors to objects
by placing these objects inside special wrapper objects that contain the behaviors

```go
// implements io.Writer
type TransportWriter struct{}
func (TransportWriter) Write(p []byte) (n int, err error) { /*...*/ }

func NewCompressionWriter(w io.Writer) io.Writer {
	return CompressionDecorator{w}
}
type CompressionDecorator struct{ writer io.Writer }
func (c CompressionDecorator) Write(p []byte) (n int, err error) {
	// compress than write
	return c.Write(p)
}
```

##### Proxy
Proxy intercepts calls to the real object before or after and can extend, perform validation, cache etc.
Similar to decorator, but Decorator get reference for decorated object (usually through constructor)
while Proxy responsible to do that by himself. Proxy implements objects interface.

```go
// implements io.Writer
type TransportWriter struct{}
func (TransportWriter) Write(p []byte) (n int, err error) { /*...*/ }

// creates transportWriter
func NewCompressionProxy() io.Writer {
	transportWriter := new(TransportWriter)
	return CompressionProxy{transportWriter}
}

type CompressionProxy struct{ writer io.Writer }
func (c CompressionProxy) Write(p []byte) (n int, err error) {
	// compress than write
	return c.Write(p)
}
```

##### Facade
Incapsulation of complex staff (hierarchy) behind simple interface.

![img](https://refactoring.guru/images/patterns/diagrams/facade/structure.png)

##### Flyweight
Just a fancy cache.
Let's say you have ton of similar objects. You are creating a Flyweight (factory) that
responsible for creatint objects with given parameters, but before return such object it
saves it to underlying array and next time you'll need it, Flyweight
will get it from that array.

#### Behavioral
##### Chan of responisibility
Allows pass requests along a chain of handlers. Upon receiving a request, each handler
decides either to process the request or to pass it to the next handler in the chain.

- If one part of chain fails, no further processing performs.
- Different way - stop processing on success.

##### Command
Sender -> Command -> Receiver

Creates middle layer set of commands where every command represented by its own class
with common interface. (usually with one method “Execute()”) It requires additional layer
to bound Commands to Senders. On initialization, Command gets Receiver’s instance.
After that command may be assigned to Sender. Sender stores a list of Commands, and methods
to add, remove and execute them, this allows dinamically change Sender’s behavior.

```
Senders
[] [] [] [] [] []
    Commands
    [] [] []
       Receiver
       []
```

##### Iterator
Moves iteration logic to its own object. Allows to easily implement different iteration
methods for COMPLEX DATA.

There is usually just interface with methods like `Next()`, `HasNext()`, `GetValue()` etc.

I guess Go's `sort.Interface` is pretty fits into this pattern. (But actually not really)

```go
// A type, typically a collection, that satisfies sort.Interface can be
// sorted by the routines in this package. The methods require that the
// elements of the collection be enumerated by an integer index.
type Interface interface {
	// Len is the number of elements in the collection.
	Len() int
	// Less reports whether the element with
	// index i should sort before the element with index j.
	Less(i, j int) bool
	// Swap swaps the elements with indexes i and j.
	Swap(i, j int)
}
```

##### Mediator
This one is FAAAT.

The pattern restricts direct communications between the objects and forces them to collaborate only via a mediator object.

Mediator contains all initialized objects that has to communicate with each other and each object contains Mediator's instance.

The Mediator is about the interactions between "colleague" objects who don't know each other.
encapsulates the interaction between several colleague objects in order to isolate them from each other.

![img](https://refactoring.guru/images/patterns/diagrams/mediator/structure.png)

##### Memento
UNDO

Lets save and restore the previous state of an object.

##### Observer
Lets you define a subscription mechanism to notify multiple objects about any events that happen to the object they’re observing.

##### State
Lets an object alter its behavior when its internal state changes. It appears as if the object changed its class.

There is method like `SetState(IState)` and everything method does goes through it's state.

E.g phone call mode (silent, vibration, sound).

##### Strategy
Ugh the same as state.

But you are changing implementation.

- The Strategy pattern is really about having a different implementation that accomplishes (basically) the same thing, so that one implementation can replace the other as the strategy requires. For example, you might have different sorting algorithms in a strategy pattern. The callers to the object does not change based on which strategy is being employed, but regardless of strategy the goal is the same (sort the collection).
- The State pattern is about doing different things based on the state, while leaving the caller relieved from the burden of accommodating every possible state. So for example you might have a getStatus() method that will return different statuses based on the state of the object, but the caller of the method doesn't have to be coded differently to account for each potential state.

- The Strategy pattern deals with HOW an object performs a certain task -- it encapsulates an algorithm.
- The State pattern deals with WHAT (state or type) an object is (in) -- it encapsulates state-dependent behavior, whereas

##### Template Method
Defines interface. Defines base struct with methods common for all objects that implements that interface.
Every method overrides SOME of the methods (that should differ).

##### Visitor
You have bunch of objects with common interface. Each object need to get one more method but without changing actual class.
You define Visitor interface that have additional method for each object. Then you define method
Accept(Visitor) for each class (yep, you actually changing it). And each Accept just calls the method it needs.

```go
type Visitor interface {
	VisitSquare()
	VisitSircle()
}

type Shape interface {
	Accept(Visitor)
}

type Square struct{}
func (s Square) Accept(v Visitor) {
	v.VisitSquare()
}

type Sircle struct{}
func (s Sircle) Accept(v Visitor) {
	v.VisitSircle()
}

func main() {
	var v = new(Visitor) // here must be implementation instead of interface
	var s = new(Square)
	s.Accept(v)
}
```

### SOLID
#### SRP: The Single Responsibility Principle
- A module should be responsible to one, and only one, actor.
- Old: A module should have one, and only one, reason to change.

One class should solve only one problem.

#### OCP: The Open Closed Principle
- A software artifact should be open for extension but closed for modification.
- Old: You should be able to extend a classes behavior, without modifying it.

Module declares only interface WITHOUT realiztion. Realization defined in other module.
Thus you are able to use different realization without recompilcation.

And when you'll need to change behaviour you can just implement another interface.

```go
type GetInfo interface {
    Get()
}

type GetInfoFrom3rdPartyAPI struct {}
type GetInfoFromDatabase struct {}
type GetInfoFromCache struct {}
```

#### LSP: The Liskov Substitution Principle
Any interface's realization should be interchengeable in any place.

#### ISP: The Interface Segregation Principle
**Interfaces granularity**

Don't force clients to implement interface they don't use.

#### DIP: The Dependency Inversion Principle
- Depend on abstractions, not on concretions.

- Модули верхних уровней не должны зависеть от модулей нижних уровней. Оба типа модулей должны зависеть от абстракций.
- Абстракции не должны зависеть от деталей. Детали должны зависеть от абстракций.
(pohuy)

### Anti patterns
https://en.wikipedia.org/wiki/Anti-pattern
- magic ints
- hardcode
- repeating
- globals
- circular dependencies
- spagetti code

There is ton of them..

### Layered architecture
Four isolated layers:

- The presentation layer
- The business layer
- The persistence layer
- The database layer

The layers are closed, meaning a request must go through all layers from top to bottom.


## DB Design
## Modeling
## Security
## Algorithms

# Core
## Programming language :bulb:
### Interfaces: Interface Values, Type Assertions
https://research.swtch.com/interfaces
https://github.com/teh-cmc/go-internals/blob/master/chapter2_interfaces/README.md

Interface is an abstract data type, that doesn't expose the representation
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
                     // (describes if interface is implemented) (but i'm not sure)
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

### Stack and heap
https://segment.com/blog/allocation-efficiency-in-high-performance-go-services/

Heap - global programm memory. If a function creates a variable and returns reference to it,
the variable allocates in heap.

Stack - local memory allocated per function. It has its own top that moves up and down for each nested
call. This memory is freed once function is returned.

Go prefers allocation on the stack — most of the allocations within a given Go program will be on the
stack. It’s cheap because it only requires two CPU instructions: one to push onto the stack for allocation,
and another to release from the stack. On the other side, heap allocations are expensive, `malloc` has to
search for a chunk of free memory large enough to hold the new value and the garbage collector
scans the heap for objects which are no longer referenced.

Compiler performs `escape analysis` - set of rules that variable must pass on compilation stage
to be allocated in stack. Stack allocation requires that the lifetime and memory footprint of a
variable can be determined at compile time.

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

### High ordered functions
A Higher-Order function is a function that receives a function as an argument or returns the function as output.
Higher order functions are functions that operate on other functions, either by taking them as arguments or by returning them.

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

`buf` - array with size
`closed` - flag
`lock` - embedded structure
`send` and `recvq` - sudog sudog represents a g in a wait list, such as for
sending/receiving on a channel.

```go
// allocates hchan in heap
func makechan(t *chantype, size int64) *hchan {/*...*/}
```

`makechan` returns pointer to channel, so it's possible to pass them as it is.

While send and receive operations it acquires a lock, performs operations under `buf` array
and releases the lock.

### Reflection: Rules of usage, reflect.Type, reflect.Value, Struct tags
https://blog.golang.org/laws-of-reflection

Reflection in computing is the ability of a program to examine its own structure, particularly through types.
A variable of interface type stores a pair: the concrete value assigned to the variable, and that value's type descriptor. To be more precise, the value is the underlying concrete data item that implements the interface and the type describes the full type of that item.

```go
func TypeOf(i interface{}) Type
func ValueOf(i interface{}) Value
```
`reflect.Type` and `reflect.Value` are interfaces describing type and value of interface.
Theese two are just examining interface's descriptor `itab *itable` to find out the undelying type and it's value.
Passing non-interface value to theese function just converts them to empty `interface{}` and then does the job.

https://github.com/golang/go/blob/master/src/reflect/type.go

1) Reflection goes from interface value to reflection object.
2) Reflection goes from reflection object to interface value.
3) To modify a reflection object, the value must be settable.
Settable value - pointer value.

### Blank identifier
`_` place holder where value is not needed.

### Dependencie injection
Pattern bridge.

Dependency injection (DI) is a style of writing code such that the dependencies of a particular object (or, in go, a struct) are provided at the time the object is initialized. Object is not responsible to initialize dependencies.

### io.Reader, io.Writer, sort.Interface, error interfaces
https://github.com/golang/go/blob/master/src/io/io.go
```go
// Read reads up to len(p) bytes into p. It returns the number of bytes
// read (0 <= n <= len(p)) and any error encountered. Even if Read
// returns n < len(p), it may use all of p as scratch space during the call.
// If some data is available but not len(p) bytes, Read conventionally
// returns what is available instead of waiting for more.
type Reader interface {
	Read(p []byte) (n int, err error)
}

// Write writes len(p) bytes from p to the underlying data stream.
// It returns the number of bytes written from p (0 <= n <= len(p))
// and any error encountered that caused the write to stop early.
// Write must return a non-nil error if it returns n < len(p).
// Write must not modify the slice data, even temporarily.
//
// Implementations must not retain p.
type Writer interface {
	Write(p []byte) (n int, err error)
}
```

https://github.com/golang/go/blob/master/src/sort/sort.go
```go
// A type, typically a collection, that satisfies sort.Interface can be
// sorted by the routines in this package. The methods require that the
// elements of the collection be enumerated by an integer index.
type Interface interface {
	// Len is the number of elements in the collection.
	Len() int
	// Less reports whether the element with
	// index i should sort before the element with index j.
	Less(i, j int) bool
	// Swap swaps the elements with indexes i and j.
	Swap(i, j int)
}
```

### Errors:
```go
type error interface {
    Error() string
}
```

### Env variables
```go
func Getenv(key string) string
```
```go
func Setenv(key, value string) error
```

### System processes: spawning and terminating, collecting output
https://github.com/golang/go/blob/master/src/os/exec/exec.go
```go
func main() {
	cmd := exec.Command("ps", "aux") // create cmd
	_ = cmd.Start()                  // start process

	in, _ := cmd.StdinPipe()   // to write input
	out, _ := cmd.StdoutPipe() // to read output

	defer in.Close()
	defer out.Close()

	_ = cmd.Wait() // wait until cmd is done
}
```

### Effective go notes
- Go doesn't provide automatic support for getters and setters. There's nothing wrong with providing getters and setters yourself, and it's often appropriate to do so, but it's neither idiomatic nor necessary to put Get into the getter's name. If you have a field called owner (lower case, unexported), the getter method should be called Owner (upper case, exported), not GetOwner. The use of upper-case names for export provides the hook to discriminate the field from the method. A setter function, if needed, will likely be called SetOwner. Both names read well in practice:

```go
owner := obj.Owner()
if owner != user {
    obj.SetOwner(user)
}
```
(getters without `Get-`, but setters with `Set-`)

- By convention, one-method interfaces are named by the method name plus an -er suffix or similar modification to construct an agent noun: Reader, Writer, Formatter, CloseNotifier etc.

- Switches has `break`

- Last defer executes first
```go
for i := 0; i < 5; i++ {
    defer fmt.Printf("%d ", i)
}
// prints 4, 3, 2, 1
```

- Arguments to deferred functions are evaluated when the defer executes.
```go
func main() {
	var x = 10
	defer fmt.Println(x)
	x = 20
}

// prints 10
```

- If a type exists only to implement an interface and will never have exported methods beyond that interface, there is no need to export the type itself.

- Import for side effects `import _ "net/http/pprof"` e.g. for it's init function.

- Do not communicate by sharing memory; instead, share memory by communicating.
One way to think about this model is to consider a typical single-threaded program running on one CPU. It has no need for synchronization primitives. Now run another such instance; it too needs no synchronization. Now let those two communicate; if the communication is the synchronizer, there's still no need for other synchronization. Unix pipelines, for example, fit this model perfectly.

- Just nice peace of code
```go
c := make(chan int)  // Allocate a channel.
// Start the sort in a goroutine; when it completes, signal on the channel.
go func() {
    list.Sort()
    c <- 1  // Send a signal; value does not matter.
}()
doSomethingForAWhile()
<-c   // Wait for sort to finish; discard sent value.
```

## Tools & Ecosystem :bulb:
### Escape analysis
`go run -gcflags '-m' main.go` - shows escape analysis details

## Go Concurrency :bulb:
### WaitGroup
https://github.com/golang/go/blob/master/src/sync/waitgroup.go
`sync.WaitGroup (struct)`

```go
wg.Add(1)
wg.Done()
wg.Wait()
```

### Shared Memory
In computer science, shared memory is memory that may be simultaneously accessed by multiple programs with an intent to provide communication among them or avoid redundant copies. Shared memory is an efficient means of passing data between programs. Depending on context, programs may run on a single processor or on multiple separate processors.

Using memory for communication inside a single program, e.g. among its multiple threads, is also referred to as shared memory.

### Race Detector (Memory synchronization, Lazy initialisation)
https://blog.golang.org/race-detector

Race conditions are among the most insidious and elusive programming errors. They typically cause erratic and mysterious failures, often long after the code has been deployed to production. While Go's concurrency mechanisms make it easy to write clean concurrent code, they don't prevent race conditions. Care, diligence, and testing are required. And tools can help.

The race detector is based on the C/C++ ThreadSanitizer runtime library, which has been used to detect many errors in Google's internal code base and in Chromium. The technology was integrated with Go in September 2012; since then it has detected 42 races in the standard library. It is now part of our continuous build process, where it continues to catch race conditions as they arise.

- memory sync:
> an unsynchronized read and write of the variable t from different goroutines.
that's the race condition^ and should be avoided

## Networking :bulb:

# Back-End
## Web and Application Servers
## Cloud-based Deployment Services

# DB
## SQL :bulb:
## NoSQL
## Database (practice) :bulb:

# Verification
## Code quality
## Refactoring
## Tests, Trace, Profile :bulb:
### Benchmark
### Profiling concepts
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

# Configuration Management
## Product builds and Continuous Integration
## Managing versions
