Remix IDE
https://remix.ethereum.org/#optimize=false&runs=200&evmVersion=null&version=soljson-v0.8.7+commit.e28d00a7.js

Bits vs Bytes
https://www.youtube.com/watch?v=Dnd28lQHquU


License
Pragma solidity
Solidity types
```solidity
pragma solidity 0.8.8;

contract SimpleStorage {  // just like class
    // bool, uint, int, address, bytes
    bool hasFavoriteNumber = true;
    uint256 favoriteNumber = 5;
    string favoriteNumberInText = "Five";
    int favoriteInt = -5;
    address myAddress = 0x7999206CAdeCA14c07c3955CD450150e6c8C51e5;
    bytes32 favoriteBytes = "cat"; // automatically convert into byte object, maximum size of 32
}
```


Function Visibility Specifiers
- Public
- Private
- external
- internal

Everytime we call a function in remix, we call a transaction.

We only pay gas if we change blockchain state, function that is free is view and pure.
```
  // view, pure doesnt need gas, and 
    function retrieve() public view returns(uint256){
        return favoriteNumber;
    }
}
```


View / pure function are free unless you call it inside a function that cost gas.
```
 function store(uint256 _favoriteNumber) public {
        favoriteNumber = _favoriteNumber;
        retreive(); // it will cost gas
    }

    // view, pure doesnt need gas, and 
    function retrieve() public view returns(uint256){
        return favoriteNumber;
    }
}
```