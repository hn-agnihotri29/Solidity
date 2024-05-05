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
The word Enum stands for Enumerable. They are user defined types that contain human readable names for a set of constants, called members. They are commonly used to restrict a variable to only have one of a few predefined values. Since they are just an abstraction for human readable constants, in actuality, they are internally represented as uints.

