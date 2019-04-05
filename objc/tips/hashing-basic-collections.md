# Hashing Basic Collections

The implementations of the [basic collections in Foundation](https://developer.apple.com/documentation/foundation/collections?language=objc)
override the [`hash`](https://developer.apple.com/documentation/objectivec/1418956-nsobject/1418859-hash?language=objc)
property to support more efficient hashing behavior. The implementations balance various considerations including
hash collision probability and runtime performance. It is generally difficult to design a hash function that is optimal in
all cases. Make sure that the hashing behaviors of Foundation's basic collections are appropriate for the scenario before
relying on them and evaluate custom hashing solutions when the defaults are suboptimal for the scenario.

Consider the following program:
```Objective-C
#import <Foundation/Foundation.h>

#import <stdio.h>

int main(int argc, const char **argv) {
  printf("NSFoundationVersionNumber: %f\n", NSFoundationVersionNumber);
  printf("Arrays\n");
  printf("%ld\n", (unsigned long)@[ @1, @2 ].hash);
  printf("%ld\n", (unsigned long)@[ @3, @4 ].hash);
  printf("%ld\n", (unsigned long)@[ @1, @2, @3, @4, @5 ].hash);
  printf("Sets\n");
  printf("%ld\n", (unsigned long)[NSSet setWithArray:@[ @1, @2 ]].hash);
  printf("%ld\n", (unsigned long)[NSSet setWithArray:@[ @3, @4 ]].hash);
  printf("%ld\n", (unsigned long)[NSSet setWithArray:@[ @1, @2, @3, @4, @5 ]].hash);
  printf("Dictionaries\n");
  printf("%ld\n", (unsigned long)@{ @"" : @"" }.hash);
  printf("%ld\n", (unsigned long)@{ @"a" : @"b" }.hash);
  printf("%ld\n", (unsigned long)@{ @"a" : @"b", @"c" : @"d" }.hash);
  return 0;
}

```

Now, consider the program output when built and run on macOS 10.13.6 (17G3025):
```
NSFoundationVersionNumber: 1454.930000
Arrays
2
2
5
Sets
2
2
5
Dictionaries
1
1
2
```

Note that the hash of arrays and sets is merely the length of the collection and the hash of dictionaries is the number of
keys. This behavior is presumably an intentional tradeoff between runtime performance of computing hashes and hash collision
probability. Be aware of the tradeoffs in hashing functionality in basic collections and evaluate whether the behavior is
appropriate in the target use case before relying upon it.
