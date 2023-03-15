## 1. Require statements with more than 32 bytes consume extra gas (29 instances found)

Reduce require statements so no statement exceeds 32 bytes, thus saving gas. 

File: AccountCodeStorage.sol | Line: 25 | require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/AccountCodeStorage.sol#L25

File: AccountCodeStorage.sol | Line: 36 | require(Utils.isContractConstructing(_hash), "Code hash is not for a contract on constructor");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/AccountCodeStorage.sol#L36

File: AccountCodeStorage.sol | Line: 50 | require(Utils.isContractConstructing(codeHash), "Code hash is not for a contract on constructor");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/AccountCodeStorage.sol#L50

File: Address.sol | Line: 69 | Address: unable to send value, recipient may have reverted
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/utils/Address.sol#L69

File: Address.sol | Line: 157 | Address: insufficient balance for call
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/utils/Address.sol#L157

File: BytecodeCompressor.sol | Line: 42 | require(dictionary.length % 8 == 0, "Dictionary length should be a multiple of 8");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BytecodeCompressor.sol#L42

File: BytecodeCompressor.sol | Line: 46 | Encoded data length should be 4 times shorter than the original bytecode
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BytecodeCompressor.sol#L46

File: BytecodeCompressor.sol | Line: 51 | require(indexOfEncodedChunk < dictionary.length, "Encoded chunk index is out of bounds");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BytecodeCompressor.sol#L51

File: BytecodeCompressor.sol | Line: 56 | require(encodedChunk == realChunk, "Encoded chunk does not match the original bytecode");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BytecodeCompressor.sol#L56

File: ContractDeployer.sol | Line: 75 | "It is only possible to change from sequential to arbitrary ordering"
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L75

File: ContractDeployer.sol | Line: 233 | require(msg.sender == FORCE_DEPLOYER, "Can only be called by FORCE_DEPLOYER_CONTRACT");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L233

File: ContractDeployer.sol | Line: 242 | require(msg.value == sumOfValues, "`value` provided is not equal to the combined `value`s of deployments");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L242

File: ContractDeployer.sol | Line: 256 | require(uint160(_newAddress) > MAX_SYSTEM_CONTRACT_ADDRESS, "Can not deploy contracts in kernel space");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L256

File: DefaultAccount.sol | Line: 103 | require(totalRequiredBalance <= address(this).balance, "Not enough balance for fee + value");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L103

File: DefaultAccount.sol | Line: 205 | require(success, "Failed to pay the fee to the operator");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L205

File: ImmutableSimulator.sol | Line: 34 | require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ImmutableSimulator.sol#L34

File: KnownCodesStorage.sol | Line: 24 | require(msg.sender == address(BYTECODE_COMPRESSOR_CONTRACT), "Callable only by the bytecode compressor");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/KnownCodesStorage.sol#L24

File: KnownCodesStorage.sol | Line: 128 | require(version == 1 && _bytecodeHash[1] == bytes1(0), "Incorrectly formatted bytecodeHash");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/KnownCodesStorage.sol#L128

File: L2EthToken.sol | Line: 45 | "Only system contracts with special access can call this method"
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L45

File: NonceHolder.sol | Line: 65 | require(_value <= MAXIMAL_MIN_NONCE_INCREMENT, "The value for incrementing the nonce is too high");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L65

File: SafeERC20.sol | Line: 62 | "SafeERC20: approve from non-zero to non-zero allowance"
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L62

File: SafeERC20.sol | Line: 95 | SafeERC20: decreased allowance below zero
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L95

File: SafeERC20.sol | Line: 124 | SafeERC20: permit did not succeed
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L124

File: SafeERC20.sol | Line: 147 |  "SafeERC20: ERC20 operation did not succeed"
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L147

File: SystemContext.sol | Line: 117 | require(currentBlockNumber + 1 == _expectedNewNumber, "The provided block number is not correct");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol#L117

File: SystemContractHelper.sol | Line: 310 | require(index < 10, "There are only 10 accessible registers");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol#L310

File: SystemContractHelper.sol | Line: 342 | "This method require system call flag"
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol#L342

File: TransactionHelper.sol | Line: 362 | require(_transaction.paymasterInput.length >= 4, "The standard paymaster input must be at least 4 bytes long");
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L362

File: TransactionHelper.sol | Line: 368 | "The approvalBased paymaster input must be at least 68 bytes long"
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L368



