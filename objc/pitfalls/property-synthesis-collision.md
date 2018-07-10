# Property Synthesis Collision

Property synthesis is a convenient mechanism used to implement
Objective-C properties. Take care to avoid potential bugs that
can be caused by inappropriate property synthesis.

Consider the following example:
```Objective-C
#import <Foundation/Foundation.h>

@interface BaseClass : NSObject

/** A string property with a default value. */
@property(nonatomic, copy) NSString *message;

@end

@implementation BaseClass

- (instancetype)init {
  self = [super init];
  if (self) {
    _message = @"Everything is fine";
  }
  return self;
}

@end

@interface DerivedClass : BaseClass

@property(nonatomic, copy) NSString *message;

@end

@implementation DerivedClass

@synthesize message;  // ⚠️ DerivedClass no longer inherits the default value from BaseClass
                      // because the property synthesis generates a new instance variable and
                      // getter/setter method overrides that use the new instance variable.

@end

```

In the example above, an instance of `BaseClass` will have the default
value `"Everything is fine"` for the `message` property. In contrast,
an instance of `DerivedClass` will have a `nil` default value for the
`message` property because `DerivedClass` synthesized an independent
`_message` instance variable and overrode `-message` and `-setMessage:`,
obscuring the superclass's method implementations and instance variable.

This form of error may be more common in complicated class hierarchies
and may occur more frequently when class hierarchies are refactored.

Be wary of colliding property syntheses and the unexpected behaviors that
they can produce.
