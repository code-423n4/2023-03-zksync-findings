 ## Summary

### Low risk issues
| |Issue|Instances| |
|-|:-|:-:|:-:|
| [L&#x2011;01] | Token transfer to address(0) should be avoided, Zero value checks are missing | 3 |
| [L&#x2011;02] | Token minting to address(0) should be avoided | 1 |

### Non-critical issues
| |Issue|Instances| |
|-|:-|:-:|:-:|
| [N&#x2011;01] | Do not use floating solidity compiler version. Lock the solidity compiler version | 13 |
| [N&#x2011;02] | Missing Zero value checks when assigning values to uint state variables | 1 |
| [N&#x2011;03] | Use a more recent version of solidity | 13 |

### Low risk issues
### [L&#x2011;01]   Token transfer to address(0) should be avoided
Address(0) checks are missing. amount should be greater than zero to prevent wastage of gas. 

```solidity
File: contracts/L2EthToken.sol

40    function transferFromTo(address _from, address _to, uint256 _amount) external override {
41        require(
42            msg.sender == MSG_VALUE_SYSTEM_CONTRACT ||
43                msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||
44                msg.sender == BOOTLOADER_FORMAL_ADDRESS,
45            "Only system contracts with special access can call this method"
46        );
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L40-L46)

### Recommended Mitigation steps

```solidity
File: contracts/L2EthToken.sol

   function transferFromTo(address _from, address _to, uint256 _amount) external override {
+        require (_from != address(0), "address can not be zero address");
+        require (_to != address(0), "address can not be zero address");
+        require (_amount != 0. "amount must be greater than zero");
         require(
            msg.sender == MSG_VALUE_SYSTEM_CONTRACT ||
                msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||
                msg.sender == BOOTLOADER_FORMAL_ADDRESS,
            "Only system contracts with special access can call this method"
        );
```

### [L&#x2011;02]   Token minting to address(0) should be avoided
Token minting to address(0) should be avoided, If mistakenly minted to address(0), there will be no way to restore from address(0). It is recommended to have address(0) check in mint function. 

```solidity
File: contracts/L2EthToken.sol

72    function mint(address _account, uint256 _amount) external override onlyBootloader {
73        totalSupply += _amount;
74        balance[_account] += _amount;
75        emit Mint(_account, _amount);
76    }
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L72-L76)

### Recommended Mitigation steps

```solidity
File: contracts/L2EthToken.sol

    function mint(address _account, uint256 _amount) external override onlyBootloader {
+       require(_account != address(0), "account can not be zero address");
        totalSupply += _amount;
        balance[_account] += _amount;
        emit Mint(_account, _amount);
    }
```

### Non-critical issues
### [N&#x2011;01]   Do not use floating solidity compiler version. Lock the solidity compiler version
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
There are 13 instances of this issue.

```solidity
File: contracts/EmptyContract.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/EmptyContract.sol#L3)

```solidity
File: contracts/ImmutableSimulator.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ImmutableSimulator.sol#L3)

```solidity
File: contracts/L1Messenger.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L3)

```solidity
File: contracts/MsgValueSimulator.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/MsgValueSimulator.sol#L3)

```solidity
File: contracts/BytecodeCompressor.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L3)

```solidity
File: contracts/AccountCodeStorage.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L3)

```solidity
File: contracts/L2EthToken.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L3)


```solidity
File: contracts/SystemContext.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L3)

```solidity
File: contracts/KnownCodesStorage.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L3)

```solidity
File: contracts/NonceHolder.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L3)


```solidity
File: contracts/DefaultAccount.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L3)


```solidity
File: contracts/ContractDeployer.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L3)

```solidity
File: contracts/BootloaderUtilities.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BootloaderUtilities.sol#L3)

### [N&#x2011;02]   Missing Zero value checks when assigning values to uint state variables
While assigning the value, The uint value should not be Zero. 

```solidity
File: contracts/SystemContext.sol

66    function setGasPrice(uint256 _gasPrice) external onlyBootloader {
67        gasPrice = _gasPrice;
68    }
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L66-L68)

### Recommended Mitigation steps

```solidity
File: contracts/SystemContext.sol

    function setGasPrice(uint256 _gasPrice) external onlyBootloader {
+       require(_gasPrice != 0, "gas price can not be zero");
        gasPrice = _gasPrice;
    }
```

### [N&#x2011;03]   Use a more recent version of solidity
Recommend to use latest solidity compiler version(0.8.18 or 0.8.19). This will safeguard from underflow and overflow conditions. This modification will make the contracts more gas optimized. safeMath library wont be explicitely required as ^0.8.0 version has already taken  the underflow and overflow conditions by default.
1- Use a solidity version of at least 0.8 to get default underflow/overflow checks.
2- Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining. 
3- Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads.
4- Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than 
   revert()/require() strings.
5- Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.
There are 13 instances of this issue.

```solidity
File: contracts/EmptyContract.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/EmptyContract.sol#L3)

```solidity
File: contracts/ImmutableSimulator.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ImmutableSimulator.sol#L3)

```solidity
File: contracts/L1Messenger.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L3)

```solidity
File: contracts/MsgValueSimulator.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/MsgValueSimulator.sol#L3)

```solidity
File: contracts/BytecodeCompressor.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L3)

```solidity
File: contracts/AccountCodeStorage.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L3)

```solidity
File: contracts/L2EthToken.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L3)


```solidity
File: contracts/SystemContext.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L3)

```solidity
File: contracts/KnownCodesStorage.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L3)

```solidity
File: contracts/NonceHolder.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L3)


```solidity
File: contracts/DefaultAccount.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L3)


```solidity
File: contracts/ContractDeployer.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L3)

```solidity
File: contracts/BootloaderUtilities.sol

3      pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BootloaderUtilities.sol#L3)