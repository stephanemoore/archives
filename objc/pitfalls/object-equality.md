# Object Equality

One of the common paradigms for evaluating object equality is by using
[`-isEqual:`](https://developer.apple.com/documentation/objectivec/nsobjectprotocol/isequal(_:)?language=objc).  
`-isEqual:` has some sharp edges.

## `nil` Equality

When sending the message `-isEqual:` to `nil`, `nil` messaging results
in `NO` as the return value for all arguments. This makes sense for most
inputs but there is a notable exception where this results in an
unintuitive result: `nil`.

See the sample program below:

```Objective-C
#import <Foundation/Foundation.h>

int main(int argc, const char **argv) {
  BOOL result = [(id)nil isEqual:nil];
  if (!result) NSLog(@"nil is not equal to nil");
  return 0;
}
```

This sample program shows that messaging `nil` with `-isEqual:` and `nil`
as the argument will report that the two `nil` objects are not equal.
By contrast, pointer equality would consider these arguments to be
identical.

One way to avoid this unintuitive behavior is by using an abstraction
layer to handle this edge case:

```Objective-C
BOOL FOOAreEqualOrBothNil(id object1, id object2) {
  if (object1 == object2) return YES;
  return [object1 isEqual:object2];
}
```
