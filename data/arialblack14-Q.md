# QA REPORT

---

### Summary of low risk issues


| Number     | Issue details                                                                                                               | Instances |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------- | :---------: |
| [L-1](#L1) | `require()` should be used instead of `assert()`                                                                            |     2     |
| [L-2](#L2) | Avoid variable names that can shade                                                                                         |     1     |
| [L-3](#L3) | Lock pragmas to specific compiler version.                                                                                  |    39    |

*Total: 3 issues.*

### Summary of non-critical issues


| Number       | Issue details                                                                                         | Instances |
| -------------- | ------------------------------------------------------------------------------------------------------- | :---------: |
| [NC-1](#NC1) | Lines are too long.                                                                                   |     3     |
| [NC-2](#NC2) | Value of `CURRENT_MAX_PRECOMPILE_ADDRESS` should be updated automatically with every precompile added. |     -     |
| [NC-3](#NC3) | `2**<n> - 1` should be re-written as `type(uint<n>).max`                                              |     1     |
| [NC-4](#NC4) | Use of`bytes.concat()` instead of `abi.encodePacked()`.                                               |     3     |
| [NC-5](#NC5) | Return values of`approve()` not checked                                                               |     3     |
| [NC-6](#NC6) | ΝonceOrdering is not implemented.                                                                    |     -     |

*Total: 6 issues.*

---

### Low Risk Issues

### <a id=L1>[L-1]</a> `require()` should be used instead of `assert()`

##### Description

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction's available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix

##### Recommendation

You should change from `assert()` to `require()`

##### *Instances (2):*

File: [2023-03-zksync/contracts/DefaultAccount.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L225 )

```solidity
225: assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);
```

File: [2023-03-zksync/contracts/libraries/RLPEncoder.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/RLPEncoder.sol#L45 )

```solidity
45: assert(_len != 1);
```

### <a id=L2>[L-2]</a> Avoid variable names that can shade

##### Description

With global variable names in the form of `call{value: value }` , argument name similarities can shade and negatively affect code readability.

##### Recommendation

Avoid such variable names.

##### *Instances (1):*

File: [2023-03-zksync/contracts/openzeppelin/utils/Address.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/utils/Address.sol#L159 )

```solidity
159: (bool success, bytes memory returndata) = target.call{value: value}(
```

### <a id=L3>[L-3]</a> Lock pragmas to specific compiler version.

##### Description

Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally.

##### Recommendation

Ethereum Smart Contract Best Practices - Lock pragmas to specific compiler version. [solidity-specific/locking-pragmas](https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/)

##### *Instances (39):*

File: [2023-03-zksync/contracts/AccountCodeStorage.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/AccountCodeStorage.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/BootloaderUtilities.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BootloaderUtilities.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/BytecodeCompressor.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BytecodeCompressor.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/Constants.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/Constants.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/ContractDeployer.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/DefaultAccount.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/EmptyContract.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/EmptyContract.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/ImmutableSimulator.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ImmutableSimulator.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/KnownCodesStorage.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/KnownCodesStorage.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/L1Messenger.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L1Messenger.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/L2EthToken.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/MsgValueSimulator.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/MsgValueSimulator.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/NonceHolder.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/SystemContext.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/interfaces/IAccount.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IAccount.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/interfaces/IAccountCodeStorage.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IAccountCodeStorage.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/interfaces/IBootloaderUtilities.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IBootloaderUtilities.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/interfaces/IBytecodeCompressor.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IBytecodeCompressor.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/interfaces/IContractDeployer.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IContractDeployer.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/interfaces/IEthToken.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IEthToken.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/interfaces/IImmutableSimulator.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IImmutableSimulator.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/interfaces/IKnownCodesStorage.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IKnownCodesStorage.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/interfaces/IL1Messenger.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IL1Messenger.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/interfaces/IL2StandardToken.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IL2StandardToken.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/interfaces/IMailbox.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IMailbox.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/interfaces/INonceHolder.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/INonceHolder.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/interfaces/IPaymaster.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IPaymaster.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/interfaces/IPaymasterFlow.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IPaymasterFlow.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/interfaces/ISystemContext.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/ISystemContext.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/libraries/EfficientCall.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/libraries/RLPEncoder.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/RLPEncoder.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/libraries/SystemContractHelper.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol#L3 )

```solidity
3: pragma solidity ^0.8;
```

File: [2023-03-zksync/contracts/libraries/SystemContractsCaller.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractsCaller.sol#L3 )

```solidity
3: pragma solidity ^0.8;
```

File: [2023-03-zksync/contracts/libraries/TransactionHelper.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/libraries/UnsafeBytesCalldata.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/UnsafeBytesCalldata.sol#L3 )

```solidity
3: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/openzeppelin/token/ERC20/IERC20.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/token/ERC20/IERC20.sol#L4 )

```solidity
4: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol#L4 )

```solidity
4: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L4 )

```solidity
4: pragma solidity ^0.8.0;
```

File: [2023-03-zksync/contracts/openzeppelin/utils/Address.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/utils/Address.sol#L4 )

```solidity
4: pragma solidity ^0.8.1;
```

### Non-critical Issues

### <a id=NC1>[NC-1]</a> Lines are too long.

##### Description

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

##### Recommendation

Reduce number of characters per line to improve readability.

##### *Instances (3):*

File: [2023-03-zksync/contracts/ContractDeployer.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L7 )

```solidity
7: import {CREATE2_PREFIX, CREATE_PREFIX, NONCE_HOLDER_SYSTEM_CONTRACT, ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT, FORCE_DEPLOYER, MAX_SYSTEM_CONTRACT_ADDRESS, KNOWN_CODE_STORAGE_CONTRACT, ETH_TOKEN_SYSTEM_CONTRACT, IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT} from "./Constants.sol";
```

File: [2023-03-zksync/contracts/libraries/EfficientCall.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol#L24 )

```solidity
24: * 3. `ptr.pack` - Do the concatenation between the lowest 128 bits of the pointer itself and the highest 128 bits of `_value`. It is typically used to prepare the ABI for external calls.
```

File: [2023-03-zksync/contracts/libraries/TransactionHelper.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L85 )

```solidity
85: "Transaction(uint256 txType,uint256 from,uint256 to,uint256 gasLimit,uint256 gasPerPubdataByteLimit,uint256 maxFeePerGas,uint256 maxPriorityFeePerGas,uint256 paymaster,uint256 nonce,uint256 value,bytes data,bytes32[] factoryDeps,bytes paymasterInput)"
```

### <a id=NC2>[NC-2]</a> Value of `CURRENT_MAX_PRECOMPILE_ADDRESS` should be updated automatically with every precompile added.

##### Description

Value of `CURRENT_MAX_PRECOMPILE_ADDRESS` should be updated automatically with every precompile added. Currently only two precompiles have been added so the value is set to `2`. The value should be updated without human intervention which could cause unexpected behaviour.

##### Recommendation

Consider updating the value without relying on remembering to do so.

##### *Instances (1):*

File: [2023-03-zksync/contracts/Constants.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/Constants.sol#L31 )

```solidity
31: /// Important! So the constant should be updated if more precompiles are deployed.
```

### <a id=NC3>[NC-3]</a> `2**<n> - 1` should be re-written as `type(uint<n>).max`

##### Description

Earlier versions of solidity can use `uint<n>(-1)` instead. Expressions not including the `- 1` can often be re-written to accomodate the change (e.g. by using a `>` rather than a `>=`, which will also save some gas)

##### Recommendation

Use `type(uint<n>).max` instead.

##### *Instances (1):*

File: [2023-03-zksync/contracts/Constants.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/Constants.sol#L74 )

```solidity
74: uint256 constant MAX_MSG_VALUE = 2 ** 128 - 1;
```

### <a id=NC4>[NC-4]</a> Use of `bytes.concat()` instead of `abi.encodePacked()`.

##### Description

Rather than using `abi.encodePacked` for appending bytes, since version `0.8.4`, `bytes.concat()` is enabled.

##### Recommendation

Since version `0.8.4` for appending bytes, `bytes.concat()` can be used instead of `abi.encodePacked()`.

##### *Instances (3):*

File: [2023-03-zksync/contracts/L2EthToken.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L98 )

```solidity
98: return abi.encodePacked(IMailbox.finalizeEthWithdrawal.selector, _to, _amount);
```

File: [2023-03-zksync/contracts/libraries/TransactionHelper.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L132 )

```solidity
132: keccak256(abi.encodePacked(_transaction.factoryDeps)),
141: return keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));
```

### <a id=NC5>[NC-5]</a> Return values of `approve()` not checked

##### Description

Not all `IERC20` implementations `revert()` when there's a failure in `transfer()/transferFrom()`. The function signature has a `boolean` return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment

##### Recommendation

You should check whether the return value is true or false in terms of security.

##### *Instances (3):*

File: [2023-03-zksync/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L66 )

```solidity
66: abi.encodeWithSelector(token.approve.selector, spender, value)
79: token.approve.selector,
101: token.approve.selector,
```

### <a id=NC6>[N-6] ΝonceOrdering is not implemented.

##### Description

Account Abstraction only changes from sequential to arbitrary.
According to the documentation it should be possible to change from one to the other and let user select nonce for arbitrary and increment for sequential.

```solidity
function updateNonceOrdering(AccountNonceOrdering _nonceOrdering) external onlySystemCall {
	AccountInfo memory currentInfo = _accountInfo[msg.sender];

	require(
		_nonceOrdering == AccountNonceOrdering.Arbitrary &&
			currentInfo.nonceOrdering == AccountNonceOrdering.Sequential,
		"It is only possible to change from sequential to arbitrary ordering"
	);

	currentInfo.nonceOrdering = _nonceOrdering;
	_storeAccountInfo(msg.sender, currentInfo);

	emit AccountNonceOrderingUpdated(msg.sender, _nonceOrdering);
}
```

But in this [comment](https://github.com/code-423n4/2023-03-zksync/blob/01379615bc20c2926d81c0a182f1abb6e922b93a/contracts/ContractDeployer.sol#L66) it is stated that only sequential to arbitrary ordering is allowed.

##### Recommendation

Consider updating the documentation or implementing change from one to the other.
