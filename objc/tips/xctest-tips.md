# XCTest Tips

### Include relevant information in assertion messages when using `XCTAssert`/`XCTAssertTrue`/`XCTAssertFalse`

When using `XCTAssert`/`XCTAssertTrue`/`XCTAssertFalse` consider relevant information to include in the assertion
message to facilitate diagnosis of failures.

```Objective-C
// 👌 This assertion messages includes information useful for diagnosing failures.
XCTAssertTrue([string hasPrefix:prefix], @"\"%@\" is missing the expected prefix \"%@\".", string, prefix);

// ❌ This assertion message provides little information to aid diagnosis.
XCTAssertTrue([string hasPrefix:prefix], @"The string is missing the expected prefix.");
```
