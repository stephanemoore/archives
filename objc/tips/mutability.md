# Mutability

Mutability is a topic of discussion that comes up often in Objective-C
API design. There are a variety of strategies that can be adopted with
respect to mutability. Several of these strategies are described in
the sections below. Consider the strengths and weaknesses of different
strategies for managing mutability and evaluate them while seeking
solutions to particular problems or scenarios.

## `NSCopying` and `NSMutableCopying`

One of the common strategies for managing models is to implement two
classes that conform to
[`NSCopying`](https://developer.apple.com/documentation/foundation/nscopying?language=objc)
and
[`NSMutableCopying`](https://developer.apple.com/documentation/foundation/nsmutablecopying?language=objc).
This is a strategy adopted by many of the most basic Foundation classes,
e.g.,
[`NSString`](https://developer.apple.com/documentation/foundation/nsstring?language=objc)
and
[`NSMutableString`](https://developer.apple.com/documentation/foundation/nsmutablestring?language=objc).

### Pros

* Easy to understand which objects can be mutated after initialization.
* Mutations are convenient when using the mutable class.
* Immutable instances prevent mutations after initialization.
* Conversions between immutable and mutable types can be performed as
  needed depending on context.
* Provides the flexibility to optimize immutable and mutable classes
  independently. For example, immutable classes might be able to avoid
  locking to more efficiently satisfy concurrency requirements.
* Readers benefit from idiomatic convention that properties with the `copy`
  attribute indicate that the setter copies the input to store immutable
  data.

### Cons

* Maintenance overhead of immutable and mutable classes. For example,
  optimal concurrency design might require two substantially different
  implementations.
* Binary size overhead of immutable and mutable classes. The compiled
  classes necessarily occupy space in binaries that they are linked into.
  The impact of this is negligible with smaller numbers of classes but
  scales as more classes are added.
* Runtime overhead of immutable and mutable classes. The classes incur
  some overhead at runtime because they must be loaded into the Objective-C
  runtime at some point during process lifetime in order to be used. The
  impact of this is negligible with smaller numbers of classes but scales
  as more classes are added.
* Requires proper management of mutable and immutable data. For example,
  an implementation may need to convert mutable data to immutable data.
* No inherent mechanism to prevent storing pointers to mutable state in
  immutable instances.

## Mutable Class with `NSCopying`

Another strategy is to only maintain a mutable class that supports a
copy operation.
[`NSURLSessionConfiguration`](https://developer.apple.com/documentation/foundation/nsurlsessionconfiguration?language=objc)
is an example of this strategy applied.

### Pros

* Easy to understand that all objects can be mutated at any time.
* Mutations are convenient.

### Cons

* No inherent mechanism for preventing mutations. This can make it more
  difficult to reason about behavior or prevent unexpected behavior.
* Difficulty of reasoning about mutations increases as objects transition
  between different modules or layers. For example, if an object is passed
  to an interface, can the caller assume that the object was not mutated
  during the call? Effective documentation can sometimes be a reasonable
  mitigation.
* No inherent mechanism for communicating when copies occur. Mitigative
  options might include effective documentation and effective indexing or
  search infrastructure.
* Potential source of performance issues. For example, to satisfy concurrency
  and stability, mutable classes may incur locking overhead.

## Immutable Class

Yet another strategy is to only maintain an immutable class which
provides one or more designated initializers or convenience construction
methods that support all valid initialization vectors (often by exposing
parameters for all internal state).
[`CLLocation`](https://developer.apple.com/documentation/corelocation/cllocation?language=objc)
arguably represents this strategy.

### Pros

* Easy to understand that all objects are immutable after initialization.
* Inherently easy to prevent mutations after initialization.
* Potential benefits to concurrency design. Immutable data often allows
  lockless design which can help reduce resource contention.

### Cons

* Mutation by initializing new immutable objects may be inconvenient.
* No inherent mechanism to prevent storing pointers to mutable state.
