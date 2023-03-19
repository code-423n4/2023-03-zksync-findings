## Non Critical Issues


### [N-1] Signature malleability of EVM’S ecrecover()
The function calls the Solidity ecrecover() function directly to verify the given signatures. However, the ecrecover() EVM opcode allows malleable (non-unique) signatures and thus is susceptible to replay attacks.

  Although a replay attack seems not possible for this contract, I recommend using the battle-tested OpenZeppelin’s ECDSA library.
  
  #### Recommended Mitigation Steps
  Use the ecrecover function from OpenZeppelin’s ECDSA library for signature verification. (Ensure using a version > 4.7.3 for there was a critical bug >= 4.1.0 < 4.7.3).

*Find (1) instance(s) in contracts*:
```solidity
File: DefaultAccount.sol

189:         address recoveredAddress = ecrecover(_hash, v, r, s);

```
[DefaultAccount.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/DefaultAccount.sol)



### [N-2] Large multiples of ten should use scientific notation rather than decimal literals, for readability
#### Recommendation
Scientific notation should be used for better code readability.
Use scientific notation (e.g. 1e18, 100000) rather than exponentiation (e.g. 10**18, 1e5)

*Find (7) instance(s) in contracts*:
```solidity
File: BytecodeCompressor.sol

43:             require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big");

```
[BytecodeCompressor.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/BytecodeCompressor.sol)

```solidity
File: Constants.sol

74: uint256 constant MAX_MSG_VALUE = 2 ** 128 - 1;

```
[Constants.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/Constants.sol)

```solidity
File: NonceHolder.sol

27:     uint256 constant DEPLOY_NONCE_MULTIPLIER = 2 ** 128;

30:     uint256 constant MAXIMAL_MIN_NONCE_INCREMENT = 2 ** 32;

```
[NonceHolder.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/NonceHolder.sol)

```solidity
File: SystemContext.sol

40:     uint256 public difficulty = 2500000000000000;

48:     uint256 constant BLOCK_INFO_BLOCK_NUMBER_PART = 2 ** 128;

```
[SystemContext.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/SystemContext.sol)

```solidity
File: libraries/Utils.sol

81:         require(bytecodeLenInWords < 2 ** 16, "pp"); // bytecode length must be less than 2^16 words

```
[libraries/Utils.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/libraries/Utils.sol)




### [N-3] Unused imports

*Find (22) instance(s) in contracts*:
```solidity
File: AccountCodeStorage.sol

7: import {DEPLOYER_SYSTEM_CONTRACT, NONCE_HOLDER_SYSTEM_CONTRACT, CURRENT_MAX_PRECOMPILE_ADDRESS} from "./Constants.sol";

```
[AccountCodeStorage.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/AccountCodeStorage.sol)

```solidity
File: BytecodeCompressor.sol

6: import "./Constants.sol";

```
[BytecodeCompressor.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/BytecodeCompressor.sol)

```solidity
File: ContractDeployer.sol

5: import {ImmutableData} from "./interfaces/IImmutableSimulator.sol";

7: import {CREATE2_PREFIX, CREATE_PREFIX, NONCE_HOLDER_SYSTEM_CONTRACT, ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT, FORCE_DEPLOYER, MAX_SYSTEM_CONTRACT_ADDRESS, KNOWN_CODE_STORAGE_CONTRACT, ETH_TOKEN_SYSTEM_CONTRACT, IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT} from "./Constants.sol";

```
[ContractDeployer.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/ContractDeployer.sol)

```solidity
File: DefaultAccount.sol

9: import {BOOTLOADER_FORMAL_ADDRESS, NONCE_HOLDER_SYSTEM_CONTRACT, DEPLOYER_SYSTEM_CONTRACT, INonceHolder} from "./Constants.sol";

```
[DefaultAccount.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/DefaultAccount.sol)

```solidity
File: ImmutableSimulator.sol

6: import {DEPLOYER_SYSTEM_CONTRACT} from "./Constants.sol";

```
[ImmutableSimulator.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/ImmutableSimulator.sol)

