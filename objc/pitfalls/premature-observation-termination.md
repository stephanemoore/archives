# Beware Premature Observation Termination

A pitfall that sometimes presents itself in software design is the premature termination of event observation.
This is more commonly observed when using object-based observation APIs.

In Objective-C an API that was particularly vulnerable to this form of error was NSNotificationCenter.
Consider the following example:

```Objective-C
// FOOExampleNotification.h

#import <Foundation/Foundation.h>

FOUNDATION_EXTERN NSNotificationName const FOOExampleNotification;
```

```Objective-C
// ObservingBaseClass.h

#import <Foundation/Foundation.h>

/** A base class that happens to observe FOOExampleNotification. */
@interface ObservingBaseClass : NSObject

/**
 * A public declaration of the notification-handling method to avoid accidental subclass overrides.
 * Note that despite this declaration subclasses can still accidentally disrupt superclass functionality.
 */
- (void)baseClassReceivedNotification:(NSNotification *)notification NS_REQUIRES_SUPER;

@end
```

```Objective-C
// ObservingBaseClass.m

#import "ObservingBaseClass.h"

#import "FOOExampleNotification.h"

@implementation ObservingBaseClass {
  NSDate _lastObservedEvent;
}

- (instancetype)init {
  self = [super init];
  if (!self) return nil;

  [NSNotificationCenter.defaultCenter addObserver:self
                                         selector:@selector(baseClassReceivedNotification:)
                                             name:FOOExampleNotification
                                           object:nil];
  return self;
}

- (void)baseClassReceivedNotification:(NSNotification *)notification {
  _lastObservedEvent = [NSDate date];
}

@end
```

```Objective-C
// UnsafeDerivedClass.h

#import "UnsafeDerivedClass.h"

/** A subclass that also listens to FOOExampleNotification. */
@interface UnsafeDerivedClass : ObservingBaseClass

/** Stops listening to FOOExampleNotification. */
- (void)stopListeningToNotification;

@end
```

```Objective-C
// UnsafeDerivedClass.m

#import "UnsafeDerivedClass.h"

#import "FOOExampleNotification.h"

@implementation UnsafeDerivedClass

- (instancetype)init {
  self = [super init];
  if (!self) return nil;

  [NSNotificationCenter.defaultCenter addObserver:self
                                         selector:@selector(subclassReceivedNotification:)
                                             name:FOOExampleNotification
                                           object:nil];
}

- (void)stopListeningToNotification {
  [NSNotificationCenter.defaultCenter removeObserver:self
                                                name:FOOExampleNotification
                                              object:nil];
}

- (void)subclassReceivedNotification:(NSNotification *)notification {
  // Subclass implementation of responding to the notification.
}

@end
```

As seen in the example above, if an instance of UnsafeDerivedClass were created and
‑[UnsafeDerivedClass stopListeningToNotification] were called then the object's ivar _lastObservedEvent
(inherited via ObservingBaseClass) would fail to be updated on subsequent FOOExampleNotification
notifications. This could potentially lead to invalid application states and unexpected behaviors.

This problem can be avoided in a couple ways:
* Avoid observation termination outside of ‑dealloc and use flags to discard undesired events.
* Favor observation APIs that are not vulnerable to observation manipulation in subclasses, e.g.,
‑[NSNotificationCenter addObserverForName:object:queue:usingBlock:].
* Document the notification observations of a class so that subclasses can be designed to avoid causing unexpected behaviors.
