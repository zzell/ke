# GAPS

## SQL
### DDL, DML, DCL
- DML - Data Manipulation Language.\
`SELECT` - Retrieves data from a table\
`INSERT` - Inserts data into a table\
`UPDATE` - Updates existing data into a table\
`DELETE` - Deletes all records from a table

- DDL - Data Definition Language.\
`CREATE` – Creates objects in the database\
`ALTER` – Alters objects of the database\
`DROP` – Deletes objects of the database\
`TRUNCATE` – Deletes all records from a table and resets table identity to initial value.

- DCL - Data Control Language.\
`GRANT` – Gives user's access privileges to database\
`REVOKE` – Withdraws user's access privileges to database given with the GRANT command

- TCL - Transactional Control Language.\
`COMMIT` – Saves work done in transactions\
`ROLLBACK` – Restores database to original state since the last COMMIT command in transactions\
`SAVE TRANSACTION` – Sets a savepoint within a transaction

- exclude join query
- aggregations
- Combining the results of multiple queries
- Difference between SQL and NoSQL transactions.
- Transaction in distributed systems.

### Stored procedure
A stored procedure is a prepared SQL code that you can save, so the code can be reused 
over and over again. So if you have an SQL query that you write over and over again, save 
it as a stored procedure, and then just call it to execute it. You can also pass parameters 
to a stored procedure, so that the stored procedure can act based on the parameter 
value(s) that is passed.

```sql
CREATE PROCEDURE SelectAllCustomers @City nvarchar(30)
AS
SELECT * FROM Customers WHERE City = @City
GO;

# usage:
EXEC SelectAllCustomers @City = "London";
```

### User-defined functions
A user-defined function (UDF) is a way to extend MySQL with a new function that works like
a native (built-in) MySQL function such as ABS() or CONCAT(). To create a function, you must 
have the INSERT privilege for the mysql system database. This is necessary because CREATE 
FUNCTION adds a row to the `mysql.func` system table that records the function's name, type, 
and shared library name. \
Function is compiled and executed every time it is called. And cannot modify the data 
received as parameters and function must return a value. Functions are similar to Stored
procedures, but with following differences:

| Function                                                                               | Stored procedure                                                                                      |
|----------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| The function always returns a value.                                                   | Procedure can return “0” or n values.                                                                 |
| Functions have only input parameters for it.                                           | Procedures can have output or input parameters.                                                       |
| You can call Functions from Procedure.                                                 | You can’t call Procedures from a Function.                                                            |
| You can’t use Transactions in Function.                                                | You can use Transactions in Procedure.                                                                |
| You can’t use try-catch block in a Function to handle the exception.                   | By using a try-catch block, an exception can be handled in a Procedure.                               |
| Function can be utilized in a SELECT statement.                                        | You can’t utilize Procedures in a SELECT statement.                                                   |
| Function allows only SELECT statement in it.                                           | The procedure allows as DML(INSERT/UPDATE/DELETE) as well as a SELECT statement in it.                |
| The function can be used in the SQL statements anywhere in SELECT/WHERE/HAVING syntax. | Stored Procedures cannot be used in the SQL statements anywhere in the WHERE/HAVING/SELECT statement. |

```sql
CREATE FUNCTION function_name(param1, param2)
RETURNS datatype
BEGIN
 -- statements
END
```

### Joins 
#### INNER JOIN
```sql
SELECT Orders.OrderID, Customers.CustomerName
FROM Orders
INNER JOIN Customers ON Orders.CustomerID = Customers.CustomerID;
```

#### LEFT JOIN
Fetches everything from left table and matched records from right, 
and non-matched values will be null.
```sql
SELECT Customers.CustomerName, Orders.OrderID
FROM Customers
LEFT JOIN Orders
ON Customers.CustomerID=Orders.CustomerID
ORDER BY Customers.CustomerName;
```

```sql
maria=# select * from districts left join bydlos on districts.id = bydlos.district_id where bydlos.id is null;         
 id | street  | crime | id | name | favourite_beer | district_id 
----+---------+-------+----+------+----------------+-------------
  3 | Kyivska |     2 |    |      |                |            
(1 row)
```

#### RIGHT JOIN
The RIGHT JOIN keyword returns all records from the right table (table2), 
and the matched records from the left table (table1). The result is NULL 
from the left side, when there is no match.

#### FULL JOIN
Fetches everything from both tables + matches.

