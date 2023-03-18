**Non-Critical Issues**

### [N-01] `legacy transactions` is not correct, it should be `EIP2930` `transactions`

*There is 1 instance of this issue:*

```diff
File: /contracts/libraries/TransactionHelper.sol
-	19: /// @dev The type id of legacy transactions.
+ 19: /// @dev The type id of EIP2930 transactions.
```

- [TransactionHelper.sol#L19](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L19)

### [N-02] `require()` should be used instead of `assert()`

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction's available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix

You should change from `assert()` to `require()`

*There is 1 instance of this issue:*

```solidity
File: /contracts/DefaultAccount.sol
	225: assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);
```

### **[N-03] Require messages are too short and unclear**

*There is 1 instance of this issue:*

```solidity
File: /contracts/NonceHolder.sol
	135: require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "");
```

- [NonceHolder.sol#L135](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L135)

### [N-04] Duplicated `require()` checks should be refactored to a modifier :

`require()` checks statement used multiple times inside a contract should be refactored to a modifier for better readability.

There are 1 instances of this issue in `PairsContract.sol`:

```solidity
91: require(vInt == 27 || vInt == 28, "Invalid v value");
190: require(vInt == 27 || vInt == 28, "Invalid v value");
285: require(vInt == 27 || vInt == 28, "Invalid v value");
```

- [BootloaderUtilities.sol#L91](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BootloaderUtilities.sol#L91)

### **[N-05] Adding a `return` statement when the function defines a named return variable, is redundant**

*There is 1 instance of this issue:*

While not consuming more gas with the Optimizer enabled: using both named returns and a return statement isn’t necessary. Removing one of those can improve code clarity.

If returns revert, you should add comment to makes failed points clear

```solidity
File: /contracts/NonceHolder.sol
	124: function getDeploymentNonce(address _address) external view returns (uint256 deploymentNonce) {
...
	128:    return deploymentNonce;

```

- [NonceHolder.sol#L128](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L128)

### [N-06] Typo `^0.8;`
Write ^0.8.0;
*There is 2 instance of this issue:*

```jsx
File: /contracts/libraries/SystemContractHelper.sol
	3: pragma solidity ^0.8;

File: /contracts/libraries/SystemContractsCaller.sol
	3: pragma solidity ^0.8;
```

### **[N-07] Include `return parameters` in *NatSpec comments***

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

[https://docs.soliditylang.org/en/v0.8.15/natspec-format.html](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)

If Return parameters are declared, you must prefix them with "/// [@return](https://github.com/return)".

- [ContractDeployer.sol#L110](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L110)
- [DefaultAccount.sol#L66](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L66)
- [DefaultAccount.sol#L77](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L77)
- [NonceHolder.sol#L146](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L146)
- [SystemContractHelper.sol#L63](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L63)
- [SystemContractHelper.sol#L164](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L164)
- [BytecodeCompressor.sol#L35](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BytecodeCompressor.sol#L35)
- [BytecodeCompressor.sol#L75](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BytecodeCompressor.sol#L75)
- [MsgValueSimulator.sol#L22](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L22)
- [MsgValueSimulator.sol#L32](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L32)
- [L1Messenger.sol#L22](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L1Messenger.sol#L22)

### [N-08] Use underscores for number literals

**Description:** There is occasions where certain number have been hardcoded, either in variable or in the code itself. Large numbers can become hard to read.

Recommendation: Consider using underscores for number literals to improve its readability.

*There is 1 instance of this issue:*

```solidity

File: /contracts/SystemContext.sol
	40: uint256 public difficulty = 2500000000000000;
```

- [SystemContext.sol#L40](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L40)