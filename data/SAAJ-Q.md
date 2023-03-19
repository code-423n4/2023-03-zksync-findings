
# Low Risk and Non-Critical Issues



# [L-01] Minting tokens to the zero address should be avoided (01 Instance)

Address(0) check is missing in function, consider applying check to ensure tokens aren’t minted to the zero address.

Link to the code:
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L2EthToken.sol#L72


# [N-01] Floating of Pragma (40 Instances)
Locking pragma version ensures contracts are not being deployed on an outdated compiler version.

Link to the code:
1.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/EmptyContract.sol#L3
2.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ImmutableSimulator.sol#L3
3.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L1Messenger.sol#L3
4.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L3
5.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BytecodeCompressor.sol#L3
6.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol#L3
7.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L2EthToken.sol#L3
8.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L3
9.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/KnownCodesStorage.sol#L3
10.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L3
11.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L3
12.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L3
13.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BootloaderUtilities.sol#L3
14.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L3
15.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/UnsafeBytesCalldata.sol#L3
16.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/Utils.sol#L2
17.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/RLPEncoder.sol#L3
18.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L4
19.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractsCaller.sol#L3
20.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L3
21.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/utils/Address.sol#L4
22.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L3
23.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IL1Messenger.sol#L3
24.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IPaymasterFlow.sol#L3
25.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IBootloaderUtilities.sol#L3
26.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IBytecodeCompressor.sol#L3
27.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IAccountCodeStorage.sol#L3
28.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IImmutableSimulator.sol#L3
29.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IL2StandardToken.sol#L3
30.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IMailbox.sol#L3
31.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IKnownCodesStorage.sol#L3
32.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IEthToken.sol#L3
33.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/INonceHolder.sol#L3
34.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol#L4
35.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/ISystemContext.sol#L3
36.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/IERC20.sol#L4
37.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IPaymaster.sol#L3
38.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IAccount.sol#L3
39.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IAccount.sol#L3
40.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IContractDeployer.sol#L3

# [N-02] Outdated Solidity Version (40 Instances)
Outdated version of solidity is used for all contracts in scope.
Recent version should be used to avoid known bugs that can impact the project negatively.

# [N-03] Empty blocks should be removed (02 Instances)

Avoid using empty blocks {} or use them for emitting events.

Link to the code:
1.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/EmptyContract.sol#L11
2.	https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/EmptyContract.sol#L13
