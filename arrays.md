```sol
contract DynArray {
    uint8[] public arr;
    
    constructor() {
        arr = new uint8[](0);
    }
    
    function getArray() returns (uint8[]) {
        return arr;
    }
    
    function getNumItems() public view returns (uint256) {
        return arr.length;
    }
    
    function addTo(uint8 v) 
        public 
    {
        arr.push(v);
    }
}
```

Arrays must be allocated, otherwise accessing them will return an error opcode. In this example, `getArray()` would fail if we did not allocate the array in the constructor.

`getArray()` works only in Web3 environments, and typically not in intra-contract calls. 

`arr.push` will increase the size of an array dynamically.
