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





