## Unit testing
Unit testing is for specific functions

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
