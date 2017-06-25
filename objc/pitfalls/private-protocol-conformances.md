# Beware Private Protocol Conformance Declarations

Private protocol conformance declarations can create a vulnerability where a
subclass can unintentionally break functionality in the superclass.
Consider the following example:

```Objective-C
// Protocols.h
@protocol FruitProtocol
- (void)apple;
@end
```

```Objective-C
// BrittleBaseClass.h

#import <Foundation/Foundation.h>

#import "Protocols.h"

@interface BrittleBaseClass : NSObject
@end
```

```Objective-C
// BrittleBaseClass.m

#import "BrittleBaseClass.h"

@interface BrittleBaseClass () <FruitProtocol>
@end

@implementation BrittleBaseClass {
  BOOL _apple;
}

- (void)apple {
  _apple = YES;
}
@end
```

```Objective-C
// BrokenDerivedClass.h

#import "BrittleBaseClass.h"

@interface BrokenDerivedClass : BrittleBaseClass <FruitProtocol>
@end
```

```Objective-C
// BrokenDerivedClass.m

#import "BrokenDerivedClass.h"

@implementation BrokenDerivedClass {
  BOOL _banana;
}

// Implementing this protocol method in the subclass unintentionally
// breaks superclass state management because -[BrokenDerivedClass apple]
// will not set _apple to YES.
- (void)apple {
  _banana = YES;
}
@end
```

As seen in this example, private protocol conformance declarations can
create an invisible method collision that can cause unstable behavior.

The vulnerabilities of private protocol conformance declarations can be
avoided in several ways:
* Publicly declare protocol conformances.
* Publicly declare implemented protocol methods in the base class header
(note that you can use NS_REQUIRES_SUPER to require subclasses to call
the superclass implementation).
* Prevent subclassing of the base class.
