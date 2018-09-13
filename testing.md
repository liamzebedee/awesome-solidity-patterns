# Testing
Not only does Test-Driven Development make your codebase stellar, it's extremely useful when interacting with a new language with different semantics. And surprisingly, it's actually not so hard in Ethereum development to get started with it!

The Truffle framework makes it very easy to test contracts with Solidity and JS. Beware:
 * while you can test contracts in Solidity, I highly advise you don't. The tooling is *very* nascent, and Solidity itself has very few libraries already - you won't be able to load test data (fixtures) from files for example, since Solidity doesn't have an FS API like JS/Node does.
 * for unit testing simple functions, Solidity comes in handy. However you might find using Remix IDE just as quick for accomplishing this to start with (although you won't reap the benefits of having a test later when your code breaks ;)).

## Unit testing
Unit testing is for testing individual functions of your contracts. 

## Integration testing
Integration testing generally requires multiple interactions from various users/contracts. Since this requires the use of multiple addresses, it is unsuitable to accomplish in Solidity as above.

### Exposing contracts
You may encounter difficulty testing contracts due to the (in)visibility of methods/types, the lack of insertion point for validating data being returned, and so on. It is possible to test not the contract itself, but a wrapped contract for testing purposes only.

```sol
contract Market {
  uint[] private orders;
  function doSomething() {}
}
```

How would we access `Market.orders` here?

```sol
contract MarketForTesting is Market {
  function getOrdersCount() public returns (uint8) { return orders.length; }
}
```

### Testing return values
When you call a method on a contract, and it updates state (i.e. is not `view`/`pure`), it must be transacted upon the network. In such case, the Web3 interfaces do not give you the return value of the method. This can be frustrating if you're trying to test such a value, so you can do something like so:;

```js
async function txWithReturnValue(method, ...args) {
  let [retval, tx] = await Promise.all([
    method.call(...args),
    method.sendTransaction(...args)
  ]);
  return retval;
}

await txWithReturnValue(Contract.method, 1, "0x123", { from: "0x12312", value: 2 }); 
```

### Testing with various 'users'
You will want to test the contract's interaction from the POV of multiple users with different addresses (whether it be human or other contracts). This is quite easy to achieve.

Whenever you transact with a contract in calling a method, you have the option to specify the `from` address of who will be funding the tx. Below is a Truffle test which combines this with access to `accounts`, which is the same output as web3.personal.getListAccounts:

```js
const MyContract = artifacts.require("./MyContract.sol");

contract('MyContract', async (accounts) => {
  it('tests with various users', async () => {
    let instance = await MyContract.new();
    for(let i = 0; i < 5; i++) {
      let from = accounts[i];
      console.log(`Submitting tx from addr: ${from}`);
      let txid = await instance.yourMethod.sendTransaction(arg1, arg2, { from, });
    }
  })
})
```



