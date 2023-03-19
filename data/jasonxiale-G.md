# <x> += <y> costs more gas than <x> = <x> + <y> for state variables
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L54
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L73-L74
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L85-L86

# using unchecked to save gas
[L2EthToken.sol#L74](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L74) can be wrapped into __unchecked__ to save gas

# no need to cast to uint32
for [SystemContractsCaller.sol#L82](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractsCaller.sol#L82), the return value of Utils.safeCastToU32 is already **uint32**, so there is no need to call **uint32()** to do another cast
