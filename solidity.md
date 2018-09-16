## Checking for null/None
Solidity does not have a null type:
 * you cannot do `if(structVar == null)`
 * you cannot check if a key on a mapping exists or not [see here](https://ethereum.stackexchange.com/questions/13021/how-can-you-figure-out-if-a-certain-key-exists-in-a-mapping-struct-defined-insi)

Every value allocated automatically receives a default, which is usually 0x0 or false. Reasoning? Likely to reduce foot-shooting mechanisms, increase memory safety and reduce complexity of the language for newcomers.

## `storage` and `memory`
Very simply speaking, if an argument or variable is annotated with `storage`, it means that it's not getting reallocated when you're passing it around - it's a reference.

 * state variables are always in storage
 * function arguments are in memory by default
 * local variables of struct, array or mapping type are reference storage by default
 * local variables of value type (i.e. neither array, nor struct nor mapping) are stored in the stack
