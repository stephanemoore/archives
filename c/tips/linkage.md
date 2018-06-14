# Linkage

C's relationship with other languages—particularly C++—raises interesting considerations with respect
to linkage.

## Specifying C linkage

When sharing C functions between C and C++ variant languages it can be necessary to specify C linkage
to prevent C++ name mangling and linkage failures. Generally macros are used to properly specify C
linkage based on the target language:
```c
#ifdef __cplusplus
#define FOO_EXTERN extern "C"
#else
#define FOO_EXTERN extern
#endif

FOO_EXTERN void Foo(void);
```

For more information consult the [C++ Special rules for "C" linkage](https://en.wikipedia.org/wiki/Name_mangling#C++)
and the Wikipedia article on [Name Mangling in C++](https://en.wikipedia.org/wiki/Name_mangling#C++)
