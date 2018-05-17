# Initialization

## Objects With Static Storage Duration

Objects with static storage duration are initialized according to the following rules from C99 §6.7.8/10:

> If an object that has static storage duration is not initialized explicitly, then:  
> — if it has pointer type, it is initialized to a null pointer;  
> — if it has arithmetic type, it is initialized to (positive or unsigned) zero;  
> — if it is an aggregate, every member is initialized (recursively) according to these rules;  
> — if it is a union, the first named member is initialized (recursively) according to these rules.

## Omitted Members of Aggregate Types

Members of aggregate types without initializers in initializer lists are initialized using the following rules from C99 §6.7.8/21:

> If there are fewer initializers in a brace-enclosed list than there are elements or members of an aggregate, or fewer characters in a string literal used to initialize an array of known size than there are elements in the array, the remainder of the aggregate shall be initialized implicitly the same as objects that have static storage duration.
