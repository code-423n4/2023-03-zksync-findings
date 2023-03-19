## [L-01] fallback function DO NOT have `payable` keyword.

- [contracts/DefaultAccount.sol#L223](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L223)
```
file: contracts/DefaultAccount.sol

223: fallback() external {
224:         // fallback of default account shouldn't be called by bootloader under no circumstances
225:          assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);
226: 
227:         // If the contract is called directly, behave like an EOA
228:     }
```
#### Description
The fallback function in the DefaultAccount.sol contract do not have the payable keyword. This will not allow the fallback function to be able to receive ether.

#### Recommended Mitigation Steps
Consider adding payable keyword to the fallback function.

## [L-02] Consider Adding Checks for Signature Malleability

```
File: contracts/DefaultAccount.sol


189:       address recoveredAddress = ecrecover(_hash, v, r, s);
```
#### Description
The Solidity `ecrevoer()` is directly used to verify signatures meanwhile the Solidity ecrecover() can be open to signature malleability.

#### Recommended Mitigation steps
Consider using the `ecrecoever()` function from the Openzeppelin's ECDSA contract for signature verification rather than calling `ecrecover()` directly.


## [L-03] USE NAMED IMPORT 
Files: All files

Consider replacing global imports with named imports. 

Consider replacing 
```
import "./interfaces/IContractDeployer.sol";
```
with
```
import {IContractDeployer} from "./interfaces/IContractDeployer.sol";
```
## [L-04] USE LATEST STABLE SOLIDITY COMPILER PRAGMA VERSION
The pragma version used in this project is 0.8.0 and the latest pragma Solidity version is 0.8.19 at the time of writting this.

See: https://swcregistry.io/docs/SWC-102

Latest versions of compilers have bug fixes and security improvements.

Consider using `pragma solidity ^0.8.19;`. 

## [L-05] AVOID FLOATING PRAGMA
see: https://swcregistry.io/docs/SWC-103
The project uses floating pragma version: `pragma solidity ^0.8.0;`

Consider using locked pragma version: `pragma solidity 0.8.19;`

## [L-06] DIVISION BEFORE MULTIPLICATION INCURS PRECISION LOSS
File: 
- [contracts/L1Messenger.sol#L37](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L37)

The `pubdataLen` variable of the `sendToL1` function of L1Messenger.sol contract is assigned a value that is the result of the operation below. Implementing division before multiplication incurs precision loss.
```
pubdataLen = ((_message.length + 31) / 32) * 32 + 64;
```

#### Recommended Mitigation Steps
Always consider doing the division operation last when you have to do multiplication and division in order to preserve precision.

## [NC-1] INCOMPLETE NATSPEC COMMENTS

Some functions in the project have incomplete Natspec comment. For example some functions do not have their arguments and the return variables included in their Natspec comments

- [contracts/KnownCodesStorage.sol#L115](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L115)
- [contracts/KnownCodesStorage.sol#L123](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L123)
```
File: contracts/KnownCodesStorage.sol#L123
/// @notice Validates the format of bytecodehash
    /// @dev zk-circuit accepts & handles only valid format of bytecode hash, other input has undefined behavior
    /// That's why we need to validate it
    function _validateBytecode(bytes32 _bytecodeHash) internal pure {
        uint8 version = uint8(_bytecodeHash[0]);
        require(version == 1 && _bytecodeHash[1] == bytes1(0), "Incorrectly formatted bytecodeHash");

        require(Utils.bytecodeLenInWords(_bytecodeHash) % 2 == 1, "Code length in words must be odd");
    }
```
```
file: contracts/KnownCodesStorage.sol#L115
/// @notice Returns the marker stored for a bytecode hash. 1 means that the bytecode hash is known
    /// and can be used for deploying contracts. 0 otherwise.
    function getMarker(bytes32 _hash) public view override returns (uint256 marker) {
        assembly {
            marker := sload(_hash)
        }
    }
```

Consider Adding the function arguments in the Natspec comments.