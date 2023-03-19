# 1. Use of deprecated storage variable in `L2EthToken` contract
### Link : 
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L2EthToken.sol#L26

The `L2EthToken` contract has a deprecated storage variable, `__DEPRECATED_l2Bridge`, which is no longer in use but still exists in the contract. This deprecated variable can pose a security risk if an attacker finds a way to manipulate it.

### Recommendation: 

The `__DEPRECATED_l2Bridge` variable should be removed from the contract as it is no longer used.

# 2. Potential reentrancy vulnerability in `L2EthToken` contract

### Link: 
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L2EthToken.sol#L80

### Summary: 
The `L2EthToken` contract contains a potential reentrancy vulnerability in the `withdraw()` function that allows an attacker to re-enter the function before the transaction completes, potentially causing unexpected behavior and loss of funds.

### Impact: 
An attacker could exploit this vulnerability to perform multiple withdrawal transactions, potentially draining the contract's balance or causing other unexpected behavior. This can result in loss of funds for the contract and its users.

### Recommendation: 
To mitigate this vulnerability, the contract should use the `check-effects-interaction` pattern to ensure that all state changes are made before any external calls are made. This can be achieved by moving the call to `L1_MESSENGER_CONTRACT.sendToL1()` to the end of the function, after all state changes have been made. Additionally use `Reentrancy Guard` for this function.

### Example:

```
function withdraw(address _l1Receiver) external payable override {
    uint256 amount = msg.value;
 
    // Silent burning of the ether
    unchecked {
        balance[address(this)] -= amount;
        totalSupply -= amount;
    }
 
    emit Withdrawal(msg.sender, _l1Receiver, amount);
 
    // Move external call to the end of the function
    bytes memory message = _getL1WithdrawMessage(_l1Receiver, amount);
    L1_MESSENGER_CONTRACT.sendToL1(message);
}
```

# 3. Missing Input Validation in `SystemContext` contract

### Links : 
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L60
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L66

### Recommendation : 

To fix this issue, the contract should implement input validation when setting the transaction `origin` and `gas price`. For example, the `setTxOrigin` and `setGasPrice` functions should check that the input is not the null address and is within a reasonable range.

# 4. Gas limit restriction in `precompileCall`

### Link : 
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L147

### Summary: 
The `precompileCall` function in the `SystemContractHelper` library has a vulnerability that can cause the contract to revert. The `precompileCall` function does not enforce a gas limit when invoking external precompiled contracts.

### Impact: 
A malicious actor can exploit this vulnerability by invoking an external precompiled contract with a high gas limit, which can lead to out-of-gas errors, causing the contract to revert. The attack can cause the contract to lose funds or cause unexpected state changes, leading to a denial-of-service attack.

### Recommendation: 
The `precompileCall` function should enforce a gas limit when invoking external precompiled contracts to prevent out-of-gas errors. The recommended gas limit for external precompiled contracts is 2,000,000 gas, which is the maximum amount of gas that can be consumed by a single transaction. This can be done by adding the following code at the beginning of the `precompileCall` function:

```
function precompileCall(uint256 _rawParams, uint32 _gasToBurn) internal view returns (bool success) {
    address callAddr = PRECOMPILE_CALL_ADDRESS;

    require(gasleft() >= _gasToBurn, "Not enough gas to call precompiled contract");
    gasleft() -= _gasToBurn;
    
    uint256 cleanupMask = UINT32_MASK;
    assembly {
        // Clearing input params as they are not cleaned by Solidity by default
        _gasToBurn := and(_gasToBurn, cleanupMask)
        success := staticcall(_rawParams, callAddr, _gasToBurn, 0xFFFF, 0, 0)
    }
}
```
# 5. Lack of Input Validation in `SystemContractHelper` Library
### Links : 
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L48
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L94
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L106
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L147

### Summary: 
The `SystemContractHelper` library of a smart contract lacks input validation for some of its functions, which can lead to unintended behavior and security vulnerabilities.

### Impact: 
The lack of input validation in the `SystemContractHelper` library functions can lead to multiple vulnerabilities. The `toL1` function doesn't validate the `_isService` parameter, which can result in an incorrect value being set for it. Similarly, the `ptrAddIntoActive` and `ptrShrinkIntoActive` functions don't validate the `_value` and `_shrink` parameters, respectively, which can cause unexpected behavior if they exceed the allowed range. Moreover, the `precompileCall` function doesn't validate the `_rawParams` parameter, which can lead to out-of-bounds memory access or other vulnerabilities if an attacker passes invalid values to it.

### Recommendation: 
It is recommended to add input validation checks to the `SystemContractHelper` library functions to prevent unexpected behavior and vulnerabilities. For instance, the `toL1` function should validate the `_isService` parameter to ensure that it's a boolean value. Similarly, the `ptrAddIntoActive` and `ptrShrinkIntoActive` functions should validate the `_value` and `_shrink` parameters, respectively, to ensure that they're within the allowed range. The `precompileCall` function should also validate the `_rawParams` parameter to prevent out-of-bounds memory access or other vulnerabilities.
# 6. Lack of input validation in `SystemContractHelper` library functions
### Link : 
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L85
### Summary: 
The `SystemContractHelper` library functions lack input validation, which may lead to unexpected behavior or security vulnerabilities. Specifically, the library functions do not check if the input parameters are within expected ranges. This could result in errors or even exploitable vulnerabilities if the input values are manipulated by an attacker.
### Impact: 
An attacker may exploit this vulnerability to perform a Denial of Service (DoS) attack, execute unintended or unauthorized transactions, or even gain control over the contract.
### Recommendation: 
It is recommended to add proper input validation checks to all library functions in the `SystemContractHelper` library. For example, the library functions should check the range and type of input parameters, as well as ensure that any values loaded from memory are valid.
### Example: 
One possible example is to add the following input validation to the `ptrPackIntoActivePtr()` function:
```
require(_farCallAbi > 0, "Invalid far call ABI");
```
# 7. Mismatch between unused parameters
Unused parameters in `DefaultAccount.sol` can cause problems if they are used.
https://github.com/code-423n4/2023-03-zksync/blob/01379615bc20c2926d81c0a182f1abb6e922b93a/contracts/DefaultAccount.sol#L217
https://github.com/code-423n4/2023-03-zksync/blob/01379615bc20c2926d81c0a182f1abb6e922b93a/contracts/interfaces/IAccount.sol#L44
### Recommendation : 
Replace `_suggestedSignedHash` to `_possibleSignedHash`.

# 8. Outdated compiler
The pragma version used is:
```
pragma solidity ^0.8.0
```
Please update also all contracts with latest Solidity version.


