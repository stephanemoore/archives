# Using Dot Syntax With Methods

Dot syntax is a syntactic shorthand introduced with Objective-C 2.0. Dot syntax can be used to
access Objective-C properties. Dot syntax can also be used to invoke Objective-C methods but
this practice can lead to unexpected behaviors.

Consider the following example:
```Objective-C
#import <Foundation/Foundation.h>

@interface Observation : NSObject

@property(nonatomic, readonly, getter=isValid) BOOL valid;

- (BOOL)valid;

@end

@implementation Observation

- (BOOL)isValid {
  return NO;
}

- (BOOL)valid {
  return YES;
}

@end

#define PrintBooleanExpression(expr) printf("(%s) = %s\n", #expr, (expr) ? "YES" : "NO")

int main(int argc, const char **argv) {
  Observation *observation = [[Observation alloc] init];
  PrintBooleanExpression(observation.valid);
  PrintBooleanExpression([observation valid]);
  return 0;
}
```

Now consider the output of this program:
```
(observation.valid) = NO
([observation valid]) = YES
```

Note that the expression `observation.valid` invokes the method `-isValid` whereas
`[observation valid]` invokes the method `-valid`.

Be wary of the potential for confusion when using dot syntax to invoke Objective-C methods
and consider only using dot syntax with Objective-C properties to avoid confusion and
unexpected behaviors as a result.
