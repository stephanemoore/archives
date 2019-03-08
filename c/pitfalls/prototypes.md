# Prototypes

Empty parameter lists in C specify no information about the number or types of the
supplied parameters. An unnamed parameter of type `void` is the appropriate way to
declare that a function has no parameters.

```c
// ❌ No information supplied about parameters.
extern void Bad();

// ✅ Function takes no arguments.
extern void Good(void);
```

C99 §6.7.5.3 states the following rules for function declarators:
> The special case of an unnamed parameter of type void as the only item in the list
> specifies that the function has no parameters. […] The empty list in a function
> declarator that is not part of a definition of that function specifies that no
> information about the number or types of the parameters is supplied."

The [-Wstrict-prototypes](https://clang.llvm.org/docs/DiagnosticsReference.html#wstrict-prototypes)
diagnostic flag can be used to generate warnings for function declarations that do
not have prototypes.

Note that empty parameter lists are treated differently in C++11 per C++11 §8.3.5:
> If the parameter-declaration-clause is empty, the function takes no arguments. A
> parameter list consisting of a single unnamed parameter of non-dependent type void
> is equivalent to an empty parameter list.
