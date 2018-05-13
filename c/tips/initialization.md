# Initialization

## Objects With Static Storage Duration

Objects with static storage duration are initialized according to the following rules from C99 §6.7.8/10:

> If an object that has static storage duration is not initialized explicitly, then:  
> — if it has pointer type, it is initialized to a null pointer;  
> — if it has arithmetic type, it is initialized to (positive or unsigned) zero;  
> — if it is an aggregate, every member is initialized (recursively) according to these rules;  
> — if it is a union, the first named member is initialized (recursively) according to these rules.
