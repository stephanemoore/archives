# Invalid `dispatch_once_t` Storage

[`dispatch_once_t`](https://developer.apple.com/documentation/dispatch/dispatch_once_t?language=objc)
is a common predicate used to execute code once in the lifetime of a process.

`dispatch_once_t` has an important requirement that is sometimes overlooked:

> Variables of this type must have global or static scope. The result of using this type with automatic or dynamic allocation is undefined.  
> —[`dispatch_once_t` Documentation](https://developer.apple.com/documentation/dispatch/dispatch_once_t?language=objc)

To avoid undefined and unexpected behavior, `dispatch_once_t` variables should be declared with static storage duration or at file scope:
```
#import <Foundation/Foundation.h>
#include <dispatch/dispatch.h>

static dispatch_once_t gOnceToken;  // ✅ Static storage duration and file scope.

static void GoodExample() {
  static dispatch_once_t onceToken;  // ✅ Static storage duration.
  dispatch_once(&onceToken, ^{
    // ...
  });
}

static void BadExample() {
  dispatch_once_t onceToken;  // ❌ Automatic storage duration.
  dispatch_once(&onceToken, ^{
    // ...
  });
}

@interface Example : NSObject
@end

@implementation Example {
  dispatch_once_t _onceToken;  // ❌ Dynamic allocation.
}

@end
```