```sql
maria=# select * from bydlos full join districts on bydlos.name = districts.street;
 id |  name  | favourite_beer | district_id | id |  street   | crime 
----+--------+----------------+-------------+----+-----------+-------
    |        |                |             |  1 | Bandery   |     8
  3 | Kaban  | Baltyke 8      |           2 |    |           |      
    |        |                |             |  3 | Kyivska   |     2
    |        |                |             |  2 | Lyubinska |     2
  1 | Sanya  | Obolon         |           1 |    |           |      
  2 | Shakal | Chernigivske   |           1 |    |           |      
(6 rows)
```

### UNION
The UNION operator is used to combine the result-set of two or more SELECT statements.

- Each SELECT statement within UNION must have the same number of columns
- The columns must also have similar data types
- The columns in each SELECT statement must also be in the same order

```sql
SELECT City FROM Customers
UNION
SELECT City FROM Suppliers
ORDER BY City;
```

### GROUP BY 
The GROUP BY statement groups rows that have the **same values** into summary rows, like "find the number of customers in each country".

```sql
SELECT count(CustomerID), Country
FROM Customers
GROUP BY Country;
```

```sql
SELECT Shippers.ShipperName, COUNT(Orders.OrderID) AS NumberOfOrders FROM Orders
LEFT JOIN Shippers ON Orders.ShipperID = Shippers.ShipperID
GROUP BY ShipperName;
```

### HAVING
The HAVING clause was added to SQL because the WHERE keyword could not be used with aggregate functions.

```sql
SELECT COUNT(CustomerID), Country
FROM Customers
GROUP BY Country
HAVING COUNT(CustomerID) > 5;
```

### AGGREGATIONS
SQL aggregate functions return a single value, calculated from values in a column.

- AVG() - Returns the average value
- COUNT() - Returns the number of rows
- FIRST() - Returns the first value
- LAST() - Returns the last value
- MAX() - Returns the largest value
- MIN() - Returns the smallest value
- SUM() - Returns the sum

### Isolation levels
**I in ACID**

https://www.geeksforgeeks.org/transaction-isolation-levels-dbms/\
Isolation levels define the degree to which a transaction must be isolated from the data 
modifications made by any other transaction in the database system. (Rules for acquiring a LOCK.)

- `dirty reads` - read data while another uncommitted write.
- `non-repeatable reads` - read data few times in single transaction, 
but another transaction is committed update between reads.
- `phantom reads` - same as above, but another transaction commits adding new row. 


- **Read uncommitted** permits dirty reads, non repeatable reads and phantom reads.
- **Read committed** permits non repeatable reads and phantom reads.
- **Repeatable read** permits only phantom reads.
- **Serializable** does not permit any read errors.

 To set the global isolation level at server startup, use the `--transaction-isolation=level`
option on the command line or in an option file.

### Session
http://go-database-sql.org/connection-pool.html

- Connection pooling means that executing two consecutive statements on a single database might open two connections and execute them separately. It is fairly common for programmers to be confused as to why their code misbehaves. For example, LOCK TABLES followed by an INSERT can block because the INSERT is on a connection that does not hold the table lock.
- Connections are created when needed and there isn’t a free connection in the pool.
- By default, there’s no limit on the number of connections. If you try to do a lot of things at once, you can create an arbitrary number of connections. This can cause the database to return an error such as “too many connections.”
- In Go 1.1 or newer, you can use db.SetMaxIdleConns(N) to limit the number of idle connections in the pool. This doesn’t limit the pool size, though.
- In Go 1.2.1 or newer, you can use db.SetMaxOpenConns(N) to limit the number of total open connections to the database. Unfortunately, a deadlock bug (fix) prevents db.SetMaxOpenConns(N) from safely being used in 1.2.
- Connections are recycled rather fast. Setting a high number of idle connections with db.SetMaxIdleConns(N) can reduce this churn, and help keep connections around for reuse.
- Keeping a connection idle for a long time can cause problems (like in this issue with MySQL on Microsoft Azure). Try db.SetMaxIdleConns(0) if you get connection timeouts because a connection is idle for too long.
- You can also specify the maximum amount of time a connection may be reused by setting db.SetConnMaxLifetime(duration) since reusing long lived connections may cause network issues. This closes the unused connections lazily i.e. closing expired connection may be deferred.

### Locks
Lock single ROW!
```sql
BEGIN TRAN

   UPDATE your_table WITH (ROWLOCK)
   SET your_field = a_value
   WHERE <a predicate>

COMMIT TRAN
```

A READ|WRITE locks has the following features:
- The only session that holds the lock of a table can read/write data from the table.
- Other sessions cannot read data from and write data to the table until the WRITE lock is released.

