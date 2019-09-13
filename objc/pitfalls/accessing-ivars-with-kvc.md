# Accessing Instance Variables with Key-Value Coding

[Key-Value Coding](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/KeyValueCoding/index.html#//apple_ref/doc/uid/10000107i) 
(KVC) provides a powerful mechanism for indirect access to instance state. However, this indirect
access mechanism has its pitfalls. Consider the following sample code:

```Objective-C
#import <Foundation/Foundation.h>

@interface BaseClass : NSObject
@end

@interface DerivedClass : BaseClass
@end

@implementation BaseClass {
  NSString *_invokedInitializer;
}

- (instancetype)init {
  self = [super init];
  if (self) {
    _invokedInitializer = @(__PRETTY_FUNCTION__);
  }
  return self;
}

@end

@implementation DerivedClass {
  NSString *_invokedInitializer;
}

- (instancetype)init {
  self = [super init];
  if (self) {
    _invokedInitializer = @(__PRETTY_FUNCTION__);
  }
  return self;
}

@end

int main(int argc, const char **argv) {
  DerivedClass *object = [[DerivedClass alloc] init];
  NSLog(@"%@", [object valueForKey:@"_invokedInitializer"]);
  return 0;
}
```
Each class has an instance variable `_invokedInitializer` which stores the initializer
of the class that was called. Unfortunately, if we use KVC to access the value at the
key path `@"_invokedInitializer"`, we only get one of the values due to the naming
collision across the independent instance variable namespaces of the two classes.

Beware relying on KVC to access instance variables, especially in complex class
hierarchies.
