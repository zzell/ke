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
Defines an interface that allows to create different implementations of object (interface)
without exposing the creation logic to client.

![img](https://refactoring.guru/images/patterns/diagrams/factory-method/structure.png)

```go
type OAuth2 interface {
	Authorize(creds ...string) error
}

type ClientCredentials struct{}
func (ClientCredentials) Authorize() error { /*..*/ }

type AuthCode struct{}
func (AuthCode) Authorize() error { /*..*/ }

type Password struct{}
func (Password) Authorize() error { /*..*/ }

type OAuthFactory interface {
	Create() OAuth2
}

type OAuth2ClientCredentials struct{}
func (OAuth2ClientCredentials) Create() OAuth2 { /*..*/ }

type OAuth2AuthCode struct{}
func (OAuth2AuthCode) Create() OAuth2 { /*..*/ }

type OAuthPassword struct{}
func (OAuthPassword) Create() OAuth2 { /*..*/ }

func Authorize(method string, creds ...string) error {
	var auth OAuth2

	switch method {
	case "client credentials":
		auth = OAuth2ClientCredentials{}.Create()
	case "auth code":
		auth = OAuth2AuthCode{}.Create()
	case "password":
		auth = OAuthPassword{}.Create()
	}

	return auth.Authorize(creds...)
}
```

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