```sql
LOCK TABLES table_name [READ | WRITE]
```

++ **lock on isolation level**

### Transaction
Ensures that all operations within the work unit are completed successfully. Otherwise, 
the transaction is aborted at the point of failure and all the previous operations are
rolled back to their former state.

- `START TRANSACTION` or `BEGIN` start a new transaction.
- `COMMIT` - to save the changes.
- `ROLLBACK` - to roll back the changes.
- `SAVEPOINT` - creates points within the groups of transactions in which to ROLLBACK.
- `SET TRANSACTION` - Places a name on a transaction.

Database writes info about transaction into persistent log, in case of failure DB reads log backward and revert changes.

#### Transaction in distributed systems
2PC - Two phase commit protocol
1) Voting phase: 
Transaction manager sends transaction to all replicas.
Replicas write transaction info into their log and send OK.

2) Commit phase:
When TM Receives OK from all slaves it sends message to commit.
After every replica commits it sends OK to TM.
If one replica sent NOT OK - TM sends msg to every replica to rollback.

### ACID
- Atomicity: Transaction should be executed or or not.
- Consistency
- Isolation: Concurrent transaction should't impact to one another.
- Durability

## Algorithms
https://www.bigocheatsheet.com/
### Big O notation
Big O notation is used in Computer Science to describe the performance or complexity of an algorithm. 

#### O(1)
O(1) describes an algorithm that will always execute in the same time (or space) regardless of the size of the input data set.
```
bool IsFirstElementNull(IList<string> elements) {
    return elements[0] == null;
}
```

#### O(N)
O(N) describes an algorithm whose performance will grow linearly and in direct proportion to the size of the input data set.
```
bool ContainsValue(IList<string> elements, string value) {
    foreach (var element in elements)
    {
        if (element == value) return true;
    }

    return false;
}
```
#### O(N^2)
O(N^2) represents an algorithm whose performance is directly proportional to the square of the size of the input
data set. This is common with algorithms that involve nested iterations over the data set. 
Deeper nested iterations will result in O(N3), O(N4) etc.

```
bool ContainsDuplicates(IList<string> elements) {
    for (var outer = 0; outer < elements.Count; outer++)
    {
        for (var inner = 0; inner < elements.Count; inner++)
        {
            // Don't compare with self
            if (outer == inner) continue;

            if (elements[outer] == elements[inner]) return true;
        }
    }

    return false;
}
```
#### O(2^N)
Usually recursive algorithm. (Exponential)
O(2^N) denotes an algorithm whose growth doubles with each addition to the input data set.
```
int Fibonacci(int number) {
    if (number <= 1) return number;
    return Fibonacci(number - 2) + Fibonacci(number - 1);
}
```

#### O(log N)
Doubling the size of the input data set has little effect on its growth as after a single
iteration of the algorithm the data set will be halved and therefore on a par with an input 
data set half the size. This makes algorithms like binary search extremely efficient when 
dealing with large data sets.

### Binary search [O(log N)] (TODO)
Binary search is an efficient algorithm for finding an item from a sorted list of items.

Binary search is a technique used to search sorted data sets. It works by selecting the middle element of 
the data set, essentially the median, and compares it against a target value. If the values match it 
will return success. If the target value is higher than the value of the probe element it will take the 
upper half of the data set and perform the same operation against it. Likewise, if the target value is 
lower than the value of the probe element it will perform the operation against the lower half. It will 
continue to halve the data set with each iteration until the value has been found or until 
it can no longer split the data set.

### Array sorting methods (TODO: MORE ALGORITHMS)
#### Quicksort
1) Peek a pivot (usually last elem in array).
2) Go through array and compare every element in it with pivot.
3) If element is bigger - move it to the right of pivot.
4) If element is smaller - go to the next one.
5) Recursively repeat on every subarray.

#### Bubble sort
1) Go from the beginning of array 
2) Compare two elements
3) Swap them if needed
4) Go on
5) At the end of iteration largest element will be on the right
6) Repeat

#### Hash function collision resolving
Linear probing

https://en.wikipedia.org/wiki/Linear_probing

When the hash function causes a collision by mapping a new key to a cell of the hash table that is already occupied by another key, linear probing searches the table for the closest following free location and inserts the new key there. Lookups are performed in the same way, by searching the table sequentially starting at the position given by the hash function, until finding a cell with a matching key or an empty cell.

## Tests, Trace, Profile	
- flappy tests, go example
- how to perform benchmarks in the right way (CPU, heat, other tools)
- F.I.R.S.T.

