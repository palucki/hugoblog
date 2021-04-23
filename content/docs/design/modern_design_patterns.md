---
title: "Modern design patterns"
date: 2021-04-18T19:47:36+02:00
draft: false
comments: false
menu:
  docs:
    parent: "design"
images:
---

First of all, modern design pattern follows all the software best practices (i.e [S.O.L.I.D.](https://en.wikipedia.org/wiki/SOLID)) and uses modern C++.

## Builder

When we have mutli-argument constructors, we should strive to replace them with piecewise object construction.
A *Builder* is an object with functions to construct, add, etc. elements.
To force the use of builder, we can set object construtors in private section.

### Fluent interface

Addition to the normal builder which allows to "chain" method invoications:

	builder.createCar().addRoof("green").addWheels().replace(engine, newEngine).
	
It also allows to specify which calls can be joined and which can not.

### Facets 

If creating a part is a complex thing, we can create several builders, ie. `withJob()`, `withId()`

## Factory method

For simple solutions, we do not need a separate Buidler. The Factory Methods will be enough.
Just make an object's constructor private / deleted and create static methods, which returns class object:

	static Point fromPolar(float rho, float theta)
	static Point fromCarthesian(float x, float y)
	
then simply use:

	auto p = Point::fromCarthesian(100, 200);


## Factory 

It is a way to overload constructor (because we provide different arguments and have different purposes)
Not an Abstract Factory by meaning the GoF definition. Just a class with access to the other class members and responsible for creating them.
Has a statich methods: 

	PointFactory::fromCarthesian
	PointFactory::fromPolar
	
needs to be friend of `Point` class, or the `Point` need to be public.

### Inner factory 

`PointFactory` contained in `Point` class. Solves the access to member problem.

### Factory vs Builder 

The difference between those two is in their purpose. The purpose of Builder is to create an element piece-by-piece.
The intent of Factory is to create an element with a single call.

## Prototype

Pattern used especially when cloning objects, when we need a deep copy.
It serves the purpose of a template, which can be used and adjusted (customized).

How to implement:

1. Create and store an instance of a object which can be then cloned.
(for example by serializing and deserializing)


## Singleton

Pattern which makes sure that only one instance of an objects will be ever created. Used when:
- constructor is expensive (a lot of operation, I/O or hardware access etc.)
- we want to be sure that only one copy will be created 
- we want to leverage lazy initialization or thread safety 

The simplest approach is to make constructor private, copy ctor and assignment operator -> deleted.
Then all we need is to create `get()` or `instance()` method which will return the object.

SingletonObject& instance() {
	static SingletonObject instnace;
	return instance;
}

> this is guaranteed to be thread-safe in C++11

### Singleton problems

Singleton tightly couples application with resource / database it represents. It is hard to test such a solution.
The solution for this issue it o use `dependency injection` pattern. It is a mechanism where in one place we inject dependency instead 
of our real object. For example. with database access singleton, we need to create a constructor, which takes `DBInterface` 
and later, in tests use DummyDatabase, and in production code use RealDatabase.
And use interface in all places.

### Monostate

Singletion variation, allows to create several instances, but they all share common data (for instance static member in class).

> It is not recommended to use that pattern

### Multiton

Singleton variation, allows to create singletons for different types (each type has own singleton).


## Adapter

This pattern allows us to adjust existing `interface X` to match the specific needs of our system (which expects `interface Y`).

> If Adapter needs a lot of data / objects inside it's worth considering *caching*

## Bridge

Allows us to avoid *carthesian product* in class design. Separates interface from its implementation.
The approach here is to pass reference to a hierearchy of objects straight to the other hierarchy, and use it there (instead of inheriting).

### PIMPL as a Bridge implementation 

Pointer to implementation, which allows us to hide the whole of implementation in .cpp file.

- Clients depend only on header and do not need to recompile when our implementation change.
- Improves compilation speed.

## Composite

Provides uniform API (interface) to treat single objects and group of objects in the same way.
For example, consider `Shape` and `Collection<Shape>`.

If single object requires `begin()` or `end()` methods, they can be implemented as:

	begin() { return this;} 
	end() {return this+1;}

### Array backed properties

Gather all properties used in `std::array`, add enum which hold their names with the last element denoting the number of elements.
Then getterse and setters will be simply:

	return array[property_name];
	
## Decorator

Allows to add features to existing objects without modifing their interfaces by wrapping.

	ColouredShape{Circle, "red"}
	
or 

	TransparentShape{ColoredSHape{Square, 10}, "blue"}, 128}

## Proxy 

A class that acts as an interface to a particular resorce (may be remote, expensive, require logging etc). 
Acts as local object, in fact under the hood is remote object (communication proxy). 

*Smart pointers* are example of such a proxy, behaves as ordinary pointers, but have a lot of added functionality.

Examples: property proxy, virtual proxy, communcation proxy.

### Proxy vs Decorator 

- Proxy uses the same interface and set of features, Decorator has the same interface and features.
- Decorator keeps reference to the decorated objects, Proxy - not necessarily.

## Command 

An object encapsulating a command (command encapsulating), which can later be replayed, serialized, saved, undone etc.
Allows to create macros (chaing of commands).

### CQS - Command Query Separation
Command - Do
Query - Ask 

Do not mix them together.

## Interpreter 

Bascially converts string to other, object oriented structure.
Performs 2 steps: 
- lexing - tokenize 
- parsing - transform set of tokens into hierarchical, logical strucutre

## Iterator 

Alows traversing collection.
Has `operator!=` and `operator++` defined

## Mediator

Central component which is a broker in communication between objects.
Objects need to have access to that (either as regference, pointer).

Example:

ChatRoom which serves Person objects.

Another name: EventBroker

## Strategy (policy)

Allows to specify behavior at runtime, or at compile time.

## Template method

Similiar to strategy, but uses inheritance instead of composition.
Defines an algorithm with points of actions which are extended in derived clases.

Guarantees that operations are taken in correct order.

## Memento (token)

Allows to save system state and restore it later. Similiar to Command, but used to keep linear history.
Creates snapshots (instead of a line of undo / redo operations).
Needs to keep the whole state, so it doesn't make sense when the state is huge and expensve.

## Observer 

Allows to subscribe & listen to events and notify interested parties.
 
- Event / Subscriber 
- Signal / Slot
- Observer / Observable  

## State 

Pattern which provides that the behavior of an objects depends on its state.
The component which handles state transitions is called *State Machine*.
The classic aproach is to define a state, and all methods defined (for example onStart(), onExit(), onTimeout())

Nice implementation is done with a set of behaviors: 
	
	std::map<State, std::vector<std::pair<Transition, State>>> rules
	
It allows to specify all the transitions etc. and then use it like this:
	
	auto nextState = rules[currentState][input].second

In Boost, there is a Meta State Machine.

## Visitor 

Pattern allowing the component to traverse / visit the whole hierarchy of inheritance.
The hierarchy needs to implement `accept(visitor)` method, which performs:
	
	visitor.visit(this);
	
And the visitor needs `visit()` method with overload for every class in the hierarchy.

Also called: *double dispatch*

### std::visit

Modern C++ implementation of visitor:
`std::variant<string, int, ... >` and `std:: visit`

Visitor requires operator() for all types visited:

	operator()(const string& s)
	operator()(const int& i)

Example implementation:

	struct Triangle {
		void Render() { cout << "Drawing a triangle!\n"; }
	};

	struct Polygon {
		void Render() { cout << "Drawing a polygon!\n"; }
	};

	struct Sphere {
		void Render() { cout << "Drawing a sphere!\n"; }
	};

	using Renderable = std::variant<Triangle, Polygon, Sphere>;
	std::vector<Renderable> vecRender {
			Polygon(), Triangle(), Sphere(), Triangle()
		};

	auto CallRender = (auto& obj) { obj.Render(); };

	for (auto& obj : vecRende )
		std::visit(CallRender, obj);

#### Observations 

Here are some observations we can make: (for `std::visit approach`)

- There's no base class, types inside a variant might be unrelated.
We can easily add a new visitor, without changing the implementation of classes. 
	
> For example, we can implement a visitor that scans the objects and sorts them according to Z-order. 
> In regular polymorphism, adding a new virtual method is more problematic, 
> as you'd have to update the whole class hierarchy.

- It's harder to add new types: you'd have to extend the Renderable type and also each visitor to support the new type. That's much easier with regular polymorphism.
Since std::variant is the size of the largest type, then we might waste some space when types differ a lot. For example, when one type holds 128 bytes, while the other is only 16 bytes.
We can use value semantics rather than pointer semantics, so we can have benefits of a contiguous memory block and possible cache friendliness.

