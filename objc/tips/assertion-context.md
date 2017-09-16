# Test Assertion Context

Test assertions generally benefit greatly from appropriate context
in assertion messages.

```Objective-C
#import <Foundation/Foundation.h>
#import <XCTest/XCTest.h>

#define FOOAssertStringHasPrefix(string, prefix) \
  XCTAssertTrue([(string) hasPrefix:(prefix)], \
                @"\"%@\" is missing the expected prefix \"%@\".", (string), (prefix))

@interface FOOStringPrefixTests : XCTestCase
@end

@implementation FOOStringPrefixTests

#pragma mark - Helper Methods

- (void)assertString:(NSString *)string hasPrefix:(NSString *)prefix {
  XCTAssertTrue([string hasPrefix:prefix],
                @"\"%@\" is missing the expected prefix \"%@\".", string, prefix);
}

- (void)testInstanceMethodAssertion {
  [self assertString:@"foo" hasPrefix:@"bar"];
}

- (void)testMacroAssertion {
  FOOAssertStringHasPrefix(@"foo", @"bar");
}

@end
```

In the above example `-testInstanceMethodAssertion` will cite a failure in
`assertString:hasPrefix:` while `-testMacroAssertion` will cite a failure
in `-testMacroAssertion`. The lack of indirection in `-testMacroAssertion`
may enable engineers to more quickly resolve failures.

Test assertion failures are often consumed from indirect sources, e.g.,
continuous build logs. Adequate context in test assertion failures can make
it easier for engineers to diagnose failures and identify appropriate
resolution.
