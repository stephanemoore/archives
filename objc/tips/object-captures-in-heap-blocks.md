# Object Captures in Heap Blocks

Under ARC it is important to be mindful of heap block captures of
Objective-C objects, how they affect object lifetime, and what the
desired object lifecycle behavior is for particular scenarios.

## Use Strong References to Extend Captured Object Lifetime

In some situations, we want a block to capture a strong reference to
an object and extend its lifetime along with the lifetime of the block.

For example, imagine a scenario where a controller issues a network
request with the intent to persist a token obtained from the response.

Consider the following snippet reflecting this scenario:
```Objective-C
  [networkService sendRequest:request
                   completion:^(NSString *response, NSError *error) {
    if (error) {
      [controller recordError:error forRequest:request];
    } else {
      [controller persistTokenFromServerResponse:response];
    }
  }];
```

This implementation ensures that the controller is still in-memory
in order to persist the token from server response once it arrives.
If we had instead captured a weak reference, the controller may have
deallocated and the token would not have been persisted which could
potentially lead to unexpected behavior.

Extending an object's lifetime is generally important when an object
must perform critically important tasks when the block is invoked.

## Use Weak References to Avoid Altering Captured Object Lifetime

In some situations, we want a block to capture a weak reference to an
object without affecting the lifetime of the object.

For example, consider a scenario where a controller issues a network
request with the intent to update a user interface with the response.
In such a case it may be desired that the controller only react to
the network response if the user has not dismissed the user interface.

Consider the following snippet reflecting this scenario:
```Objective-C
  // Store a weak reference to be captured in the block to avoid
  // affecting the retain count of the controller.
  __weak __typeof__(controller) weakController = controller;
  [networkService sendRequest:request
                   completion:^(NSString *response, NSError *error) {
    // Resolve the weak reference to a strong reference.
    __weak __typeof__(controller) strongController = weakController;

    // Nil-messaging ensures that the operations below will be no-ops
    // if the controller happens to have deallocated since the
    // request was sent.
    if (error) {
      [strongController displayError:error];
    } else {
      [strongController updateUserInterfaceWithResponse:response];
    }
  }];
```

With this implementation, if the user interface has been dismissed
and all references to the controller have been released, the
controller can deallocate and the network response is silently
discarded. If a strong reference were captured instead, the
controller would survive until the network response is received
and could create unnecessary overhead or cause unexpected behavior
by mutating global state.

One notable usage of weak references in heap block captures is to
prevent retain cycles between blocks and captured objects. This can
often be important for long-lived blocks, e.g., event-handling
blocks.

In general, using a weak reference to avoid changing an object's
lifetime is appropriate when an object should only perform
operations if it is still in-memory or to prevent retain cycles.

### Block Parameters Annotated with the `noescape` Attribute

Weak references are generally not necessary for blocks provided
as arguments to block parameters annotated with the
[noescape](https://clang.llvm.org/docs/AttributeReference.html#noescape)
attribute. This is because the noescape attribute specifies that
the lifetime of the block argument will not extend beyond the
invocation of the method or function.

## When should I extend a heap block captured object's lifetime?

There is no recommendation that is optimal for all scenarios.
Use a strong reference to extend an object's lifetime or a weak
reference to avoid altering an object's lifetime depending on
the circumstances.
