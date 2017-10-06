# Designated Initializers

Designated initializers, introduced in C99, are a nice mechanism for initializing arrays and structs.

Array initialization using a designated initializer:
```c
char designatedInitializationArrayExample[8] = {
  [7] = '\0',
  [6] = 'e',
  [5] = 'l',
  [4] = 'p',
  [3] = 'm',
  [2] = 'a',
  [1] = 'x',
  [0] = 'e'
};

char compoundLiteralArrayExample[8] = { 'e', 'x', 'a', 'm', 'p', 'l', 'e', '\0' };

assert(strcmp(designatedInitializationArrayExample, compoundLiteralArrayExample) == 0);
```

Struct initialization using a designated initializer:
```c
typedef struct ExampleStruct {
  int x;
  int y;
  char ch;
} ExampleStruct;

ExampleStruct designatedInitializationStructExample = { .ch = 'a' };
ExampleStruct compoundLiteralStructExample = { 0, 0, 'a' };

assert(designatedInitializationStructExample.x == compoundLiteralStructExample.x);
assert(designatedInitializationStructExample.y == compoundLiteralStructExample.y);
assert(designatedInitializationStructExample.ch == compoundLiteralStructExample.ch);

```

Note that GCC and clang will implicitly initialize omitted field members in the same way
as objects that have static storage duration
(see [GCC §6.27 Designated Initializers](https://gcc.gnu.org/onlinedocs/gcc/Designated-Inits.html)).
