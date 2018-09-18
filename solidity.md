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

## Time, dates, `block.timestamp`
> The one constraint that miners have is that the timestamps cannot be ahead of the current time. Hence, even though the individual timestamps may be off, in the long term the mean of the timestamps must be less than or equal to the actual mean. We also know that timestamps must be strictly increasing (ie. the timestamp differences are positive), and so from this we can mathematically derive the inequality that the mean must be at least half the median. The algorithm does an impeccable job of targeting the median to 13 seconds, so the mean block time cannot be pushed below 6.5s even with perfect collusion.
> [Vitalik on r/ethereum](https://www.reddit.com/r/ethereum/comments/3k2qvs/whats_to_stop_miners_from_lying_about_timestamps/cuudfcl/)
