
### [L01] require() should be used instead of assert()


#### Findings:
```
2023-03-zksync-main/contracts/DefaultAccount.sol::225 => assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);
2023-03-zksync-main/contracts/libraries/RLPEncoder.sol::45 => assert(_len != 1);
```


### [L02] `safeApprove()` is deprecated

#### Impact
[Deprecated](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/bfff03c0d2a59bcd8e2ead1da9aed9edf0080d05/contracts/token/ERC20/utils/SafeERC20.sol#L38-L45) in favor of safeIncreaseAllowance() and safeDecreaseAllowance()
#### Findings:
```
2023-03-zksync-main/contracts/libraries/TransactionHelper.sol::381 => IERC20(token).safeApprove(paymaster, 0);
2023-03-zksync-main/contracts/libraries/TransactionHelper.sol::382 => IERC20(token).safeApprove(paymaster, minAllowance);
```


### [L03] Unused `receive()` function will lock Ether in contract

#### Impact
If the intention is for the Ether to be used, the function should call another function, otherwise, it should revert
#### Findings:
```
2023-03-zksync-main/contracts/DefaultAccount.sol::230 => receive() external payable {
2023-03-zksync-main/contracts/EmptyContract.sol::11 => receive() external payable {}    
2023-03-zksync-main/contracts/EmptyContract.sol::13 => receive() external payable {}
```



### Non-Critical Issues



### [N01] Adding a return statement when the function defines a named return variable, is redundant


#### Findings:
```
2023-03-zksync-main/contracts/AccountCodeStorage.sol::96 => return codeHash;
2023-03-zksync-main/contracts/ContractDeployer.sol::171 => return newAddress;
2023-03-zksync-main/contracts/ContractDeployer.sol::192 => return newAddress;
2023-03-zksync-main/contracts/NonceHolder.sol::49 => return minNonce;
2023-03-zksync-main/contracts/openzeppelin/utils/Address.sol::268 => return returndata;
2023-03-zksync-main/contracts/openzeppelin/utils/Address.sol::286 => return returndata;
```



### [N02] `require()`/`revert()` statements should have descriptive reason strings


#### Findings:
```
2023-03-zksync-main/contracts/MsgValueSimulator.sol::43 => revert(0, 0)
2023-03-zksync-main/contracts/SystemContext.sol::16 => require(msg.sender == BOOTLOADER_FORMAL_ADDRESS);
2023-03-zksync-main/contracts/libraries/EfficientCall.sol::231 => revert(0, size)
2023-03-zksync-main/contracts/libraries/SystemContractHelper.sol::152 => require(gasleft() >= _gasToBurn);
2023-03-zksync-main/contracts/libraries/SystemContractsCaller.sol::161 => revert(add(returnData, 0x20), size)
2023-03-zksync-main/contracts/openzeppelin/utils/Address.sol::270 => _revert(returndata, errorMessage);
2023-03-zksync-main/contracts/openzeppelin/utils/Address.sol::288 => _revert(returndata, errorMessage);
2023-03-zksync-main/contracts/openzeppelin/utils/Address.sol::292 => function _revert(bytes memory returndata, string memory errorMessage)
2023-03-zksync-main/contracts/openzeppelin/utils/Address.sol::302 => revert(add(32, returndata), returndata_size)
2023-03-zksync-main/contracts/openzeppelin/utils/Address.sol::305 => revert(errorMessage);
```



### [N03] Unused file


#### Findings:
```
2023-03-zksync-main/contracts/AccountCodeStorage.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/BootloaderUtilities.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/Constants.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/ContractDeployer.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/DefaultAccount.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/EmptyContract.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/ImmutableSimulator.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/KnownCodesStorage.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/L1Messenger.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/L2EthToken.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/MsgValueSimulator.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/NonceHolder.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/SystemContext.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/interfaces/IAccount.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/interfaces/IAccountCodeStorage.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/interfaces/IBootloaderUtilities.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/interfaces/IBytecodeCompressor.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/interfaces/IContractDeployer.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/interfaces/IEthToken.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/interfaces/IImmutableSimulator.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/interfaces/IKnownCodesStorage.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/interfaces/IL1Messenger.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/interfaces/IL2StandardToken.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/interfaces/IMailbox.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/interfaces/INonceHolder.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/interfaces/IPaymaster.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/interfaces/IPaymasterFlow.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/interfaces/ISystemContext.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/libraries/RLPEncoder.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/libraries/SystemContractHelper.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/libraries/SystemContractsCaller.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/libraries/TransactionHelper.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/libraries/Utils.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/openzeppelin/token/ERC20/IERC20.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol::1 => // SPDX-License-Identifier: MIT
2023-03-zksync-main/contracts/openzeppelin/utils/Address.sol::1 => // SPDX-License-Identifier: MIT
```





### [N04] NC-library/interface files should use fixed compiler versions, not floating ones


#### Findings:
```
2023-03-zksync-main/contracts/AccountCodeStorage.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/BootloaderUtilities.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/BytecodeCompressor.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/Constants.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/ContractDeployer.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/DefaultAccount.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/EmptyContract.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/ImmutableSimulator.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/KnownCodesStorage.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/L1Messenger.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/L2EthToken.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/MsgValueSimulator.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/NonceHolder.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/SystemContext.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/interfaces/IAccount.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/interfaces/IAccountCodeStorage.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/interfaces/IBootloaderUtilities.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/interfaces/IBytecodeCompressor.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/interfaces/IContractDeployer.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/interfaces/IEthToken.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/interfaces/IImmutableSimulator.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/interfaces/IKnownCodesStorage.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/interfaces/IL1Messenger.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/interfaces/IL2StandardToken.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/interfaces/IMailbox.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/interfaces/INonceHolder.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/interfaces/IPaymaster.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/interfaces/IPaymasterFlow.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/interfaces/ISystemContext.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/libraries/EfficientCall.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/libraries/RLPEncoder.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/libraries/SystemContractHelper.sol::3 => pragma solidity ^0.8;
2023-03-zksync-main/contracts/libraries/SystemContractsCaller.sol::3 => pragma solidity ^0.8;
2023-03-zksync-main/contracts/libraries/TransactionHelper.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/libraries/UnsafeBytesCalldata.sol::3 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/libraries/Utils.sol::2 => pragma solidity >=0.8.0;
2023-03-zksync-main/contracts/openzeppelin/token/ERC20/IERC20.sol::4 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol::4 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol::4 => pragma solidity ^0.8.0;
2023-03-zksync-main/contracts/openzeppelin/utils/Address.sol::4 => pragma solidity ^0.8.1;
```


