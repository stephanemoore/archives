# Beware Missing Superclass Method Invocations

Classes should explicitly annotate methods where subclass overrides must invoke superclass implementations.
Consider the following example:

```Objective-C
// VulnerableBaseClass.h

#import <Foundation/Foundation.h>

@interface VulnerableBaseClass : NSObject

- (void)foo;

@end

```

```Objective-C
// VulnerableBaseClass.m

#import "VulnerableBaseClass.h"

@implementation VulnerableBaseClass

- (void)foo {
  // Do something critically important.
}

@end

```

```Objective-C
// BrokenDerivedClass.h

#import "VulnerableBaseClass.h"

@interface BrokenDerivedClass : VulnerableBaseClass
@end

```

```Objective-C
// BrokenDerivedClass.m

#import "BrokenDerivedClass.h"

@implementation BrokenDerivedClass

- (void)foo {
  // This implementation fails to invoke critically important functionality 💥
}

@end

```

As seen in the example above, the subclass `BrokenDerivedClass` overrode `‑foo` without invoking the
implementation inherited from `VulnerableBaseClass` and caused critically important functionality to be skipped.
To defend against this vulnerability one may use
[`__attribute__((objc_requires_super))`](https://clang.llvm.org/docs/AttributeReference.html#objc-requires-super)
(or the `NS_REQUIRES_SUPER` macro provided by Foundation) to enable the compiler to warn when subclass overrides
fail to invoke superclass method implementations.

The example below demonstrates a base class interface that more
effectively prevents subclasses from overriding `‑foo` without invoking the superclass implementation:

```Objective-C
// BaseClass.h

#import <Foundation/Foundation.h>

@interface BaseClass : NSObject

// 🛡 The compiler may require subclass overrides to call the superclass implementation.
- (void)foo NS_REQUIRES_SUPER;

@end

```

[//]: # (TODO: Add a section specifically about XCTestCase subclass methods)
