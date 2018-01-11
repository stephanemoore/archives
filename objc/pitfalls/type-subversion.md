# Type Subversion

Type aliases can have associated semantic descriptions. This means that a type alias can have value beyond the
type it is an alias for. Type aliases can make code easier to understand. Using a type alias in a way that
conflicts with its semantic description subverts the value of the type alias.

Consider the type alias `dispatch_block_t`:
```Objective-C
typedef void (^dispatch_block_t)(void);
```

`dispatch_block_t` is ["the prototype of blocks submitted to dispatch queues, which take no arguments and have no return value."](https://developer.apple.com/documentation/dispatch/dispatch_block_t?language=objc).
The use of `dispatch_block_t` for any block that is not submitted to a dispatch queue undermines the value of
the type alias and its semantic meaning.

Avoid using type aliases in ways that conflict with their semantic descriptions. Instead, create new type aliases
with appropriate semantic descriptions or use a type that has no associated semantic description.
