## Augur-style

[Augur-style](https://sourcegraph.com/github.com/AugurProject/augur-core@master/-/blob/source/contracts/trading/Order.sol) data encapsulation. 

### Characteristics

 * `library` defined for a named entity. e.g. `Order`
 * `struct Data {}` within the library is the abstract data type to be passed around. e.g. `Order.Data`
 * function `create` which returns an instance of `Data`. 
 * other related types (such as enum's and constants) are also encapsulated by this library. e.g. `Order.Types`
 * pure helper functions that compute higher-level information on this entity. e.g. `getOrderId` which computes the ID from the hash of the data

### Example
```sol
library Order {
  struct Data {
        // Contracts
        IOrders orders;
        IMarket market;
        IAugur augur;

        // Order
        bytes32 id;
        address creator;
        uint256 outcome;
        Order.Types orderType;
        uint256 amount;
        uint256 price;
        uint256 sharesEscrowed;
        uint256 moneyEscrowed;
        bytes32 betterOrderId;
        bytes32 worseOrderId;
    }
  
  enum Types {
    Bid, Ask
  }
  
  function create(IController _controller, address _creator, uint256 _outcome, Order.Types _type, uint256 _attoshares, uint256 _price, IMarket _market, bytes32 _betterOrderId, bytes32 _worseOrderId) internal view returns (Data) {
        require(_outcome < _market.getNumberOfOutcomes());
        require(_price < _market.getNumTicks());
        require(_attoshares > 0);

        IOrders _orders = IOrders(_controller.lookup("Orders"));
        IAugur _augur = _controller.getAugur();

        return Data({
            orders: _orders,
            market: _market,
            augur: _augur,
            id: 0,
            creator: _creator,
            outcome: _outcome,
            orderType: _type,
            amount: _attoshares,
            price: _price,
            sharesEscrowed: 0,
            moneyEscrowed: 0,
            betterOrderId: _betterOrderId,
            worseOrderId: _worseOrderId
        });
    }
  
  function getOrderId(Order.Data _orderData) internal view returns (bytes32) {
        if (_orderData.id == bytes32(0)) {
            bytes32 _orderId = _orderData.orders.getOrderId(_orderData.orderType, _orderData.market, _orderData.amount, _orderData.price, _orderData.creator, block.number, _orderData.outcome, _orderData.moneyEscrowed, _orderData.sharesEscrowed);
            require(_orderData.orders.getAmount(_orderId) == 0);
            _orderData.id = _orderId;
        }
        return _orderData.id;
    }
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