```solidity
File: KnownCodesStorage.sol

8: import {BOOTLOADER_FORMAL_ADDRESS, BYTECODE_PUBLISHING_OVERHEAD, BYTECODE_COMPRESSOR_CONTRACT} from "./Constants.sol";

```
[KnownCodesStorage.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/KnownCodesStorage.sol)

```solidity
File: L2EthToken.sol

6: import {MSG_VALUE_SYSTEM_CONTRACT, DEPLOYER_SYSTEM_CONTRACT, BOOTLOADER_FORMAL_ADDRESS, L1_MESSENGER_CONTRACT} from "./Constants.sol";

```
[L2EthToken.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/L2EthToken.sol)

```solidity
File: MsgValueSimulator.sol

7: import {MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT, ETH_TOKEN_SYSTEM_CONTRACT, MAX_MSG_VALUE} from "./Constants.sol";

```
[MsgValueSimulator.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/MsgValueSimulator.sol)

```solidity
File: NonceHolder.sol

7: import {ISystemContract} from "./libraries/SystemContractHelper.sol";

8: import {DEPLOYER_SYSTEM_CONTRACT} from "./Constants.sol";

```
[NonceHolder.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/NonceHolder.sol)

```solidity
File: SystemContext.sol

7: import {BOOTLOADER_FORMAL_ADDRESS} from "./Constants.sol";

```
[SystemContext.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/SystemContext.sol)

```solidity
File: interfaces/IAccount.sol

5: import "../libraries/TransactionHelper.sol";

```
[interfaces/IAccount.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IAccount.sol)

```solidity
File: interfaces/IBootloaderUtilities.sol

5: import "../libraries/TransactionHelper.sol";

```
[interfaces/IBootloaderUtilities.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IBootloaderUtilities.sol)

```solidity
File: interfaces/IPaymaster.sol

5: import "../libraries/TransactionHelper.sol";

```
[interfaces/IPaymaster.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IPaymaster.sol)

```solidity
File: libraries/EfficientCall.sol

7: import {SHA256_SYSTEM_CONTRACT, KECCAK256_SYSTEM_CONTRACT} from "../Constants.sol";

```
[libraries/EfficientCall.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/libraries/EfficientCall.sol)

```solidity
File: libraries/SystemContractHelper.sol

5: import {MAX_SYSTEM_CONTRACT_ADDRESS, MSG_VALUE_SYSTEM_CONTRACT} from "../Constants.sol";

7: import "./SystemContractsCaller.sol";

8: import "./Utils.sol";

```
[libraries/SystemContractHelper.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/libraries/SystemContractHelper.sol)

```solidity
File: libraries/SystemContractsCaller.sol

5: import {MSG_VALUE_SYSTEM_CONTRACT, MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT} from "../Constants.sol";

```
[libraries/SystemContractsCaller.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/libraries/SystemContractsCaller.sol)

```solidity
File: libraries/TransactionHelper.sol

9: import "../interfaces/IContractDeployer.sol";

10: import {ETH_TOKEN_SYSTEM_CONTRACT, BOOTLOADER_FORMAL_ADDRESS} from "../Constants.sol";

```
[libraries/TransactionHelper.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/libraries/TransactionHelper.sol)

### [N-4] Use require instead of assert
Assert should not be used except for tests, require should be used.

Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process’s available gas instead of returning it, as request()/revert() did.

`assert() and reqire();`

The big difference between the two is that the assert()function when false, uses up all the remaining gas and reverts all the changes made.

Meanwhile, a require() function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay.This is the most common Solidity function used by developers for debugging and error handling.

Assertion() should be avoided even after solidity version 0.8.0, because its documentation states “The Assert function generates an error of type Panic(uint256). Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there’s a mistake”.

*Find (2) instance(s) in contracts*:
```solidity
File: DefaultAccount.sol

225:         assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);

```
[DefaultAccount.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/DefaultAccount.sol)

