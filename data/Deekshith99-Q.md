# [QA Report]

## [NC -1] Unused imports
There are 5 instances of it
It is recommended to remove unused imports for better readbility of the code
here are the permalinks of them
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L6
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractHelper.sol#L7
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractHelper.sol#L8
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractHelper.sol#L5
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L9

## [NC-2] Stop using v == 27 || v == 28 or v != 27 && v != 28 
There is 1 instance of it
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L176
for more refer this link
https://twitter.com/alexberegszaszi/status/1534461421454606336?s=20&t=H0Dv3ZT2bicx00hLWJk7Fg

## [NC-2] Use of a more recent solidity version
Context all contracts
currently our contracts are at ^0.8.0;
it is recomended to use the latest stable version
for better optimization and more secure