## Code Quality	
- Common Software measurements: Coupling, Cohesion, Number of lines of code
- Program execution time
- Best practices for code review
- Code smells

## Cloud-based Deployment Services	

## Product Builds and Continuous Integration
- Levels of Requirements

## Process Planning (SDLC)
- Agile software development concept

## Estimation
- Overestimate vs Underestimate

## Software Requirements	
- Levels of Requirements


___
___
___



# Design
## OOD
### GoF Design Patterns
#### Creational
##### Singleton
Asserts that the whole application will have single instance of certain object
and provides global access point to that object. The access to the object goes 
through the "GetInstance" method.

##### Factory Method
Defines an interface (with method "Create") that allows to create different
implementations of object.

##### Abstract Factory
Defines an interface with bunch of methods like `CreateCarcass`, `CreateEngine`,
`CreateWheels`, it can be implemented in various ways (sportcar, pickup, truck). \
The main point is that end users are not aware of what exact implementation they use.
Instead, correct concrete factory will be chosen at runtime at the initialization stage.
The app must select the factory type depending on the configuration or the environment settings.
(e.g. when writing GUI application choose btw `WinWindow`, `MacWindow`, `GnomeWindow`)

##### Builder
Allows to create objects with different options. Every Builder's method
returns Builder itself, so it's convenient to chain methods. There is
also might be a Director - struct that contains builder and has method "Construct"
that creates Builder's instance with predefined options, but this is optional.

##### Prototype
Allows to copy object with it's state. Defines an interface with method "Clone"
that returns same interface. Under the hood implementation creates new instance with
same fields as Prototype.

#### Structural
##### Adapter
TODO: too fuzzy

Transforms object of one type to another. Adapter doesn't have any strict 
form (interface or something) and can be implemented in various ways.

- Struct that contains existing and target objects via composition
and overrides target object's methods, then access to the target performs through
that struct.
- Can be a struct that implements target interface and contains existing. (most popular)
- Can be a simple function that accepts existing and returns target.

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
**Dependency injection.** \
Lets split a large class or a set of closely related classes into separate
hierarchies-abstraction and implementation which can be developed independently of each other.
Resulting structure contains different objects over composition and interacts with them. Use the pattern
when you need to extend a class in several orthogonal (independent) dimensions.

##### Composite
**Filetree-like** \
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
Lets attach new behaviors to objects by placing these objects inside the special 
wrapper object that contains behavior.

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
Similar to Decorator, but Decorator gets reference for decorated object (usually through constructor)
while Proxy responsible to do that by itself. Proxy also implements objects interface.

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
Hides complex logic (hierarchy) behind simple interface.

##### Flyweight
**Cache** \
Let's say you have ton of similar objects. You are creating a Flyweight (factory) that
responsible for creating objects with given parameters, but before return such object it
saves it to underlying array and next time you'll need it, Flyweight
will get it from that array.

#### Behavioral
##### Chan of responsibility
**Pipeline**\
Allows pass requests along a chain of handlers. Upon receiving a request, each handler
decides either to process the request or to pass it to the next handler in the chain.\
There are two possible implementations:
- If one part of chain fails, no further processing performs.
- Different way - stop processing on success.

##### Command
Sender -> Command -> Receiver

Creates middle layer set of commands where every command represented by its own class
with common interface. (usually with one method `Execute()`) It requires additional layer
to bound Commands to Senders. On initialization, Command gets Receiver’s instance.
After that command may be assigned to Sender. Sender stores a list of Commands, and methods
to add, remove and execute them, this allows to dynamically change Sender’s behavior.

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
methods for COMPLEX DATA. There is usually just interface with methods like `Next()`,
`HasNext()`, `GetValue()` etc.

##### Mediator
The pattern restricts direct communications between the objects and forces them to collaborate
only via a mediator object. Mediator contains all initialized objects that has to communicate
with each other and each object contains Mediator's instance.

The Mediator is about the interactions between "colleague" objects who don't know each other.
encapsulates the interaction between several colleague objects in order to isolate them from each other.

