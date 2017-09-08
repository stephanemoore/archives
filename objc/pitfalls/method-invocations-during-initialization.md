# Beware Method Invocations During Initialization

Method invocations during initialization may cause subclasses to operate on uninitialized state.
Consider the following example:

```Objective-C
// BaseClass.h

#import <Foundation/Foundation.h>

@interface BaseClass : NSObject

- (void)logMessage:(NSString *)message;

@end

```

```Objective-C
// BaseClass.m

#import "BaseClass.h"

@implementation BaseClass

- (instancetype)init {
  self = [super init];
  if (!self) return nil;
  [self logMessage:@"Initializing."];
  return self;
}

- (void)logMessage:(NSString *)message {
  NSLog(@"%@", message);
}

@end

```

```Objective-C
// DerivedClass.h

#import "BaseClass.h"

@interface DerivedClass : BaseClass
@end

```

```Objective-C
// DerivedClass.m

#import <Foundation/Foundation.h>

@implementation DerivedClass {
  NSString *_prefix;
}

- (instancetype)init {
  self = [super init];
  if (!self) return nil;
  _prefix = @"PREFIX";
  return self;
}

- (void)logMessage:(NSString *)message {
  NSLog(@"%@: %@", _prefix, message);
}

@end

```

When a new instance of `DerivedClass` is initialized its initializer `‑[DerivedClass init]`
first invokes `‑[BaseClass init]` and subsequently `‑[DerivedClass logMessage:]` is called.
`‑[DerivedClass logMessage:]` accesses `_prefix` but unfortunately at this stage of
initialization the instance variable remains `nil` rather than the presumably intended initialized
value `@"PREFIX"`.

Superclass initialization generally occurs before subclass initialization but method
invocations cause dynamic dispatches that may invoke subclass methods that may rely on state
that has not yet been initialized. Be wary of method invocations in initializers lest operations
on uninitialized state cause unintended behaviors.