```solidity
File: libraries/RLPEncoder.sol

45:         assert(_len != 1);

```
[libraries/RLPEncoder.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/libraries/RLPEncoder.sol)


### [N-5] Constant values such as a call to keccak256(), should used to immutable rather than constant
There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.
  
Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

*Find (59) instance(s) in contracts*:
```solidity
File: Constants.sol

24: address constant ECRECOVER_SYSTEM_CONTRACT = address(0x01);

25: address constant SHA256_SYSTEM_CONTRACT = address(0x02);

32: uint256 constant CURRENT_MAX_PRECOMPILE_ADDRESS = uint256(uint160(SHA256_SYSTEM_CONTRACT));

34: address payable constant BOOTLOADER_FORMAL_ADDRESS = payable(address(SYSTEM_CONTRACTS_OFFSET + 0x01));

35: IAccountCodeStorage constant ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT = IAccountCodeStorage(

38: INonceHolder constant NONCE_HOLDER_SYSTEM_CONTRACT = INonceHolder(address(SYSTEM_CONTRACTS_OFFSET + 0x03));

39: IKnownCodesStorage constant KNOWN_CODE_STORAGE_CONTRACT = IKnownCodesStorage(address(SYSTEM_CONTRACTS_OFFSET + 0x04));

40: IImmutableSimulator constant IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT = IImmutableSimulator(

43: IContractDeployer constant DEPLOYER_SYSTEM_CONTRACT = IContractDeployer(address(SYSTEM_CONTRACTS_OFFSET + 0x06));

47: address constant FORCE_DEPLOYER = address(SYSTEM_CONTRACTS_OFFSET + 0x07);

48: IL1Messenger constant L1_MESSENGER_CONTRACT = IL1Messenger(address(SYSTEM_CONTRACTS_OFFSET + 0x08));

49: address constant MSG_VALUE_SYSTEM_CONTRACT = address(SYSTEM_CONTRACTS_OFFSET + 0x09);

51: IEthToken constant ETH_TOKEN_SYSTEM_CONTRACT = IEthToken(address(SYSTEM_CONTRACTS_OFFSET + 0x0a));

53: address constant KECCAK256_SYSTEM_CONTRACT = address(SYSTEM_CONTRACTS_OFFSET + 0x10);

55: ISystemContext constant SYSTEM_CONTEXT_CONTRACT = ISystemContext(payable(address(SYSTEM_CONTRACTS_OFFSET + 0x0b)));

57: BootloaderUtilities constant BOOTLOADER_UTILITIES = BootloaderUtilities(address(SYSTEM_CONTRACTS_OFFSET + 0x0c));

59: address constant EVENT_WRITER_CONTRACT = address(SYSTEM_CONTRACTS_OFFSET + 0x0d);

61: IBytecodeCompressor constant BYTECODE_COMPRESSOR_CONTRACT = IBytecodeCompressor(

74: uint256 constant MAX_MSG_VALUE = 2 ** 128 - 1;

```
[Constants.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/Constants.sol)

```solidity
File: NonceHolder.sol

27:     uint256 constant DEPLOY_NONCE_MULTIPLIER = 2 ** 128;

30:     uint256 constant MAXIMAL_MIN_NONCE_INCREMENT = 2 ** 32;

```
[NonceHolder.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/NonceHolder.sol)

```solidity
File: SystemContext.sol

48:     uint256 constant BLOCK_INFO_BLOCK_NUMBER_PART = 2 ** 128;

```
[SystemContext.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/SystemContext.sol)

```solidity
File: interfaces/IAccount.sol

7: bytes4 constant ACCOUNT_VALIDATION_SUCCESS_MAGIC = IAccount.validateTransaction.selector;

```
[interfaces/IAccount.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IAccount.sol)

```solidity
File: interfaces/IPaymaster.sol

12: bytes4 constant PAYMASTER_VALIDATION_SUCCESS_MAGIC = IPaymaster.validateAndPayForPaymasterTransaction.selector;

```
[interfaces/IPaymaster.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IPaymaster.sol)

