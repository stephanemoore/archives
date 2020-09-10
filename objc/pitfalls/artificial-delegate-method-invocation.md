# Artificial Delegate Method Invocation

Classes often implement delegate protocols so that external objects can
communicate events to the class. The methods in delegate protocols sometimes
have well-defined rules or lifecycles indicating how and when the methods
are called or what assumptions can be made when those methods are called.

In some cases during development, another method might see a need to
invoke logic contained within a delegate method. In such cases, it can be
tempting to directly invoke the delegate method like so:
```Objective-C
#import <Foundation/Foundation.h>
#import <CoreLocation/CoreLocation.h>

@protocol ErrorLogger <NSObject>
- (void)logError:(NSError *)error;
@end

@interface BadLocationObserver : NSObject <CLLocationManagerDelegate>
@end

@implementation BadLocationObserver {
  CLLocationManager *_locationManager;
  id<ErrorLogger> *_logger;
}

- (instancetype)initWithLocationManager:(CLLocationManager *)locationManager logger:(id<ErrorLogger>)logger {
  self = [super init];
  if (self) {
    _locationManager = locationManager;
    locationManager.delegate = self;
    _logger = logger;
  }
  return self;
}

- (void)locationManager:(CLLocationManager *)locationManager didFailWithError:(NSError *)error {
  [_logger logError:error];
}

- (void)encounteredRPCError:(NSError *)error {
  // ❌ -locationManager:didFailWithError: should only be called for CLLocationManager failures. 
  [self locationManager:_locationManager didFailWithError:error];
}

@end
```

The problem with taking a potentially convenient shortcut like this is that
any well-defined rules, lifecycles, or assumptions associated with
`-locationManager:didFailWithError:` may no longer be valid for this class
because it manually invokes the delegate method. Among other things, this
increases the difficulty of reasoning about the behavior of the class.

To avoid this, we can instead refactor out the internals of the delegate method
into a helper method and call that from the delegate method and the other method:
```Objective-C
#import <Foundation/Foundation.h>
#import <CoreLocation/CoreLocation.h>

@protocol ErrorLogger <NSObject>
- (void)logError:(NSError *)error;
@end

@interface BetterLocationObserver : NSObject <CLLocationManagerDelegate>
@end

@implementation BetterLocationObserver {
  CLLocationManager *_locationManager;
  id<ErrorLogger> *_logger;
}

- (instancetype)initWithLocationManager:(CLLocationManager *)locationManager logger:(id<ErrorLogger>)logger {
  self = [super init];
  if (self) {
    _locationManager = locationManager;
    locationManager.delegate = self;
    _logger = logger;
  }
  return self;
}


// ✅ -locationManager:didFailWithError: is only called for CLLocationManager failures.
- (void)locationManager:(CLLocationManager *)locationManager didFailWithError:(NSError *)error {
  [self logError:error];
}

- (void)encounteredRPCError:(NSError *)error {
  // ✅ This method no longer sends a false signal that a CLLocationManager failure occurred.
  [self logError:error];
}

- (void)logError:(NSError *)error {
  [_logger logError:error];
}

@end
```

With a simple refactor, we can maintain the integrity of the well-defined
rules, lifeycles, and assumptions associated with the delegate method and
also share logic between the delegate method and other methods.
