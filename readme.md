# Engineering Management
## Process Planning (SDLC)
## Estimation

# Requirements
## Software Requirements Engineering

# Design
## OOD
### GoF Design Patterns
#### Creational
##### Singleton
Asserts that the whole application will have single instance of certain object
and provides global access point to that object.
The access to the object goes through the "GetInstance" method.

Just a fancy global - unsafe and has side effects, hides the dependencies of
application in code instead of exposing them

![img](https://refactoring.guru/images/patterns/diagrams/singleton/structure-en.png)

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

![img](https://refactoring.guru/images/patterns/diagrams/factory-method/structure.png)

##### Builder
Allows to create objects with different options. Every Builder's method
returns Builder itself, so it's convenient to chain methods. There is
also might be a Director - struct that contains builder and has method "Construct"
that creates Builder's instance with predefined options, but this is optional.

![img](https://refactoring.guru/images/patterns/diagrams/builder/structure.png)

##### Prototype
Allows to copy object with it's state. Defines an interface with method "Clone"
that returns same interface. Under the hood implementation creates new instance with
same fields as Prototype.

![img](https://refactoring.guru/images/patterns/diagrams/prototype/structure-prototype-cache.png)

##### Abstract Factory
Just like Factory Method, AF defines an interface, but instead of single method it has
bunch of methods like "CreateCarcass", "CreateEngine", "CreateWheel" of course
it has to be implemented in various ways (sportcar, pickup, truck).

The main point is that end users are not aware of what exact implementation they use.
Instead, correct concrete factory will be chosen at runtime at the initialization stage.
The app must select the factory type depending on the configuration or the environment settings.

![img](https://refactoring.guru/images/patterns/diagrams/abstract-factory/structure.png)

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
type Adaptee struct {
}

// NewAdapter is the Adapter constructor.
func NewAdapter(adaptee *Adaptee) Target {
	return &Adapter{adaptee}
}

// SpecificRequest implementation.
func (a *Adaptee) SpecificRequest() string {
	return "Request"
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

![img](https://refactoring.guru/images/patterns/diagrams/adapter/structure-object-adapter.png)
![img](https://refactoring.guru/images/patterns/diagrams/adapter/structure-class-adapter.png)

##### Bridge
Mockery
Lets split a large class or a set of closely related classes into separate
hierarchies-abstraction and implementation which can be developed independently of each other.

Structure contains different objects over composition and interacts with them. Use the pattern
when you need to extend a class in several orthogonal (independent) dimensions.

![img](https://refactoring.guru/images/patterns/diagrams/bridge/structure-en.png)

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

![img](https://refactoring.guru/images/patterns/diagrams/decorator/structure.png)

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

![img](https://refactoring.guru/images/patterns/diagrams/proxy/structure.png)

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
lets pass requests along a chain of handlers. Upon receiving a request, each handler
decides either to process the request or to pass it to the next handler in the chain.

- If one part of chain fails, no further processing performs.
- Different way - stop processing on success.

![img](https://refactoring.guru/images/patterns/diagrams/chain-of-responsibility/structure.png)

## DB Design
## Modeling
## Security
## Algorithms

# Core
## Programming language
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

## Tools & Ecosystem
## Go Concurrency
## Networking

# Back-End
## Web and Application Servers
## Cloud-based Deployment Services

# DB
## SQL
## NoSQL
## Database (practice)

# Verification
## Code quality
## Refactoring
## Tests, Trace, Profile
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