```solidity
File: libraries/SystemContractsCaller.sol

12: address constant TO_L1_CALL_ADDRESS = address((1 << 16) - 1);

13: address constant CODE_ADDRESS_CALL_ADDRESS = address((1 << 16) - 2);

14: address constant PRECOMPILE_CALL_ADDRESS = address((1 << 16) - 3);

15: address constant META_CALL_ADDRESS = address((1 << 16) - 4);

16: address constant MIMIC_CALL_CALL_ADDRESS = address((1 << 16) - 5);

17: address constant SYSTEM_MIMIC_CALL_CALL_ADDRESS = address((1 << 16) - 6);

18: address constant MIMIC_CALL_BY_REF_CALL_ADDRESS = address((1 << 16) - 7);

19: address constant SYSTEM_MIMIC_CALL_BY_REF_CALL_ADDRESS = address((1 << 16) - 8);

20: address constant RAW_FAR_CALL_CALL_ADDRESS = address((1 << 16) - 9);

21: address constant RAW_FAR_CALL_BY_REF_CALL_ADDRESS = address((1 << 16) - 10);

22: address constant SYSTEM_CALL_CALL_ADDRESS = address((1 << 16) - 11);

23: address constant SYSTEM_CALL_BY_REF_CALL_ADDRESS = address((1 << 16) - 12);

24: address constant SET_CONTEXT_VALUE_CALL_ADDRESS = address((1 << 16) - 13);

25: address constant SET_PUBDATA_PRICE_CALL_ADDRESS = address((1 << 16) - 14);

26: address constant INCREMENT_TX_COUNTER_CALL_ADDRESS = address((1 << 16) - 15);

27: address constant PTR_CALLDATA_CALL_ADDRESS = address((1 << 16) - 16);

28: address constant CALLFLAGS_CALL_ADDRESS = address((1 << 16) - 17);

29: address constant PTR_RETURNDATA_CALL_ADDRESS = address((1 << 16) - 18);

30: address constant EVENT_INITIALIZE_ADDRESS = address((1 << 16) - 19);

31: address constant EVENT_WRITE_ADDRESS = address((1 << 16) - 20);

32: address constant LOAD_CALLDATA_INTO_ACTIVE_PTR_CALL_ADDRESS = address((1 << 16) - 21);

33: address constant LOAD_LATEST_RETURNDATA_INTO_ACTIVE_PTR_CALL_ADDRESS = address((1 << 16) - 22);

34: address constant PTR_ADD_INTO_ACTIVE_CALL_ADDRESS = address((1 << 16) - 23);

35: address constant PTR_SHRINK_INTO_ACTIVE_CALL_ADDRESS = address((1 << 16) - 24);

36: address constant PTR_PACK_INTO_ACTIVE_CALL_ADDRESS = address((1 << 16) - 25);

37: address constant MULTIPLICATION_HIGH_ADDRESS = address((1 << 16) - 26);

38: address constant GET_EXTRA_ABI_DATA_ADDRESS = address((1 << 16) - 27);

41: uint256 constant META_GAS_PER_PUBDATA_BYTE_OFFSET = 0 * 8;

42: uint256 constant META_HEAP_SIZE_OFFSET = 8 * 8;

43: uint256 constant META_AUX_HEAP_SIZE_OFFSET = 12 * 8;

44: uint256 constant META_SHARD_ID_OFFSET = 28 * 8;

45: uint256 constant META_CALLER_SHARD_ID_OFFSET = 29 * 8;

46: uint256 constant META_CODE_SHARD_ID_OFFSET = 30 * 8;

```
[libraries/SystemContractsCaller.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/libraries/SystemContractsCaller.sol)

```solidity
File: libraries/TransactionHelper.sol

81:     bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");

83:     bytes32 constant EIP712_TRANSACTION_TYPE_HASH =

```
[libraries/TransactionHelper.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/libraries/TransactionHelper.sol)



### [N-6] Stop using v != 27 && v != 28 or v == 27 || v == 28


