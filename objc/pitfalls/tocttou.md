# Time Of Check To Time Of Use

[Time of check to time of use (TOCTTOU) bugs](https://en.wikipedia.org/wiki/Time_of_check_to_time_of_use)
occur due to changes between evaluating a condition and acting upon the condition.

```objective-c
- (void)notifyDelegate {
  if ([self.delegate respondsToSelector:@selector(foo)]) {
    [self.delegate foo]; // 💥 self.delegate could have changed since the condition was evaluated.
  }
}
```

TOCTTOU bugs are common in code that operates on multiple threads but they can also
manifest in code that runs on a single thread. TOCTTOU bugs can be resolved in various
ways. In some cases a local variable is an appropriate way to resolve such bugs.

```objective-c
- (void)notifyDelegate {
  // 👌 A local variable ensures that the same object is used for checking the
  // condition and performing the action.
  id<FOODelegate> delegate = self.delegate;
  if ([delegate respondsToSelector:@selector(foo)]) {
    [delegate foo];
  }
}
```

Mutexes are another solution that resolves TOCTTOU bugs though they introduce locking behavior.
Consider the desired behavior and the desired performance characteristics when addressing
TOCTTOU bugs.
