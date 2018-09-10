awesome-solidity-patterns
=========================

An accomplice to the [Consensys "Smart Contract Best Practices"](https://consensys.github.io/smart-contract-best-practices/), but with less security and more engineering focus. WIP.

Copyright 2018 Liam Zebedee and contributors.

## Foreward
While Solidity on the outside looks like JS, functions like C, its semantics are quite unique and different. It is birthed as a "smart contract" programming language, however you can better understand its strange decisions by the constraints imposed by running atop the Ethereum blockchain:

- storage is **very expensive**
- money/value is data
- computation is transactioned, state is rationed

You can see this in many ways: 
 * basic operations on data types are difficult/missing: iteration on maps, non-existent keys are 0x0 (even for structs their fields are all 0x0) 
 * returning structs from external methods is impossible - rather, it returns tuples for now due to the ABI version
 * types are generally much more plentiful in number, but lower-level in nature (bytes32, bytes64, uint256)
 * contracts cannot respond natively to "timed events" - they must be interacted with via calls, or by decentralized services such as the Ethereum Alarm Clock
 * all interactions are based on the primitive of messages, contract instantiations are sending eth to an address (which is in itself the hash of the contract), everything functions as an address (wallets and contracts alike)
 * since protocol messages are padded, features such as dynamic arrays do not work in places you expect
 * upgradeability is not built-in
 * no ubiquitous package management yet

## A Guide
I've tried to organise this guide into various topics that commonly crop up in the software engineering practice of building smart contracts.

 - **[Data modelling](https://github.com/liamzebedee/awesome-solidity-patterns/blob/master/data-modelling.md)**: the common patterns of separating concerns (i.e. MVC)
 - **[Testing](https://github.com/liamzebedee/awesome-solidity-patterns/blob/master/testing.md)**: the simplest patterns for writing unit and integration tests for smart contract systems / dApps.
 - **[Solidity semantics](https://github.com/liamzebedee/awesome-solidity-patterns/blob/master/solidity.md)**: common gotcha's/suprises in the semantics of Solidity
