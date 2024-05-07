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
## Contract Inheritance
Inheritance is the procedure by which one contract can inherit the attributes and methods of another contract. Solidity supports multiple inheritance. Contracts can inherit other contract by using the is keyword.

A parent contract which has a function that can be overridden by a child contract must be declared as a virtual function.

A child contract that is going to override a parent function must use the override keyword.

The order of inheritance matters if parent contracts share methods or attributes by the same name.

## ETH Transfers
There are three ways to transfer ETH from a contract to some other address.  Here will go over all of them analysing their strengths and weakness and conclude on the best of the 3.

1 **Using the _send_ function**

The send function sends a specified amount of Ether to an address. It is a low-level function that only provides a stipend of 2300 gas for execution. This amount of gas is only enough to emit an event. If the execution requires more gas, it will fail. The send function does not throw an exception when it fails, instead, it returns **false**. This means you should always check the result of send and handle the failure case manually

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

contract SendEther {
    function sendEth(address payable _to) public payable {
        // Just forward the ETH received in this payable function
        // to the given address
        uint amountToSend = msg.value;
  
        bool sent = _to.send(amountToSend);
        require(sent == true, "Failed to send ETH");
    }
}
```

2. **Using the _call_ function**

The call function is a low-level function that transfers Ether and also forwards all remaining gas. This means call can be used to send Ether and execute more complex operations that require more than 2300 gas. However, like send, call does not automatically revert the transaction if the transfer fails. Instead, it returns false. So you should always check the result and handle the failure case manually. Also, because call forwards all remaining gas, it can potentially enable re-entrancy attacks if not used carefully

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

contract SendEther {
    function sendEth(address payable _to) public payable {
        // Just forward the ETH received in this payable function
        // to the given address
        uint amountToSend = msg.value;
        // call returns a bool value specifying success or failure
        (bool success, bytes memory data) = _to.call{value: msg.value}("");
        require(success == true, "Failed to send ETH");
    }
}
```
3. **Using the _transfer_ function**

The transfer function also sends a specified amount of Ether to an address. Unlike send or call, transfer automatically throws an exception and reverts the transaction if the transfer fails. This makes transfer safer and easier to use because you do not need to manually check the result or handle the failure case. However, like send, transfer also only provides a stipend of 2300 gas for execution

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

contract SendEther {
    function sendEth(address payable _to) public payable {
        // Just forward the ETH received in this payable function
        // to the given address
        uint amountToSend = msg.value;
        
        // Use the transfer method to send the ETH.
        _to.transfer(msg.value);
    }
}
```
By using the .transfer method, we can make the code simpler and more straightforward. The .transfer method is also considered safer because it helps to avoid potential reentrancy attacks.

**NOTE:** When using .transfer, ensure you know that it will only forwards 2300 gas, thereby preventing reentrancy attacks. This might not be sufficient gas if the receiver contract needs to perform certain operations.

## Receiving ETH

If you are receiving ETH in an Externally Owned Account (EOA) i.e. an account controlled by a private key (like MetaMask) - you do not need to do anything special as all such accounts can automatically accept all ETH transfers.

However, if you are writing a contract that you want to be able to receive ETH transfers directly, you must have at least one of the functions below:

* receive() external payable

* fallback() external payable

receive() is called if msg.data is an empty value, and fallback() is used otherwise.

## Calling Other Contracts

Contracts can call other contracts by just calling functions on an instance of the other contract like A.foo(x, y, z). To do so, you must have an interface for A which tells your contract which functions exist. Interfaces in Solidity behave like header files, and serve similar purposes to the ABI we have been using when calling contracts from the frontend. This allows a contract to know how to encode and decode function arguments and return values for calling external contracts.

**NOTE:** Interfaces you use do not need to be extensive. i.e. they do not need to necessarily contain all the functions that exist in the external contract - only those which you might be calling at some point.
Assume there is an external ERC20 contract, and we are interested in calling the balanceOf function to check the balance of a given address from our contract.

## Importing Contracts

To maintain code readability, you can split your Solidity code over multiple files. Assume we have a folder structure that looks like this:
```
├── Import.sol
└── Foo.sol
```
where Foo.sol is
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

contract Foo {
    string public name = "Foo";
}
```

We can import Foo and use it in Import.sol as such
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

// import Foo.sol from current directory
import "./Foo.sol";

contract Import {
    // Initialize Foo.sol
    Foo public foo = new Foo();

    // Test Foo.sol by getting it's name.
    function getFooName() public view returns (string memory) {
        return foo.name();
    }
}
```

## Libraries
Libraries are similar to contracts in Solidity, with a few limitations. Libraries cannot contain any state variables, and cannot transfer ETH. Additionally, libraries are only deployed once to the network, which means if you are using a library published by someone else, you do not need to pay gas for it when you deploy your code to the network as Ethereum will understand that that library was already deployed in the past by someone else.

Typically, libraries are used to add helper functions to your contracts. An extremely commonly used library in Solidity world is SafeMath - which ensures that mathematical operations do not cause an integer underflow or overflow.