*Find (1) instance(s) in contracts*:
```solidity
File: contracts/DefaultAccount.sol 

176:        require(v == 27 || v == 28, "v is neither 27 nor 28");

```
[contracts/DefaultAccount.sol ](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol)

See this for reference:
https://twitter.com/alexberegszaszi/status/1534461421454606336?s=20&t=H0Dv3ZT2bicx00hLWJk7Fg

## Low Issues

### [L-1] Non-library/interface files should use fixed compiler versions, not floating ones

*Find (40) instance(s) in contracts*:
```solidity
File: AccountCodeStorage.sol

3: pragma solidity ^0.8.0;

```
[AccountCodeStorage.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/AccountCodeStorage.sol)

```solidity
File: BootloaderUtilities.sol

3: pragma solidity ^0.8.0;

```
[BootloaderUtilities.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/BootloaderUtilities.sol)

```solidity
File: BytecodeCompressor.sol

3: pragma solidity ^0.8.0;

```
[BytecodeCompressor.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/BytecodeCompressor.sol)

```solidity
File: Constants.sol

3: pragma solidity ^0.8.0;

```
[Constants.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/Constants.sol)

```solidity
File: ContractDeployer.sol

3: pragma solidity ^0.8.0;

```
[ContractDeployer.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/ContractDeployer.sol)

```solidity
File: DefaultAccount.sol

3: pragma solidity ^0.8.0;

```
[DefaultAccount.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/DefaultAccount.sol)

```solidity
File: EmptyContract.sol

3: pragma solidity ^0.8.0;

```
[EmptyContract.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/EmptyContract.sol)

```solidity
File: ImmutableSimulator.sol

3: pragma solidity ^0.8.0;

```
[ImmutableSimulator.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/ImmutableSimulator.sol)

```solidity
File: KnownCodesStorage.sol

3: pragma solidity ^0.8.0;

```
[KnownCodesStorage.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/KnownCodesStorage.sol)

```solidity
File: L1Messenger.sol

3: pragma solidity ^0.8.0;

```
[L1Messenger.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/L1Messenger.sol)

```solidity
File: L2EthToken.sol

3: pragma solidity ^0.8.0;

```
[L2EthToken.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/L2EthToken.sol)

```solidity
File: MsgValueSimulator.sol

3: pragma solidity ^0.8.0;

```
[MsgValueSimulator.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/MsgValueSimulator.sol)

```solidity
File: NonceHolder.sol

3: pragma solidity ^0.8.0;

```
[NonceHolder.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/NonceHolder.sol)

```solidity
File: SystemContext.sol

3: pragma solidity ^0.8.0;

```
[SystemContext.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/SystemContext.sol)

```solidity
File: interfaces/IAccount.sol

3: pragma solidity ^0.8.0;

```
[interfaces/IAccount.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IAccount.sol)

```solidity
File: interfaces/IAccountCodeStorage.sol

3: pragma solidity ^0.8.0;

```
[interfaces/IAccountCodeStorage.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IAccountCodeStorage.sol)

```solidity
File: interfaces/IBootloaderUtilities.sol

3: pragma solidity ^0.8.0;

```
[interfaces/IBootloaderUtilities.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IBootloaderUtilities.sol)

```solidity
File: interfaces/IBytecodeCompressor.sol

3: pragma solidity ^0.8.0;

```
[interfaces/IBytecodeCompressor.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IBytecodeCompressor.sol)

```solidity
File: interfaces/IContractDeployer.sol

3: pragma solidity ^0.8.0;

```
[interfaces/IContractDeployer.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IContractDeployer.sol)

```solidity
File: interfaces/IEthToken.sol

3: pragma solidity ^0.8.0;

```
[interfaces/IEthToken.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IEthToken.sol)

```solidity
File: interfaces/IImmutableSimulator.sol

3: pragma solidity ^0.8.0;

```
[interfaces/IImmutableSimulator.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IImmutableSimulator.sol)