![img](https://refactoring.guru/images/patterns/diagrams/mediator/structure.png)

##### Observer
Lets you define a subscription mechanism to notify multiple objects about any events 
that happen to the object they’re observing.

##### Memento
**UNDO**\
Lets save and restore the previous state of an object.

##### State
Lets an object alter its behavior when its internal state changes. It appears as if the object
changed its class. There is method like `SetState(IState)` and everything method does goes through
it's state.

E.g phone call mode (silent, vibration, sound).

##### Strategy
Similar to state.\
But you are changing implementation.

- The Strategy pattern is really about having a different implementation that accomplishes (basically)
the same thing, so that one implementation can replace the other as the strategy requires. For example,
you might have different sorting algorithms in a strategy pattern. The callers to the object does not
change based on which strategy is being employed, but regardless of strategy the goal is the same
(sort the collection).
- The State pattern is about doing different things based on the state, while leaving the caller
relieved from the burden of accommodating every possible state. So for example you might have
a getStatus() method that will return different statuses based on the state of the object,
but the caller of the method doesn't have to be coded differently to account for each potential state.
- The Strategy pattern deals with HOW an object performs a certain task -- it encapsulates an algorithm.
- The State pattern deals with WHAT (state or type) an object is (in) -- it encapsulates 
state-dependent behavior, whereas

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

Module declares only interface WITHOUT realization. Realization defined in other module.
Thus you are able to use different realization without recompilation.

And when you'll need to change behaviour you can just implement interface in another way.

```go
type GetInfo interface {
    Get()
}

type GetInfoFrom3rdPartyAPI struct {}
type GetInfoFromDatabase struct {}
type GetInfoFromCache struct {}
```

#### LSP: The Liskov Substitution Principle
Any interface's realization should be interchangeable in any place.

#### ISP: The Interface Segregation Principle
Don't force clients to implement interface they don't use.

#### DIP: The Dependency Inversion Principle
- Depend on abstractions, not on concretions.

**pohuy:**
- Модули верхних уровней не должны зависеть от модулей нижних уровней. Оба типа модулей должны 
зависеть от абстракций.
- Абстракции не должны зависеть от деталей. Детали должны зависеть от абстракций.

### Anti patterns
https://en.wikipedia.org/wiki/Anti-pattern
- magic ints
- hardcode
- repeating
- globals
- circular dependencies
- spaghetti code

### Layered architecture
Four isolated layers:

- The presentation layer
- The business layer
- The persistence layer
- The database layer

The layers are closed, meaning a request must go through all layers from top to bottom.

# Core
## Programming language
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

### Reflection: Rules of usage, reflect.Type, reflect.Value, Struct tags
https://blog.golang.org/laws-of-reflection\
https://github.com/golang/go/blob/master/src/reflect/type.go

1) Reflection goes from interface value to reflection object.
2) Reflection goes from reflection object to interface value.
3) To modify a reflection object, the value must be settable.
Settable value - pointer value.

Reflection in computing is the ability of a program to examine its own structure, particularly through types.
A variable of interface type stores a pair: the concrete value assigned to the variable, and that value's type descriptor. To be more precise, the value is the underlying concrete data item that implements the interface and the type describes the full type of that item.

```go
func TypeOf(i interface{}) Type
func ValueOf(i interface{}) Value
```

`reflect.Type` and `reflect.Value` are interfaces describing type and value of interface.
These two are just examining interface's descriptor `itab *itable` to find out the underlying
type and it's value. Passing non-interface value to these function just converts them to 
empty `interface{}`.

### io.Reader, io.Writer, sort.Interface, error...
https://gist.github.com/asukakenji/ac8a05644a2e98f1d5ea8c299541fce9

