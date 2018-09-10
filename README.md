Solidity dev tips

https://blog.aragon.org/library-driven-development-in-solidity-2bebcaf88736/
https://ethereum.stackexchange.com/questions/13167/are-there-well-solved-and-simple-storage-patterns-for-solidity
https://solidity.readthedocs.io/en/v0.4.21/contracts.html

Questions:
- How does storage work, why do you use it
- How to push to array


View This is generally the replacement for constant. It indicates that the function will not alter the storage state in any way. 
Pure This is even more restrictive, indicating that it won't even read the storage state. 
A pure function might look something like this very contrived example:

https://ethereum.stackexchange.com/questions/28898/when-to-use-view-and-pure-in-place-of-constant
https://truffleframework.com/docs/truffle/testing/writing-tests-in-javascript


* state variables are always in storage
* function arguments are in memory by default
* local variables of struct, array or mapping type reference storage by default
* local variables of value type (i.e. neither array, nor struct nor mapping) are stored in the stack
https://ethereum.stackexchange.com/questions/7058/reading-values-from-a-contract-when-do-i-need-transactions

https://ethereum.stackexchange.com/questions/3285/how-to-get-return-values-when-function-with-argument-is-called
https://michalzalecki.com/ethereum-test-driven-introduction-to-solidity/

https://truffleframework.com/docs/truffle/testing/writing-tests-in-solidity

https://github.com/trufflesuite/truffle-core/blob/develop/lib/testing/Assert.sol


https://ethereum.stackexchange.com/questions/13021/how-can-you-figure-out-if-a-certain-key-exists-in-a-mapping-struct-defined-insi

https://truffleframework.com/docs/truffle/reference/truffle-commands


https://ethereum.stackexchange.com/questions/41407/what-caused-this-error-attempting-to-run-transaction-which-calls-a-contract-fun

https://github.com/alianse777/solidity-standard-library/blob/master/Array.sol




https://sourcegraph.com/search?q=repo:%5Egithub%5C.com/AugurProject/augur-core%24%400f6d921+publicCreateOrder#12




https://blog.cotten.io/thinking-in-solidity-6670c06390a9


If you use for (var i = 0; i < a.length; i ++) { a[i] = i; }, then the type of i will be inferred only from 0, whose type is uint8. This means that if a has more than 255 elements, your loop will not terminate because i can only hold values up to 255.


The types where the so-called storage location is important are structs and arrays. If you e.g. pass such variables in function calls, their data is not copied if it can stay in memory or stay in storage. This means that you can modify their content in the called function and these modifications will still be visible in the caller.



There are defaults for the storage location depending on which type of variable it concerns:
* state variables are always in storage
* function arguments are always in memory
* local variables always reference storage
Example:
contract C {
    uint[] data1;
    uint[] data2;
    function appendOne() {
        append(data1);
    }
    function appendTwo() {
        append(data2);
    }
    function append(uint[] storage d) {
        d.push(1);
    }
}
The function append can work both on data1 and data2 and its modifications will be stored permanently. If you remove the storage keyword, the default is to use memory for function arguments. This has the effect that at the point where append(data1) or append(data2) is called, an independent copy of the state variable is created in memory and append operates on this copy (which does not support .push - but that is another issue). The modifications to this independent copy do not carry back to data1 or data2.


