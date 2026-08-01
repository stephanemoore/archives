# Concurrency

## Documentation

Document concurrency models to make it easier to read and maintain code.

Memory that is protected by locks, dispatch queues, or other concurrency
constructs should have comments explaining the concurrency model.

Memory that is expected to be immutable should have comments explaining any
constraints on immutability.

```Objective-C

@implementation Example {
  /**
   * A queue of data to upload.
   *
   * This ivar must be accessed or mutated on _uploadQueue.
   */
  NSArray<NSData *> *_queuedUploads;

  /**
   * A serial dispatch queue for uploading data.
   *
   * _queuedUploads must be accessed or mutated on this queue.
   *
   * This ivar is immutable after initialization.
   */
  dispatch_queue_t _uploadQueue;

  /**
   * The user's access token.
   *
   * This ivar must be accessed or mutated while holding _accessTokenLock.
   */
  NSString *_accessToken;

  /**
   * The lock controlling access to the user's access token.
   *
   * _accessToken must be accessed or mutated while holding this lock.
   *
   * This ivar is immutable after initialization.
   */
  os_unfair_lock_t _accessTokenLock;

  /**
   * The user agent to use for uploads.
   *
   * This ivar is immutable after initialization.
   */
  NSString *_userAgent;
}

// ...

@end
```
