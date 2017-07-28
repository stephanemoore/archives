# Beware Singleton Class Derivation

Singleton interfaces may sometimes be vulnerable to inheritance. Consider the example below:

```Objective-C
// VulnerableSingleton.h

@interface VulnerableSingleton : NSObject

/** Returns a singleton instance. */
+ (instancetype)sharedInstance;

/** Performs an important operation. */
- (void)performImportantOperation;

@end

```

```Objective-C
// VulnerableSingleton.m

@implementation VulnerableSingleton

+ (instancetype)sharedInstance {
  static dispatch_once_t once;
  static VulnerableSingleton *sharedInstance;
  dispatch_once(&once, ^{
    sharedInstance = [[self alloc] init]; // 💥 A subclass may hijack the singleton instance.
  });
  return sharedInstance;
}

- (void)performImportantOperation {
  // Perform an important operation.
}

@end

```

```Objective-C
// DerivedSingleton.h
@interface DerivedSingleton : VulnerableSingleton
@end

```

```Objective-C
// DerivedSingleton.m
@implementation DerivedSingleton

- (void)performImportantOperation {
  NSLog(@"This class prefers that the important operation never occurs.");
}

@end

```

If +sharedInstance is first invoked on the class DerivedSingleton then the shared instance
will be of type DerivedSingleton and the superclass's implementation of -performImportantOperation
will not be called.

Consider this alternative singleton implementation:

```Objective-C
// Singleton.h

@interface Singleton : NSObject

/** Returns a singleton instance. */
+ (Singleton *)sharedInstance;

/** Performs an important operation. */
- (void)performImportantOperation;

@end

```

```Objective-C
// Singleton.m

@implementation Singleton

+ (Singleton *)sharedInstance {
  static dispatch_once_t once;
  static Singleton *sharedInstance;
  dispatch_once(&once, ^{
    // 🛡 This implementation offers more protection of the singleton's type.
    sharedInstance = [[Singleton alloc] init];
  });
  return sharedInstance;
}

- (instancetype)init {
  self = [super init];
  if (!self) return nil;

  // 🛡 An initializer override can provide a defensive measure against
  // unsupported subclassing.
  if ([self class] != [Singleton class]) {
    NSLog(@"This class discourages subclassing.");
    return nil;
  }

  return self;
}

- (void)performImportantOperation {
  // Perform an important operation.
}

@end

```

This alternative singleton implementation offers better protection of the singleton's type
and prevents derived types from hijacking the shared instance.