```solidity
File: interfaces/IKnownCodesStorage.sol

3: pragma solidity ^0.8.0;

```
[interfaces/IKnownCodesStorage.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IKnownCodesStorage.sol)

```solidity
File: interfaces/IL1Messenger.sol

3: pragma solidity ^0.8.0;

```
[interfaces/IL1Messenger.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IL1Messenger.sol)

```solidity
File: interfaces/IL2StandardToken.sol

3: pragma solidity ^0.8.0;

```
[interfaces/IL2StandardToken.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IL2StandardToken.sol)

```solidity
File: interfaces/IMailbox.sol

3: pragma solidity ^0.8.0;

```
[interfaces/IMailbox.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IMailbox.sol)

```solidity
File: interfaces/INonceHolder.sol

3: pragma solidity ^0.8.0;

```
[interfaces/INonceHolder.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/INonceHolder.sol)

```solidity
File: interfaces/IPaymaster.sol

3: pragma solidity ^0.8.0;

```
[interfaces/IPaymaster.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IPaymaster.sol)

```solidity
File: interfaces/IPaymasterFlow.sol

3: pragma solidity ^0.8.0;

```
[interfaces/IPaymasterFlow.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/IPaymasterFlow.sol)

```solidity
File: interfaces/ISystemContext.sol

3: pragma solidity ^0.8.0;

```
[interfaces/ISystemContext.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/interfaces/ISystemContext.sol)

```solidity
File: libraries/EfficientCall.sol

3: pragma solidity ^0.8.0;

```
[libraries/EfficientCall.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/libraries/EfficientCall.sol)

```solidity
File: libraries/RLPEncoder.sol

3: pragma solidity ^0.8.0;

```
[libraries/RLPEncoder.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/libraries/RLPEncoder.sol)

```solidity
File: libraries/SystemContractHelper.sol

3: pragma solidity ^0.8;

```
[libraries/SystemContractHelper.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/libraries/SystemContractHelper.sol)

```solidity
File: libraries/SystemContractsCaller.sol

3: pragma solidity ^0.8;

```
[libraries/SystemContractsCaller.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/libraries/SystemContractsCaller.sol)

```solidity
File: libraries/TransactionHelper.sol

3: pragma solidity ^0.8.0;

```
[libraries/TransactionHelper.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/libraries/TransactionHelper.sol)

```solidity
File: libraries/UnsafeBytesCalldata.sol

3: pragma solidity ^0.8.0;

```
[libraries/UnsafeBytesCalldata.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/libraries/UnsafeBytesCalldata.sol)

```solidity
File: libraries/Utils.sol

2: pragma solidity >=0.8.0;

```
[libraries/Utils.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/libraries/Utils.sol)

```solidity
File: openzeppelin/token/ERC20/IERC20.sol

4: pragma solidity ^0.8.0;

```
[openzeppelin/token/ERC20/IERC20.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/openzeppelin/token/ERC20/IERC20.sol)

```solidity
File: openzeppelin/token/ERC20/extensions/IERC20Permit.sol

4: pragma solidity ^0.8.0;

```
[openzeppelin/token/ERC20/extensions/IERC20Permit.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/openzeppelin/token/ERC20/extensions/IERC20Permit.sol)

```solidity
File: openzeppelin/token/ERC20/utils/SafeERC20.sol

4: pragma solidity ^0.8.0;

```
[openzeppelin/token/ERC20/utils/SafeERC20.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/openzeppelin/token/ERC20/utils/SafeERC20.sol)

```solidity
File: openzeppelin/utils/Address.sol

4: pragma solidity ^0.8.1;

```
[openzeppelin/utils/Address.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/openzeppelin/utils/Address.sol)

### [L-2] Unused/Empty RECEIVE()/FALLBACK() function
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth))). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds.

*Find (2) instance(s) in contracts*:
```solidity
File: EmptyContract.sol

11:     fallback() external payable {}

13:     receive() external payable {}

```
[EmptyContract.sol](https://github.com/code-423n4/2023-03-zksync//blob/main/EmptyContract.sol)