## [G-01] Mark storage variables as `constant` if they never change

*There are 5 instances of this issue*
https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L21
https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L33
https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L37
https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L40
https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L26

---

## [G-02] Optimal Comparison

When comparing integers, it is cheaper to use strict > & < operators over >= & <= operators, even if you must increment or decrement one of the operands. Note: before using this technique, it's important to consider whether incrementing/decrementing one of the operators could result in an over/underflow. This optimization is applicable when the optimizer is turned off.

*There are 8 instances of this issue*
https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L96
https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L103
https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L187
https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L49
https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L71
https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L167
https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L262
https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol#L43

---

## [G-03] += costs more gas than = + for state variables

Use =+/=- to save gas

*There are 9 instances of this issue*
```
L2EthToken.sol:54:            balance[_to] += _amount;
L2EthToken.sol:73:        totalSupply += _amount;
L2EthToken.sol:74:        balance[_account] += _amount;
BootloaderUtilities.sol:96:                vInt += 8 + block.chainid * 2;
BytecodeCompressor.sol:49:            for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) {
ContractDeployer.sol:240:            sumOfValues += _deployments[i].value;
L2EthToken.sol:85:            balance[address(this)] -= amount;
L2EthToken.sol:86:            totalSupply -= amount;
```

---
