# LOW FINDINGS

##

### [L-1] Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from other types

FILE : 2023-03-zksync/contracts/ImmutableSimulator.sol

      27: return immutableDataStorage[uint256(uint160(_dest))][_index];

[ImmutableSimulator.sol#L27](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ImmutableSimulator.sol#L27)

     40: immutableDataStorage[uint256(uint160(_dest))][index] = value;

[ImmutableSimulator.sol#L40](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ImmutableSimulator.sol#L40)

FILE : 2023-03-zksync/contracts/L1Messenger.sol

     49:  SystemContractHelper.toL1(true, bytes32(uint256(uint160(msg.sender))), hash);

[L1Messenger.sol#L49](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L49)

FILE: 2023-03-zksync/contracts/MsgValueSimulator.sol

    29: to = address(uint160(addressAsUint));

[MsgValueSimulator.sol#L29](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/MsgValueSimulator.sol#L29)

   60 : SystemContractHelper.setValueForNextFarCall(uint128(value));

[MsgValueSimulator.sol#L60](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/MsgValueSimulator.sol#L60)

##

### [L-2] LOSS OF PRECISION DUE TO ROUNDING

FILE : 2023-03-zksync/contracts/L1Messenger.sol

   37: pubdataLen = ((_message.length + 31) / 32) * 32 + 64;

[L1Messenger.sol#L37](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L37)

##

### [L-3] REQUIRE MESSAGES ARE TOO SHORT AND UNCLEAR

FILE : 2023-03-zksync/contracts/L1Messenger.sol
 
   47: require(precompileCallSuccess);

[L1Messenger.sol#L47](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L47)

##

### [L-4] 


# NON CRITICAL FINDINGS 

##

### [NC-1] USE A MORE RECENT VERSION OF SOLIDITY

Latest solidity version is 0.8.19 

FILE : 2023-03-zksync/contracts/ImmutableSimulator.sol

   3: pragma solidity ^0.8.0;

[ImmutableSimulator.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ImmutableSimulator.sol#L3)

FILE : 2023-03-zksync/contracts/L1Messenger.sol

   3: pragma solidity ^0.8.0;

[L1Messenger.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L3)

FILE: 2023-03-zksync/contracts/MsgValueSimulator.sol

   3: pragma solidity ^0.8.0;

[MsgValueSimulator.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/MsgValueSimulator.sol#L3)

FILE : 2023-03-zksync/contracts/BytecodeCompressor.sol

   3: pragma solidity ^0.8.0;

[BytecodeCompressor.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L3)

Recommended Mitigation :

Consider using latest solidity version at least 0.8.17

##

### [NC-2] Named imports can be used

It’s possible to name the imports to improve code readability. E.g. import "@openzeppelin/contracts/token/ERC20/IERC20.sol"; can be rewritten as import {IERC20} from “import “@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol”

FILE : 2023-03-zksync/contracts/ImmutableSimulator.sol

   5: import "./interfaces/IImmutableSimulator.sol";


[ImmutableSimulator.sol#L5](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ImmutableSimulator.sol#L5)

FILE : 2023-03-zksync/contracts/L1Messenger.sol

     5: import "./interfaces/IL1Messenger.sol";
     6: import "./libraries/SystemContractHelper.sol";
     7: import "./libraries/EfficientCall.sol";

[L1Messenger.sol#L5-L7](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L5-L7)

FILE : 2023-03-zksync/contracts/BytecodeCompressor.sol

    5: import "./interfaces/IBytecodeCompressor.sol";
    6: import "./Constants.sol";
    7: import "./libraries/Utils.sol";
    8: import "./libraries/UnsafeBytesCalldata.sol";

[BytecodeCompressor.sol#L5-L8](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L5-L8)

##

### [N-3] NOT USING THE NAMED RETURN VARIABLES ANYWHERE IN THE FUNCTION IS CONFUSING

Consider changing the variable to be an unnamed one.

FILE : 2023-03-zksync/contracts/L1Messenger.sol

     22: function sendToL1(bytes calldata _message) external override returns (bytes32 hash) {

[L1Messenger.sol#L22](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L22)

FILE: 2023-03-zksync/contracts/MsgValueSimulator.sol

    22: function _getAbiParams() internal view returns (uint256 value, bool isSystemCall, address to) {

[MsgValueSimulator.sol#L22](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/MsgValueSimulator.sol#L22)

##

### [NC-4] Pragma float

All the contracts in scope are floating the pragma version.

Recommendation
Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.

Note that pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or a package

##

### [NC-5] CONSTANTS SHOULD BE DEFINED RATHER THAN USING MAGIC NUMBERS

  It is bad practice to use numbers directly in code without explanation

FILE : 2023-03-zksync/contracts/L1Messenger.sol

   37: pubdataLen = ((_message.length + 31) / 32) * 32 + 64;

[L1Messenger.sol#L37](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L37)



NC-1	Missing checks for address(0) when assigning values to address state variables	1
NC-2	require() / revert() statements should have descriptive reason strings	3
NC-3	TODO Left in the code	1
NC-4	Event is missing indexed fields	11
NC-5	Functions not used internally could be marked external	7

L-1	abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()	2
L-2	Do not use deprecated library functions	3
L-3	Empty Function Body - Consider commenting why	2
L-4	Initializers could be front-run	2
L-5	Unspecific compiler version pragma	1

    

