# Preventing Subclassing

In some scenarios it is advantageous to discourage subclassing. Initializer overrides
and [`__attribute__((objc_subclassing_restricted))`](https://reviews.llvm.org/D25993)
are effective tools to deter subclassing. The following example shows both in practice:

``` Objective-C
// SubclassingMacros.h

// FOO_SUBCLASSING_RESTRICTED allows the compiler to restrict subclassing.
#if defined(__has_attribute) && __has_attribute(objc_subclassing_restricted)
#define FOO_SUBCLASSING_RESTRICTED __attribute__((objc_subclassing_restricted))
#else // defined(__has_attribute) && __has_attribute(objc_subclassing_restricted)
#define FOO_SUBCLASSING_RESTRICTED
#endif // defined(__has_attribute) && __has_attribute(objc_subclassing_restricted)
```

``` Objective-C
// ExampleClass.h

#import <Foundation/Foundation.h>

#import "SubclassingMacros.h"

FOO_SUBCLASSING_RESTRICTED
@interface ExampleClass : NSObject
@end

```

``` Objective-C
// ExampleClass.m

#import "ExampleClass.h"

@implementation ExampleClass

- (instancetype)init {
  self = [super init];

  // 🛡 This runtime check aims to detect and discourage subclasses.
  if ([self class] != [ExampleClass class]) return nil;

  return self;
}

@end

```
