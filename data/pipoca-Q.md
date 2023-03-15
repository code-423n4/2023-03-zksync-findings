## 1.  Inappropriate Use of Assert

The use of assert can lead to Panic errors upon failure, hindering the implementation of error strings. Use custom errors instead.

File: DefaultAccount.sol | Line: 225 | assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L225
File: RLPEncoder.sol | Line: 45 | assert(_len != 1);
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/RLPEncoder.sol#L45

