# Macros

## Stringizing/Stringification

Macro arguments can be converted into string constants using the `#` operator
(see [GCC stringizing documentation](https://gcc.gnu.org/onlinedocs/cpp/Stringizing.html#Stringizing)).

#### Example Program:
```c
#include <stdio.h>

#define EvaluateIntegerExpression(expr) printf("(%s) = %d\n", #expr, (expr))

int main(int argc, char **argv) {
  EvaluateIntegerExpression(1+5);
  return 0;
}
```

#### Output:
```
(1+5) = 6
```
