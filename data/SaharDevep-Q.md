# Audit Report

## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Token approval issues | 1 | 
| [L&#x2011;02] | Unused `receive()` function will lock ether in the contract | 2 | 



### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Expressions for constant values such as a call to KECCAK256() should use immutable rather than constant | 1 | 



### [L&#x2011;01]  Token approval issues
safeApprove() has been deprecated in favour of safeIncreaseAllowance() and safeDecreaseAllowance()
Using approve() might fail because some tokens (eg. USDT) don’t work when changing the allowance from an existing non-zero allowance value.
Update instances of approve() and safeApprove() to safeIncreaseAllowance().

*There is 2 instances of this issue:*

```solidity
File: /contracts/libraries/TransactionHelper.sol

381:          IERC20(token).safeApprove(paymaster, 0);
382:          IERC20(token).safeApprove(paymaster, minAllowance);

```
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L381

### [L&#x2011;02] Unused receive() function will lock ether in the contract

*There is 2 instances of this issue:*

```solidity
File: /contracts/DefaultAccount.sol

230:          receive() external payable {

```
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L230

```solidity
File: /contracts/EmptyContract.sol

13:          receive() external payable {}

```

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/EmptyContract.sol#L13

### [N&#x2011;01] Expressions for constant values such as a call to KECCAK256() should use immutable rather than constant

*There is 2 instances of this issue:*

```solidity
File: /contracts/libraries/TransactionHelper.sol

81:          bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");
83:          bytes32 constant EIP712_TRANSACTION_TYPE_HASH =

```
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L81


