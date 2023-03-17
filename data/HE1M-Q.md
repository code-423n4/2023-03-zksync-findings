### Q1
In the document, it is noted that the `gasLimit` is equal to `2^32-1`, but in the `SystemContext` the value is hardcoded to `1<<30`.
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L33
https://era.zksync.io/docs/dev/developer-guides/transactions/blocks.html#block-properties

### Q2
The revert message is missing.
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractHelper.sol#L152
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L47
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L135

### Q3
The following check is not needed as there is casting to uint128 in line 60.
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/MsgValueSimulator.sol#L48-L57
Using `safeCastToU128` is recomended.
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/Utils.sol#L19

### Q4
The modifier `onlySystemCall` is not needed as it is forced that `msg.sender == DEPLOYER_SYSTEM_CONTRACT`.
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L134

### Q5
`msg.sender` is forced to be `FORCE_DEPLOYER` contract address. So, no need to read `msg.sender`.
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L233
It can be hardcoded, like:
`this.forceDeployOnAddress{value: _deployments[i].value}(_deployments[i], FORCE_DEPLOYER);`
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L245

The same applies to line 212 as it only allows self call, so the `_sender` is always `FORCE_DEPLOYER`:
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L212

### Q6
If by chance the `_newAddress` during creating an account is in kernel space, the user can not deploy the contract. 
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L181
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L256

The probability is very very low, but since the kernel space is `2**16 -1`, the chance of address collision is higher than on Ethereum.

In that case, the user should change the bytecode of to-be-deployed contract, or increase his deployment nonce by deploying just a dummy contract.

### Q7
If bootlaoder calls `markFactoryDeps`, the parameter `_l1PreimageHash` is set zero as the parameter sent to `_markBytecodeAsPublished`.
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L36

When `BytecodeCompressor` during publishing the compressed bytecode, calls  `markBytecodeAsPublished`, parameter `_shouldSendToL1` is false in `_markBytecodeAsPublished`, so the `if` clause will not be executed.
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L63
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L51
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L68

So, the parameter `_l1PreimageHash` is not accessed at all, and can be removed.


