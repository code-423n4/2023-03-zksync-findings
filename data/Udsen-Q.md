## 1. CONDUCT `address(0)` CHECKS FOR THE PROTOCOL CRITICAL ADDRESSES

        (uint256 value, bool isSystemCall, address to) = _getAbiParams();
		
It is recommended to perform `address(0)` check on the `to` address received via the return value from `_getAbiParams()` function call.
This will make sure `ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo` function will not be called with zero address.

        (uint256 value, bool isSystemCall, address to) = _getAbiParams();
		require(to != address(0), "address can not be zero");

https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L33-L38

## 2. EXPRESSIONS SHOULD USE IMMUTABLE INSTEAD OF CONSTANTS

There is a difference in the way constants and immutable are used. Each should be used in their appropriate contexts. Constants should be used where literal values are written into the code and immutable to used for expressions or values calculated in the constructor.

    uint256 constant BLOCK_INFO_BLOCK_NUMBER_PART = 2 ** 128;

https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L48

The above should be declared either as a `immutable` variable and initialized inside the constructor or the final calculated value should be assigned to the constant as literal.

There are 2 more instances of this issue:

https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L27
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L30
 
## 3. INCONSISTENCY IN THE CONDITIONANL CHECKS BETWEEN `block.number` and `_block` PARAMETER PASSED IN

    if (block.number < _block || block.number - _block > 256) {
	
In the `getBlockHashEVM` function of the `SystemContext.sol` contract the `block.number` is checked against the `_block` parameter to set the `_hash` to `bytes32(0)`, in the case hashes that are older than 256 blocks ago are queried.
But as it is evident from the above `if` statement, when the `block.number < _block` is true the `block.number - _block < 256` will revert as the condition arithmetic underflows. 

Even though the condition expects to set the `hash = bytes32(0)` when the `block.number < _block` condition is true, it will never happen as the `if` condtion will revert.

Above condition should be broken into `if, else if` statement to implement the expected logic properly as follows:

        if (block.number < _block ) { 
            hash = bytes32(0);
        } else if (block.number - _block > 256) {
            hash = bytes32(0);
        } 
		
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L74-L75

## 4. require() / revert() STATEMENTS SHOULD HAVE DESCRIPTIVE REASON STRINGS

        require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), ""); 
	
`require` statement should have a descriptive message for the revert scenario.

https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L135

## 5. USE `openzeppelin` `safeCast.sol` LIBRARY TO PERFORM DOWNCASTING SAFELY

        address to = address(uint160(_transaction.to));
		
In the above code the `_transaction.to` is downcast to `uint160` from `uint256`. Hence it is recommended to use `safeCast.sol` of `openzeppelin` to perform this downcast.

https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L141

## 6. `recoveredAddress != address(0)` REDUNDANT CHECK IS PERFORMED.

In the `_isValidSignature` function of the `DefaultAccount.sol` contract, a boolean value is returned after performing the following check.

        return recoveredAddress == address(this) && recoveredAddress != address(0);
		
According to the above check if the `recoveredAddress == address(this)` is true then `recoveredAddress != address(0)` will be also true and the return boolean will be true.
If the `recoveredAddress == address(this)` is false then the return value will be false since it uses `&&` operation.

Hence the `recoveredAddress != address(0)` is a redundant check here and can be omitted.

The updated return code line will look as follows:

        return recoveredAddress == address(this);
		
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L191

## 7. USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’

Can import the required specific contracts, functions or variables by using the named imports explicitly (This will reduce the deployment bytecode as well). Plain imports will import the entire context of the imported contract which could lead into variable namespace conflicts etc ...

	import "./interfaces/IImmutableSimulator.sol";	

Currently the `IImmutableSimulator` is imported as follows:
	import "./interfaces/IImmutableSimulator.sol";	

But it can be imported explicitly by the name as follows:
	import {IImmutableSimulator} from "./interfaces/IImmutableSimulator.sol";
	
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ImmutableSimulator.sol#L5

Above issue is prevalent in all the smart contracts of the project.

## 8. NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

	pragma solidity ^0.8.0;

https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ImmutableSimulator.sol#L3

Above issue is prevalent in all the smart contracts of the project.

## 9. PROPER COMMENTING WILL INCREASE THE READABILITY AND AUDITABILITY OF THE CODE.

    /// @dev The contract accepts value, the callee and whether the call **should a system one** via its ABI params. 
Above code should be changed as 

    /// @dev The contract accepts value, the callee and whether the call **should be a system one,** via its ABI params. 
	
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L18

    // The if above should never be true, since **noone** should be able to have	
Above code should be changed as 

    // The **above 'if'** should never be true, since **none** should be able to have	
	
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L49
	
    /// @dev For the support of coinbase, we will the bootloader formal address for now 
Above code should be changed as

    /// @dev For the support of coinbase, we will **use** the bootloader formal address for now 
	
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L36

    /// @notice Set the current tx origin. 
Above code should be changed as

    /// @notice Set the current tx gasPrice. 
	
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L64

    /// @notice Returns the raw version of the current minimal nonce 
Above code should be changed as

    /// @notice Returns the raw version of the current minimal nonce **and current deployment nonce**

https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L52	

    // The fact there **is are** enough balance for the account 
Above code should be changed as

    // The fact there **is** enough balance for the account 
	
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L99
