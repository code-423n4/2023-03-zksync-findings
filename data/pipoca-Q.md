## 1.  Inappropriate Use of Assert

The use of assert can lead to Panic errors upon failure, hindering the implementation of error strings. Use custom errors instead.

File: DefaultAccount.sol | Line: 225 | assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L225
File: RLPEncoder.sol | Line: 45 | assert(_len != 1);
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/RLPEncoder.sol#L45

## 2. Requires without statement

If a require will be used, there should be a statement to inform developer and users.

File: SystemContractHelper.sol | Line: 152 | require(gasleft() >= _gasToBurn);
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol#L152
File: SystemContext.sol | Line: 16 | require(msg.sender == BOOTLOADER_FORMAL_ADDRESS);
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol#L16
File: NonceHolder.sol | Line: 135 | require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L135

## 3. Use the latest version of solidity + use same version for all contracts

This inconsistency may cause compatibility issues, hinder maintainability, and result in security vulnerabilities.

Utilizing older versions of Solidity may lead to suboptimal gas usage due to outdated features and optimizations. This can increase transaction costs for your users, affecting the overall experience and adoption of your smart contracts.

Older versions of Solidity may have known vulnerabilities that have been addressed in newer releases. Continuing to use these outdated versions exposes your smart contracts to potential security breaches and exploits.

File: AccountCodeStorage.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/AccountCodeStorage.sol#L3
File: Address.sol | Line: 4 | pragma solidity ^0.8.1;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/utils/Address.sol#L4
File: BootloaderUtilities.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BootloaderUtilities.sol#L3
File: BytecodeCompressor.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BytecodeCompressor.sol#L3
File: Constants.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/Constants.sol#L3
File: ContractDeployer.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L3
File: DefaultAccount.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L3
File: EfficientCall.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol#L3
File: EmptyContract.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/EmptyContract.sol#L3
File: IAccount.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IAccount.sol#L3
File: IAccountCodeStorage.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IAccountCodeStorage.sol#L3
File: IBootloaderUtilities.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IBootloaderUtilities.sol#L3
File: IBytecodeCompressor.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IBytecodeCompressor.sol#L3
File: IContractDeployer.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/IContractDeployer.sol#L3
File: IERC20.sol | Line: 4 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/token/ERC20/IERC20.sol#L4
File: IERC20Permit.sol | Line: 4 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol#L4
File: IEthToken.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IEthToken.sol#L3
File: IImmutableSimulator.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IImmutableSimulator.sol#L3
File: IKnownCodesStorage.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IKnownCodesStorage.sol#L3
File: IL1Messenger.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IL1Messenger.sol#L3
File: IL2StandardToken.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IL2StandardToken.sol#L3
File: IMailbox.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IMailbox.sol#L3
File: ImmutableSimulator.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ImmutableSimulator.sol#L3
File: INonceHolder.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/INonceHolder.sol#L3
File: IPaymaster.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IPaymaster.sol#L3
File: IPaymasterFlow.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IPaymasterFlow.sol#L3
File: ISystemContext.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/ISystemContext.sol#L3
File: KnownCodesStorage.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/KnownCodesStorage.sol#L3
File: L1Messenger.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L1Messenger.sol#L3
File: L2EthToken.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L3
File: MsgValueSimulator.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/MsgValueSimulator.sol#L3
File: NonceHolder.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L3
File: RLPEncoder.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/RLPEncoder.sol#L3
File: SafeERC20.sol | Line: 4 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L4
File: SystemContext.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol#L3
File: SystemContractHelper.sol | Line: 3 | pragma solidity ^0.8;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol#L3
File: SystemContractsCaller.sol | Line: 3 | pragma solidity ^0.8;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractsCaller.sol#L3
File: TransactionHelper.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L3
File: UnsafeBytesCalldata.sol | Line: 3 | pragma solidity ^0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/UnsafeBytesCalldata.sol#L3
File: Utils.sol | Line: 2 | pragma solidity >=0.8.0;
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/Utils.sol#L2

## 4. Lack of event emission when state changes occurs 

Contract: NonceHolder.sol

Although the contract emits an event for the `setValueUnderNonce` function, there are no events for other state-changing functions like `increaseMinNonce`, `incrementMinNonceIfEquals`, and `incrementDeploymentNonce`. It is good practice to emit events for all state-changing functions to facilitate easier tracking and monitoring of contract state changes.

https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L64
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L109
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L134






