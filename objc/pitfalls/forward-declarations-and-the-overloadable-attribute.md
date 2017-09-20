# Beware forward declarations and the overloadable attribute.

Forward declarations have various benefits (e.g., compilation efficiency) but
they can interact poorly with clang's
[overloadable attribute](https://clang.llvm.org/docs/AttributeReference.html#overloadable).

Consider the following example:

```Objective-C
#import <Foundation/Foundation.h>

/** FOO_OVERLOADABLE specifies the clang overloadable attribute. */
#if defined(__has_attribute) && __has_attribute(overloadable)
#define FOO_OVERLOADABLE __attribute__((overloadable))
#else // defined(__has_attribute) && __has_attribute(overloadable)
#error "This file cannot be compiled without support for the overloadable attribute."
#endif // defined(__has_attribute) && __has_attribute(overloadable)

#if FORWARD_DECLARE_FOO
@class Foo;
#else  // FORWARD_DECLARE__FOO
@interface Foo : NSObject
@end
#endif  // FORWARD_DECLARE__FOO

void Log(id object) FOO_OVERLOADABLE {
  NSLog(@"The compiler assumed Foo is of type id.");
}

void Log(NSObject *object) FOO_OVERLOADABLE {
  NSLog(@"The compiler assumed Foo is of type NSObject.");
}

int main(int argc, const char **argv) {
  Foo *f = nil;
  Log(f);
  return 0;
}

```

When compiling with FORWARD_DECLARE_FOO=0 the interface for the class `Foo` is exposed
and the compiler is aware that the type derives from `NSObject`. The program
output will include the log statement `"The compiler assumed Foo is of type NSObject."`.

When compiling with FORWARD_DECLARE_FOO=1 the class `Foo` is forward declared.
The compiler does not have further information about the class and is unable to make an
assumption about the type of `Foo` more specific than it being an Objective-C object.
`Foo` may in fact be declared as an NSObject in another source file but that type
information is hidden from this compilation unit. As a result, the program output will
include the log statement `"The compiler assumed Foo is of type id."`.

Program behavior may change as a result of interactions between forward declarations
and the overloadable attribute. When considering the use of forward declarations be
mindful of the intended or desired behavior.