### Env variables
```go
// os package
func Getenv(key string) string
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
- Go doesn't provide automatic support for getters and setters. There's nothing wrong with providing
getters and setters yourself, and it's often appropriate to do so, but it's neither idiomatic nor 
necessary to put Get into the getter's name. If you have a field called owner (lower case, unexported),
the getter method should be called Owner (upper case, exported), not GetOwner. The use of upper-case
names for export provides the hook to discriminate the field from the method. A setter function,
if needed, will likely be called SetOwner. Both names read well in practice:

    ```go
    owner := obj.Owner()
    if owner != user {
        obj.SetOwner(user)
    }
    ```

- By convention, one-method interfaces are named by the method name plus an -er suffix or similar
modification to construct an agent noun: Reader, Writer, Formatter, CloseNotifier etc.

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

- If a type exists only to implement an interface and will never have exported methods beyond that
interface, there is no need to export the type itself.

- Import for side effects `import _ "net/http/pprof"` e.g. for it's init function.

- Do not communicate by sharing memory; instead, share memory by communicating.
One way to think about this model is to consider a typical single-threaded program running on one CPU.
It has no need for synchronization primitives. Now run another such instance; it too needs no 
synchronization. Now let those two communicate; **if the communication is the synchronizer**,
there's still no need for other synchronization. Unix pipelines, for example, fit this model perfectly.

## Tools & Ecosystem
### Escape analysis
`go run -gcflags '-m' main.go` - shows escape analysis details

## Go Concurrency
### WaitGroup
https://github.com/golang/go/blob/master/src/sync/waitgroup.go\
`sync.WaitGroup (struct)`:
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

### Concurrent vs Parallel 
- Concurrency is about dealing with few things at once. It's how program is built.
E.g. perform operations while access ext resource like http, i/o, syscall.
- Parallelism is about performing processes independently.

### Timeouts
```go
select {
  case err := <-c:
    // use err and reply
  case <-time.After(timeoutNanoseconds):
    // call timed out
}
```

### Concurrency patterns
**TODO:** https://sudo.ch/unizh/concurrencypatterns/ConcurrencyPatterns.pdf

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

## Networking
### TCP/IP
Transmission control protocol

TCP/IP is a system of protocols, and a protocol is a system of rules and procedures. 
The TCP/IP protocol system is divided into separate components that theoretically function 
independently from one another. Each component is responsible for a piece of the communication process.

Because of TCP/IP’s modular design, a vendor such as Microsoft does not have to build a completely 
different software package for TCP/IP on an optical-fiber network (as opposed to TCP/IP on an 
ordinary ethernet network). The upper layers are not affected by the different physical architecture;
only the Network Access layer must change.

```
|----------------------+--------------------|
|                      | Application Layer  |
| Application Layer    | Presentation Layer |
|                      | Session Layer      |
|----------------------+--------------------|
| Transport Layer      | Transport Layer    |
|----------------------+--------------------|
| Internet Layer       | Network Layer      |
|----------------------+--------------------|
| Network Access Layer | Data Link Layer    |
|                      | Physical Layer     |
|----------------------+--------------------|
       TCP/IP                   OSI
