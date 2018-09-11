## Augur-style

[Augur-style](https://sourcegraph.com/github.com/AugurProject/augur-core@master/-/blob/source/contracts/trading/Order.sol) encapsulation with Data and .create.
```sol
library Order {
  struct Data {
  }
  
  function create() public pure returns (Data) {}
}
```

## Monax-style
[Monax's "Five Types"](https://github.com/monax/legacy-docs/blob/master/solidity/solidity_1_the_five_types_model.md) model:

#### 1) Database contracts

These are used only as data storage. The only logic they need is functions that allow other contracts to write, update and get data, and some simple way of checking caller permissions (whatever those permissions may be).

#### 2) Controller contracts

These contracts operate on the storage contracts. In a flexible system, both controllers and databases can be replaced by other, similar contracts that share the same public api (although this is not always needed). Controllers can be advanced, and could for example do batched reads/writes, or read from and write to multiple different databases instead of just one.

#### 3) Contract managing contracts (CMCs)

The purpose of these contracts is only to manage other contracts. Their main tasks is to keep track of all the contracts/components of the system, handle the communication between these components, and to make modular design easier. Keeping this functionality separate from normal business logic should be considered good practice, and has a number of positive effects on the system (as we will see later).

#### 4) Application logic contracts (ALCs)

Application logic contracts contains application-specific code. Generally speaking, if the contract utilizes controllers and other contracts to perform application specific tasks it's an ALC.

#### 5) Utility contracts

These type of contracts usually perform a specific task, and can be called by other contracts without restrictions. It could be a contract that hashes strings using some algorithm, provide random numbers, or other things. They normally don't need a lot of storage, and often have few or no dependencies.
