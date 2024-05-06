## Mappings
Mappings in Solidity act like hashmaps or dictionaries in other programming languages. They are used to store the data in key-value pairs.
They are created using the syntax mapping(keyType ⇒ valueType)

We can also create nested mappings, where each value in a mapping points to another mapping. To do so, we set the valueType to a mapping itself.
```
contract NestedMappings {
    // Mapping from address => (mapping from uint to bool)
    mapping(address => mapping(uint => bool)) public nestedMap;

    function get(address _addr1, uint _i) public view returns (bool) {
        // You can get values from a nested mapping
        // even when it is not initialized
        // The default value for a bool type is false
        return nestedMap[_addr1][_i];
    }

    function set(
        address _addr1,
        uint _i,
        bool _boo
    ) public {
        nestedMap[_addr1][_i] = _boo;
    }

    function remove(address _addr1, uint _i) public {
        delete nestedMap[_addr1][_i];
    }
}
```

## Enums
The word Enum stands for Enumerable. They are user defined types that contain human readable names for a set of constants, called members. They are commonly used to restrict a variable to only have one of a few predefined values. Since they are just an abstraction for human readable constants, in actuality, they are internally represented as units.


## View and Pure Functions
You might have noticed that some of the functions we have been writing specify one of either a view or pure keyword in the function header. These are special keywords which indicate specific behavior for the function.

Getter functions (those which return values) can be declared either view or pure.

* View: Functions which do not change any state values

* Pure: Functions which do not change any state values and also do not read any state values
  

## Modifiers
Modifiers are code that can be run before and/or after a function call. They are commonly used for restricting access to certain functions, validating input parameters, protecting against certain types of attacks, etc.


## Events
Events allow contracts to perform logging on the Ethereum blockchain. Logs for a given contract can be parsed later to perform updates on the frontend interface, for example. They are commonly used to allow frontend interfaces to listen for specific events and update the user interface, or used as a cheap form of storage.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

contract Events {
    // Declare an event which logs an address and a string
    event TestCalled(address sender, string message);

    function test() public {
        // Log an event
        emit TestCalled(msg.sender, "Someone called test()!");
    }
}
```
## Constructors
A constructor is an optional function that is executed when the contract is first deployed. Unlike other functions, constructors cannot be manually called ever again after the contract has been deployed, therefore it can only ever run once. You can also pass arguments to constructors before the contract is deployed.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

contract X {
    string public name;

    // You will need to provide a string argument when deploying the contract
    constructor(string memory _name) {
        // This will be set immediately when the contract is deployed
        name = _name;
    }
}
```