```

- Physical layer: Converts the data into the stream of electric or analog pulses that will actually cross the transmission medium and oversees the transmission of the data; Provides an interface with the physical network. Formats the data for the transmission medium and addresses data for the subnet based on physical hardware addresses.
- Data Link layer: Provides an interface with the network adapter; maintains
logical links for the subnet
- Network layer: Supports logical addressing and routing
- Transport layer: Provides error control and flow control for the internetwork **TCP or UDP**
- Session layer: Establishes sessions between communicating applications on the communicating computers
- Presentation layer: Translates data to a standard format; manages encryption and data compression
- Application layer: Provides a network interface for applications; supports
network applications for file transfer, communications, and so forth

The data passes through all layers from top to down (or vice-versa when consuming), every 
layer adds some headers to data, and these headers are important on the next layer.

### UDP
User datagram protocol. (faster but not reliable)
- does not establish a session.
- does not guarantee data delivering.

### Application layer protocols
- HTTP: Hypertext Transfer Protocol. (1989) Used to transmit hypertext documents that include hyperlinks to other resources that the user can easily access. Request-response model. HTTP2 - полного сжатия данных, контроля трафика, инициации событий с сервера, переиспользования одного cокета для нескольких параллельных запросов.
- FTP: File Transfer Protocol. Used to send files between client and server over network. **predecessor of tcp/ip**. first written on 1971, later replaced by tcp/ip version on 1980
- SMTP: Simple mail transfer protocol. (1974) Used for sending emails between servers smtp.googe.com -> smtp.yahoo.go. Build on top of tcp/ip (port 25)

### WWW world wide web
**Information system** where documents and other web resources are identified by **Uniform Resource Locators (URL)**, which may be interlinked by hypertext, and are accessible over the Internet. The resources of the WWW are transfered via the Hypertext Transfer Protocol (HTTP) and may be accessed by users by a software application called a web browser and are published by a software application called a web server.

### HTTPS
**TLS: Transport layer security.**

All the data during communication is encrypted via **symmetric algorithm**, the key is created 
during handshake:
1. The 'client hello' message: The client initiates the handshake by sending a "hello" message to the server. The message will include which TLS version the client supports, the cipher suites (encryption algorithms) supported, and a string of random bytes known as the "client random."
2. The 'server hello' message: In reply to the client hello message, the server sends a message containing the server's SSL certificate, the server's chosen cipher suite, and the "server random," another random string of bytes that's generated by the server.
3. Authentication: The client verifies the server's SSL certificate with the certificate authority that issued it. This confirms that the server is who it says it is, and that the client is interacting with the actual owner of the domain.
4. The pre-master secret: The client sends one more random string of bytes, the "pre-master secret." The pre-master secret is encrypted with the public key and can only be decrypted with the private key by the server. (The client gets the public key from the server's SSL certificate.)
5. Private key used: The server decrypts the pre-master secret.
6. Session key created: Both client and server generate session key from the client random, the server random, and the pre-master secret. They should arrive at the same results.
7. Client sends encrypted message with "shared secret".
8. The server decrypts and verifies the message, then it sends another encrypted message to the client.
9. Client verifies the message.
10. Client is ready: The client sends a "finished" message that is encrypted with a session key.
11. Server is ready: The server sends a "finished" message encrypted with a session key.
12. Secure symmetric encryption achieved: The handshake is completed, and communication continues using the session keys.

### DNS Domain Name System
Hierarchical and decentralized naming system for computers, services, or other resources
connected to the Internet or a private network. It associates various information with domain
names assigned to each of the participating entities. Most prominently, it translates more 
readily memorized domain names to the numerical IP addresses needed for locating and identifying
computer services and devices with the underlying network protocols. 

# DB
## SQL Structured query langugae
### Tables, relationships, keys, constraints 
Primary key - is **unique** column (or set of columns) that is used to identify each row
in the table. If primary key contains from few columns - the may not be unique, but the set
of columns has to. Foreign key - the column or set of columns used to establish link between 
tables. It holds another's tables primary key.

Relationships:
- ONE TO ONE
- ONE TO MANY
- MANY TO MANY

### DDL, DML, DCL
- DML Data Manipulation Language.\
`SELECT` - Retrieves data from a table\
`INSERT` - Inserts data into a table\
`UPDATE` - Updates existing data into a table\
`DELETE` - Deletes all records from a table

- DDL Data Definition Language.\
`CREATE` – Creates objects in the database\
`ALTER` – Alters objects of the database\
`DROP` – Deletes objects of the database\
`TRUNCATE` – Deletes all records from a table and resets table identity to initial value.

- DCL Data Control Language.\
`GRANT` – Gives user's access privileges to database\
`REVOKE` – Withdraws user's access privileges to database given with the GRANT command

- TCL Transactional Control Language.\
`COMMIT` – Saves work done in transactions\
`ROLLBACK` – Restores database to original state since the last COMMIT command in transactions\
`SAVE TRANSACTION` – Sets a savepoint within a transaction

### Data types
https://www.postgresql.org/docs/9.5/datatype.html + User defined

### Operators, functions
https://www.w3schools.com/sql/sql_operators.asp
http://www-db.deis.unibo.it/courses/TW/DOCS/w3schools/sql/sql_functions.asp.html

### Joins
https://www.w3schools.com/sql/sql_join.asp

```sql
SELECT Orders.OrderID, Customers.CustomerName, Orders.OrderDate
FROM Orders
-- attach customer row if o.customerID == c.CustomerID
INNER JOIN Customers ON Orders.CustomerID=Customers.CustomerID;
```

![](https://i.imgur.com/v23nUwQg.png)

### Group By
https://www.w3schools.com/sql/sql_groupby.asp
```
SELECT COUNT(CustomerID), Country
FROM Customers
GROUP BY Country; # count how many customers leaves in each country
                  # without it it would just count all of the customers.
```

### Union
https://www.w3schools.com/sql/sql_union.asp
Combines the result of few SELECT statements.

### Creating, modifying, removing database objects
```sql
DROP DATABASE name;
CREATE DATABASE name;
```

### Session
Interaction unit between database server and client.
```sql
CONNECT user@database
```

### Transaction
Ensures that all operations within the work unit are completed successfully. Otherwise, 
the transaction is aborted at the point of failure and all the previous operations are
rolled back to their former state.

- `START TRANSACTION` or `BEGIN` start a new transaction.
- `COMMIT` - to save the changes.
- `ROLLBACK` - to roll back the changes.
- `SAVEPOINT` - creates points within the groups of transactions in which to ROLLBACK.
- `SET TRANSACTION` - Places a name on a transaction.

### Lock
A READ|WRITE locks has the following features:
- The only session that holds the lock of a table can read/write data from the table.
- Other sessions cannot read data from and write data to the table until the WRITE lock is released.

```sql
LOCK TABLES table_name [READ | WRITE]
```

### Isolation levels
**I in ACID**

https://www.geeksforgeeks.org/transaction-isolation-levels-dbms/\
Isolation levels define the degree to which a transaction must be isolated from the data 
modifications made by any other transaction in the database system. (Rules for acquiring a LOCK.)

- `dirty reads` - read data while another uncommitted write.
- `non-repeatable reads` - read data few times in single transaction, 
but another transaction is committed update between reads.
- `phantom reads` - same as above, but another transaction commits adding new row. 


- **Read uncommitted** permits dirty reads, non repeatable reads and phantom reads.
- **Read committed** permits non repeatable reads and phantom reads.
- **Repeatable read** permits only phantom reads.
- **Serializable** does not permit any read errors.

 To set the global isolation level at server startup, use the `--transaction-isolation=level`
option on the command line or in an option file.

### Stored procedure
A stored procedure is a prepared SQL code that you can save, so the code can be reused 
over and over again. So if you have an SQL query that you write over and over again, save 
it as a stored procedure, and then just call it to execute it. You can also pass parameters 
to a stored procedure, so that the stored procedure can act based on the parameter 
value(s) that is passed.

```sql
CREATE PROCEDURE SelectAllCustomers @City nvarchar(30)
AS
SELECT * FROM Customers WHERE City = @City
GO;

