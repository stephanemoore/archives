# Functions in Implementation Blocks

Declaring functions within Objective-C implementation blocks is not normally encouraged but functions declared
within implementation blocks possess a unique trait: they inherit access to the same instance variables that
Objective-C methods in that implementation block would have access to.

Consider the following example:
```
#import <Foundation/Foundation.h>

@interface Foo : NSObject
@end

@implementation Foo {
  int _counter;
}

static void LogCounterWithinImplementation(Foo *foo) {
  NSLog(@"%d", foo->_counter);  // ✅
}

@end

static void LogCounterOutsideImplementation(Foo *foo) {
  NSLog(@"%d", foo->_counter);  // ❌
}

```

`LogCounterWithinImplementation` compiles successfully whereas `LogCounterOutsideImplementation` fails to
compile because it does not have access to the private instance variable `_counter`. This practice is
typically only useful in situations where a class wants to share logic between two Objective-C methods without
introducing the risk of a subclass causing unexpected behavior by inadvertently overriding a private instance
method. For example, a C function might be used to share logic between two or more designated initializers in
an Objective-C class.
