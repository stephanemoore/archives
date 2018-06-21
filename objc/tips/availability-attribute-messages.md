# Availability Attribute Messages

## Messages on `deprecated` and `unavailable` Attributes
clang provides a language extension that supports
[messages on `deprecated` and `unavailable` attributes](https://clang.llvm.org/docs/LanguageExtensions.html#messages-on-deprecated-and-unavailable-attributes).

The example below demonstrates a message set on a `deprecated` attribute:
```Objective-C
#if __has_extension(attribute_deprecated_with_message)
#define FOO_DEPRECATED(...) __attribute__((deprecated("" __VA_ARGS__)))
#else
#define FOO_DEPRECATED(...) __attribute__((deprecated))
#endif

extern void bar(void) FOO_DEPRECATED("Use baz instead.");
```

The example below demonstrates a message set on an `unavailable` attribute:
```Objective-C
#if __has_extension(attribute_unavailable_with_message)
#define FOO_UNAVAILABLE(...) __attribute__((unavailable("" __VA_ARGS__)))
#else
#define FOO_UNAVAILABLE(...) __attribute__((unavailable))
#endif

extern void foo(void) FOO_UNAVAILABLE("This function is not safe to use.");
```

Specifying messages on `deprecated` and `unavailable` attributes can be a great
way to provide immediate information to engineers regarding the symbol they are
attempting to use.