# usage:
EXEC SelectAllCustomers @City = "London";
```

### User-defined functions
A user-defined function (UDF) is a way to extend MySQL with a new function that works like
a native (built-in) MySQL function such as ABS() or CONCAT(). To create a function, you must 
have the INSERT privilege for the mysql system database. This is necessary because CREATE 
FUNCTION adds a row to the `mysql.func` system table that records the function's name, type, 
and shared library name. \
Function is compiled and executed every time it is called. And cannot modify the data 
received as parameters and function must return a value. Functions are similar to Stored
procedures, but with following differences:

| Function                                                                               | Stored procedure                                                                                      |
|----------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| The function always returns a value.                                                   | Procedure can return “0” or n values.                                                                 |
| Functions have only input parameters for it.                                           | Procedures can have output or input parameters.                                                       |
| You can call Functions from Procedure.                                                 | You can’t call Procedures from a Function.                                                            |
| You can’t use Transactions in Function.                                                | You can use Transactions in Procedure.                                                                |
| You can’t use try-catch block in a Function to handle the exception.                   | By using a try-catch block, an exception can be handled in a Procedure.                               |
| Function can be utilized in a SELECT statement.                                        | You can’t utilize Procedures in a SELECT statement.                                                   |
| Function allows only SELECT statement in it.                                           | The procedure allows as DML(INSERT/UPDATE/DELETE) as well as a SELECT statement in it.                |
| The function can be used in the SQL statements anywhere in SELECT/WHERE/HAVING syntax. | Stored Procedures cannot be used in the SQL statements anywhere in the WHERE/HAVING/SELECT statement. |

```sql
CREATE FUNCTION function_name(param1, param2)
RETURNS datatype
BEGIN
 -- statements
END
```

### Triggers
Performs operation on data. Fires on event (`INSERT`, `UPDATE`, etc) or interval.
```sql
CREATE TRIGGER ins_sum BEFORE INSERT ON account
FOR EACH ROW SET @sum = @sum + NEW.amount;
```

### Cursor
Type of SQL variable that holds `SELECT`'s statement resulting set. It might be used to perform
some logic on dataset in `STORED PROCEDURE` or `FUNCTION`.

```sql
CREATE PROCEDURE demo()
BEGIN
  DECLARE a CHAR(16);
  DECLARE b INT;
  DECLARE cur1 CURSOR FOR SELECT id,data FROM test.t1;

  OPEN cur1;

  read_loop: LOOP
    FETCH cur1 INTO a, b;
      INSERT INTO table1.t3 VALUES (a,b); -- INSERT into different table
  END LOOP;

  CLOSE cur1;
END;
```

## Database (practice)
### Persistence
Ability to save state of process without saving a process (e.g. in file).

### Connection with DB
```go
import (
	"database/sql"
	_ "github.com/go-sql-driver/mysql" // driver
)

func main() {
	db, err := sql.Open("mysql",
		"user:password@tcp(127.0.0.1:3306)/hello")
	if err != nil {
		log.Fatal(err)
	}
	defer db.Close()
}
```

### Data Models
**TODO**

Database management system (DBMS) is a computer program that interacts with a database.
- ACID (atomicity, consistency, isolation, durability) is a set of properties of database transactions intended to guarantee validity even in the event of errors, power failures, etc. 
- CAP theorem: consistency(C), availability(A) and partition toleration(P).

# Verification
## Tests, Trace, Profile
### Benchmark
**TODO**

### Profiling concepts
**TODO**

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
### Race conditions
https://blog.golang.org/race-detector

```sh
go run -race main.go # will run race detector. same for build, get, test and install
```
