# Gas Report 

[Exclusive findings](#exclusive-findings) is below the [automated findings](#automated-findings).

---

## Automated Findings
## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | Use `selfbalance()` instead of `address(this).balance` | 3 |
| [GAS-2](#GAS-2) | Use assembly to check for `address(0)` | 4 |
| [GAS-3](#GAS-3) | Cache array length outside of loop | 1 |
| [GAS-4](#GAS-4) | Use calldata instead of memory for function arguments that do not get mutated | 1 |
| [GAS-5](#GAS-5) | Use Custom Errors | 54 |
| [GAS-6](#GAS-6) | Don't initialize variables with default value | 6 |
| [GAS-7](#GAS-7) | Long revert strings | 18 |
| [GAS-8](#GAS-8) | Use shift Right/Left instead of division/multiplication if possible | 28 |
| [GAS-9](#GAS-9) | Splitting require() statements that use && saves gas | 1 |
| [GAS-10](#GAS-10) | Use `storage` instead of `memory` for structs/arrays | 52 |
| [GAS-11](#GAS-11) | Increments can be `unchecked` in for-loops | 4 |
| [GAS-12](#GAS-12) | Use != 0 instead of > 0 for unsigned integer comparison | 8 |
| [GAS-13](#GAS-13) | `internal` functions not called by the contract should be removed | 53 |
### [GAS-1] Use `selfbalance()` instead of `address(this).balance`
Use assembly when getting a contract's balance of ETH.

You can use `selfbalance()` instead of `address(this).balance` when getting your contract's balance of ETH to save gas.
Additionally, you can use `balance(address)` instead of `address.balance()` when getting an external contract's balance of ETH.

*Saves 15 gas when checking internal balance, 6 for external*

*Instances (3)*:
```solidity
File: contracts/DefaultAccount.sol

103:         require(totalRequiredBalance <= address(this).balance, "Not enough balance for fee + value");

```

```solidity
File: contracts/openzeppelin/utils/Address.sol

62:             address(this).balance >= amount,

156:             address(this).balance >= value,

```

### [GAS-2] Use assembly to check for `address(0)`
*Saves 6 gas per instance*

*Instances (4)*:
```solidity
File: contracts/DefaultAccount.sol

93:         bytes32 txHash = _suggestedSignedHash != bytes32(0) ? _suggestedSignedHash : _transaction.encodeHash();

191:         return recoveredAddress == address(this) && recoveredAddress != address(0);

```

```solidity
File: contracts/KnownCodesStorage.sol

128:         require(version == 1 && _bytecodeHash[1] == bytes1(0), "Incorrectly formatted bytecodeHash");

```

```solidity
File: contracts/libraries/TransactionHelper.sol

405:         if (address(uint160(_transaction.paymaster)) != address(0)) {

```

### [GAS-3] Cache array length outside of loop
If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

*Instances (1)*:
```solidity
File: contracts/BytecodeCompressor.sol

49:             for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) {

```

### [GAS-4] Use calldata instead of memory for function arguments that do not get mutated
Mark data types as `calldata` instead of `memory` where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as `calldata`. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies `memory` storage.

*Instances (1)*:
```solidity
File: contracts/interfaces/IL1Messenger.sol

10:     function sendToL1(bytes memory _message) external returns (bytes32);

```

### [GAS-5] Use Custom Errors
[Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)
Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.

*Instances (54)*:
```solidity
File: contracts/AccountCodeStorage.sol

25:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

36:         require(Utils.isContractConstructing(_hash), "Code hash is not for a contract on constructor");

50:         require(Utils.isContractConstructing(codeHash), "Code hash is not for a contract on constructor");

```

```solidity
File: contracts/BootloaderUtilities.sol

36:             revert("Unsupported tx type");

91:             require(vInt == 27 || vInt == 28, "Invalid v value");

190:             require(vInt == 27 || vInt == 28, "Invalid v value");

285:             require(vInt == 27 || vInt == 28, "Invalid v value");

```

```solidity
File: contracts/BytecodeCompressor.sol

42:             require(dictionary.length % 8 == 0, "Dictionary length should be a multiple of 8");

43:             require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big");

51:                 require(indexOfEncodedChunk < dictionary.length, "Encoded chunk index is out of bounds");

56:                 require(encodedChunk == realChunk, "Encoded chunk does not match the original bytecode");

```

```solidity
File: contracts/ContractDeployer.sol

27:         require(msg.sender == address(this), "Callable only by self");

233:         require(msg.sender == FORCE_DEPLOYER, "Can only be called by FORCE_DEPLOYER_CONTRACT");

242:         require(msg.value == sumOfValues, "`value` provided is not equal to the combined `value`s of deployments");

255:         require(_bytecodeHash != bytes32(0x0), "BytecodeHash can not be zero");

256:         require(uint160(_newAddress) > MAX_SYSTEM_CONTRACT_ADDRESS, "Can not deploy contracts in kernel space");

264:         require(NONCE_HOLDER_SYSTEM_CONTRACT.getRawNonce(_newAddress) == 0x00, "Account is occupied");

296:         require(knownCodeMarker > 0, "The code hash is not known");

```

```solidity
File: contracts/DefaultAccount.sol

96:             require(_transaction.data.length >= 4, "Invalid call to ContractDeployer");

103:         require(totalRequiredBalance <= address(this).balance, "Not enough balance for fee + value");

163:         require(_signature.length == 65, "Signature length is incorrect");

176:         require(v == 27 || v == 28, "v is neither 27 nor 28");

187:         require(uint256(s) <= 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0, "Invalid s");

205:         require(success, "Failed to pay the fee to the operator");

```

```solidity
File: contracts/ImmutableSimulator.sol

34:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

```

```solidity
File: contracts/KnownCodesStorage.sol

19:         require(msg.sender == BOOTLOADER_FORMAL_ADDRESS, "Callable only by the bootloader");

24:         require(msg.sender == address(BYTECODE_COMPRESSOR_CONTRACT), "Callable only by the bytecode compressor");

112:         require(precompileCallSuccess, "Failed to charge gas");

128:         require(version == 1 && _bytecodeHash[1] == bytes1(0), "Incorrectly formatted bytecodeHash");

130:         require(Utils.bytecodeLenInWords(_bytecodeHash) % 2 == 1, "Code length in words must be odd");

```

```solidity
File: contracts/L2EthToken.sol

29:         require(msg.sender == BOOTLOADER_FORMAL_ADDRESS, "Callable only by the bootloader");

49:         require(fromBalance >= _amount, "Transfer amount exceeds balance");

```

```solidity
File: contracts/NonceHolder.sol

65:         require(_value <= MAXIMAL_MIN_NONCE_INCREMENT, "The value for incrementing the nonce is too high");

84:         require(_value != 0, "Nonce value can not be set to 0");

88:             require(isNonceUsed(msg.sender, _key - 1), "Previous nonce has not been used");

114:         require(oldMinNonce == _expectedNonce, "Incorrect nonce");

135:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "");

162:             revert("Reusing the same nonce twice");

164:             revert("The nonce was not set as used");

```

```solidity
File: contracts/SystemContext.sol

116:         require(_newTimestamp >= currentBlockTimestamp, "Timestamps should be incremental");

117:         require(currentBlockNumber + 1 == _expectedNewNumber, "The provided block number is not correct");

```

```solidity
File: contracts/libraries/EfficientCall.sol

37:         require(returnData.length == 32, "keccak256 returned invalid data");

46:         require(returnData.length == 32, "sha returned invalid data");

```

```solidity
File: contracts/libraries/SystemContractHelper.sol

310:         require(index < 10, "There are only 10 accessible registers");

```

```solidity
File: contracts/libraries/TransactionHelper.sol

111:             revert("Encoding unsupported tx");

362:         require(_transaction.paymasterInput.length >= 4, "The standard paymaster input must be at least 4 bytes long");

387:             revert("Unsupported paymaster flow");

```

```solidity
File: contracts/libraries/Utils.sol

20:         require(_x <= type(uint128).max, "Overflow");

26:         require(_x <= type(uint32).max, "Overflow");

32:         require(_x <= type(uint24).max, "Overflow");

78:         require(_bytecode.length % 32 == 0, "po");

81:         require(bytecodeLenInWords < 2 ** 16, "pp"); // bytecode length must be less than 2^16 words

82:         require(bytecodeLenInWords % 2 == 1, "pr"); // bytecode length in words must be odd

```

```solidity
File: contracts/openzeppelin/utils/Address.sol

266:                 require(isContract(target), "Address: call to non-contract");

```

### [GAS-6] Don't initialize variables with default value

*Instances (6)*:
```solidity
File: contracts/BytecodeCompressor.sol

49:             for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) {

```

```solidity
File: contracts/ContractDeployer.sol

238:         uint256 sumOfValues = 0;

239:         for (uint256 i = 0; i < deploymentsLength; ++i) {

244:         for (uint256 i = 0; i < deploymentsLength; ++i) {

```

```solidity
File: contracts/ImmutableSimulator.sol

37:             for (uint256 i = 0; i < immutablesLength; ++i) {

```

```solidity
File: contracts/KnownCodesStorage.sol

34:             for (uint256 i = 0; i < hashesLen; ++i) {

```

### [GAS-7] Long revert strings

*Instances (18)*:
```solidity
File: contracts/AccountCodeStorage.sol

25:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

36:         require(Utils.isContractConstructing(_hash), "Code hash is not for a contract on constructor");

50:         require(Utils.isContractConstructing(codeHash), "Code hash is not for a contract on constructor");

```

```solidity
File: contracts/BytecodeCompressor.sol

42:             require(dictionary.length % 8 == 0, "Dictionary length should be a multiple of 8");

51:                 require(indexOfEncodedChunk < dictionary.length, "Encoded chunk index is out of bounds");

56:                 require(encodedChunk == realChunk, "Encoded chunk does not match the original bytecode");

```

```solidity
File: contracts/ContractDeployer.sol

233:         require(msg.sender == FORCE_DEPLOYER, "Can only be called by FORCE_DEPLOYER_CONTRACT");

242:         require(msg.value == sumOfValues, "`value` provided is not equal to the combined `value`s of deployments");

256:         require(uint160(_newAddress) > MAX_SYSTEM_CONTRACT_ADDRESS, "Can not deploy contracts in kernel space");

```

```solidity
File: contracts/DefaultAccount.sol

103:         require(totalRequiredBalance <= address(this).balance, "Not enough balance for fee + value");

205:         require(success, "Failed to pay the fee to the operator");

```

```solidity
File: contracts/ImmutableSimulator.sol

34:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

```

```solidity
File: contracts/KnownCodesStorage.sol

24:         require(msg.sender == address(BYTECODE_COMPRESSOR_CONTRACT), "Callable only by the bytecode compressor");

128:         require(version == 1 && _bytecodeHash[1] == bytes1(0), "Incorrectly formatted bytecodeHash");

```

```solidity
File: contracts/NonceHolder.sol

65:         require(_value <= MAXIMAL_MIN_NONCE_INCREMENT, "The value for incrementing the nonce is too high");

```

```solidity
File: contracts/SystemContext.sol

117:         require(currentBlockNumber + 1 == _expectedNewNumber, "The provided block number is not correct");

```

```solidity
File: contracts/libraries/SystemContractHelper.sol

310:         require(index < 10, "There are only 10 accessible registers");

```

```solidity
File: contracts/libraries/TransactionHelper.sol

362:         require(_transaction.paymasterInput.length >= 4, "The standard paymaster input must be at least 4 bytes long");

```

### [GAS-8] Use shift Right/Left instead of division/multiplication if possible
Shifting left by N is like multiplying by 2^N and shifting right by N is like dividing by 2^N

*Instances (28)*:
```solidity
File: contracts/BootloaderUtilities.sol

97:             }

```

```solidity
File: contracts/BytecodeCompressor.sol

44:             require(

44:             require(

46:                 "Encoded data length should be 4 times shorter than the original bytecode"

51:                 require(indexOfEncodedChunk < dictionary.length, "Encoded chunk index is out of bounds");

55: 

82:             encodedData = _rawCompressedData[2 + dictionaryLen * 8:];

83:         }

```

```solidity
File: contracts/Constants.sol

19: 

23: 

75: 

```

```solidity
File: contracts/L1Messenger.sol

38:         }

38:         }

```

```solidity
File: contracts/NonceHolder.sol

28:     /// The minNonce can be increased by at 2^32 at a time to prevent it from

31: 

```

```solidity
File: contracts/SystemContext.sol

49: 

```

```solidity
File: contracts/libraries/RLPEncoder.sol

33: 

68: 

```

```solidity
File: contracts/libraries/SystemContractsCaller.sol

42: uint256 constant META_HEAP_SIZE_OFFSET = 8 * 8;

43: uint256 constant META_AUX_HEAP_SIZE_OFFSET = 12 * 8;

44: uint256 constant META_SHARD_ID_OFFSET = 28 * 8;

45: uint256 constant META_CALLER_SHARD_ID_OFFSET = 29 * 8;

46: uint256 constant META_CODE_SHARD_ID_OFFSET = 30 * 8;

47: 

```

```solidity
File: contracts/libraries/Utils.sol

40:     }

46:         }

81:         require(bytecodeLenInWords < 2 ** 16, "pp"); // bytecode length must be less than 2^16 words

82:         require(bytecodeLenInWords % 2 == 1, "pr"); // bytecode length in words must be odd

```

### [GAS-9] Splitting require() statements that use && saves gas

*Instances (1)*:
```solidity
File: contracts/KnownCodesStorage.sol

128:         require(version == 1 && _bytecodeHash[1] == bytes1(0), "Incorrectly formatted bytecodeHash");

```

### [GAS-10] Use `storage` instead of `memory` for structs/arrays
Using `memory` copies the struct or array in memory. Use `storage` to save the location in storage and have cheaper reads:

*Instances (52)*:
```solidity
File: contracts/BootloaderUtilities.sol

52:         // Encode `gasPrice` and `gasLimit` together to prevent "stack too deep error".

56:             bytes memory encodedGasLimit = RLPEncoder.encodeUint256(_transaction.gasLimit);

57:             encodedGasParam = bytes.concat(encodedGasPrice, encodedGasLimit);

61:         bytes memory encodedValue = RLPEncoder.encodeUint256(_transaction.value);

62:         // Encode only the length of the transaction data, and not the data itself,

143:             bytes memory encodedNonce = RLPEncoder.encodeUint256(_transaction.nonce);

144:             bytes memory encodedGasPrice = RLPEncoder.encodeUint256(_transaction.maxFeePerGas);

145:             bytes memory encodedGasLimit = RLPEncoder.encodeUint256(_transaction.gasLimit);

146:             bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));

147:             bytes memory encodedValue = RLPEncoder.encodeUint256(_transaction.value);

148:             encodedFixedLengthParams = bytes.concat(

176: 

236:             bytes memory encodedNonce = RLPEncoder.encodeUint256(_transaction.nonce);

237:             bytes memory encodedMaxPriorityFeePerGas = RLPEncoder.encodeUint256(_transaction.maxPriorityFeePerGas);

238:             bytes memory encodedMaxFeePerGas = RLPEncoder.encodeUint256(_transaction.maxFeePerGas);

239:             bytes memory encodedGasLimit = RLPEncoder.encodeUint256(_transaction.gasLimit);

240:             bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));

241:             bytes memory encodedValue = RLPEncoder.encodeUint256(_transaction.value);

242:             encodedFixedLengthParams = bytes.concat(

271: 

```

```solidity
File: contracts/ContractDeployer.sol

40:         if (info.supportedAAVersion != AccountAbstractionVersion.None) {

71: 

324:         ImmutableData[] memory immutables = abi.decode(returnData, (ImmutableData[]));

325:         IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT.setImmutables(_newAddress, immutables);

```

```solidity
File: contracts/L2EthToken.sol

91:         L1_MESSENGER_CONTRACT.sendToL1(message);

```

```solidity
File: contracts/NonceHolder.sol

83: 

```

```solidity
File: contracts/libraries/EfficientCall.sol

37:         require(returnData.length == 32, "keccak256 returned invalid data");

46:         require(returnData.length == 32, "sha returned invalid data");

```

```solidity
File: contracts/libraries/TransactionHelper.sol

155:         // Encode `gasPrice` and `gasLimit` together to prevent "stack too deep error".

159:             bytes memory encodedGasLimit = RLPEncoder.encodeUint256(_transaction.gasLimit);

160:             encodedGasParam = bytes.concat(encodedGasPrice, encodedGasLimit);

164:         bytes memory encodedValue = RLPEncoder.encodeUint256(_transaction.value);

165:         // Encode only the length of the transaction data, and not the data itself,

228:             bytes memory encodedNonce = RLPEncoder.encodeUint256(_transaction.nonce);

229:             bytes memory encodedGasPrice = RLPEncoder.encodeUint256(_transaction.maxFeePerGas);

230:             bytes memory encodedGasLimit = RLPEncoder.encodeUint256(_transaction.gasLimit);

231:             bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));

232:             bytes memory encodedValue = RLPEncoder.encodeUint256(_transaction.value);

233:             encodedFixedLengthParams = bytes.concat(

261: 

298:             bytes memory encodedNonce = RLPEncoder.encodeUint256(_transaction.nonce);

299:             bytes memory encodedMaxPriorityFeePerGas = RLPEncoder.encodeUint256(_transaction.maxPriorityFeePerGas);

300:             bytes memory encodedMaxFeePerGas = RLPEncoder.encodeUint256(_transaction.maxFeePerGas);

301:             bytes memory encodedGasLimit = RLPEncoder.encodeUint256(_transaction.gasLimit);

302:             bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));

303:             bytes memory encodedValue = RLPEncoder.encodeUint256(_transaction.value);

304:             encodedFixedLengthParams = bytes.concat(

333: 

```

```solidity
File: contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol

140:             data,

```

```solidity
File: contracts/openzeppelin/utils/Address.sol

160:             data

202:         return

241:         return

```

### [GAS-11] Increments can be `unchecked` in for-loops

*Instances (4)*:
```solidity
File: contracts/ContractDeployer.sol

239:         for (uint256 i = 0; i < deploymentsLength; ++i) {

244:         for (uint256 i = 0; i < deploymentsLength; ++i) {

```

```solidity
File: contracts/ImmutableSimulator.sol

37:             for (uint256 i = 0; i < immutablesLength; ++i) {

```

```solidity
File: contracts/KnownCodesStorage.sol

34:             for (uint256 i = 0; i < hashesLen; ++i) {

```

### [GAS-12] Use != 0 instead of > 0 for unsigned integer comparison

*Instances (8)*:
```solidity
File: contracts/AccountCodeStorage.sol

87:         if (codeHash == 0x00 && NONCE_HOLDER_SYSTEM_CONTRACT.getRawNonce(account) > 0) {

```

```solidity
File: contracts/ContractDeployer.sol

296:         require(knownCodeMarker > 0, "The code hash is not known");

317:         if (value > 0) {

```

```solidity
File: contracts/NonceHolder.sol

148:         return (_nonce < getMinNonce(_address) || nonceValues[addressAsKey][_nonce] > 0);

```

```solidity
File: contracts/libraries/Utils.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol

143:         if (returndata.length > 0) {

```

```solidity
File: contracts/openzeppelin/utils/Address.sol

41:         return account.code.length > 0;

297:         if (returndata.length > 0) {

```

### [GAS-13] `internal` functions not called by the contract should be removed
If the functions are required by an interface, the contract should inherit from that interface and use the `override` keyword

*Instances (53)*:
```solidity
File: contracts/libraries/EfficientCall.sol

35:     function keccak(bytes calldata _data) internal view returns (bytes32) {

44:     function sha(bytes calldata _data) internal view returns (bytes32) {

56:     function call(

85:     function delegateCall(

102:     function mimicCall(

```

```solidity
File: contracts/libraries/RLPEncoder.sol

6:     function encodeAddress(address _val) internal pure returns (bytes memory encoded) {

19:     function encodeUint256(uint256 _val) internal pure returns (bytes memory encoded) {

44:     function encodeNonSingleBytesLen(uint64 _len) internal pure returns (bytes memory) {

51:     function encodeListLen(uint64 _len) internal pure returns (bytes memory) {

```

```solidity
File: contracts/libraries/SystemContractHelper.sol

48:     function toL1(bool _isService, bytes32 _key, bytes32 _value) internal {

63:     function getCodeAddress() internal view returns (address addr) {

74:     function loadCalldataIntoActivePtr() internal view {

85:     function ptrPackIntoActivePtr(uint256 _farCallAbi) internal view {

94:     function ptrAddIntoActive(uint32 _value) internal view {

106:     function ptrShrinkIntoActive(uint32 _shrink) internal view {

124:     function packPrecompileParams(

147:     function precompileCall(uint256 _rawParams, uint32 _gasToBurn) internal view returns (bool success) {

164:     function setValueForNextFarCall(uint128 _value) internal returns (bool success) {

177:     function eventInitialize(uint256 initializer, uint256 value1) internal {

187:     function eventWrite(uint256 value1, uint256 value2) internal {

231:     function getHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 heapSize) {

240:     function getAuxHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 auxHeapSize) {

272:     function getZkSyncMeta() internal view returns (ZkSyncMeta memory meta) {

298:     function getCalldataPtr() internal view returns (uint256 ptr) {

309:     function getExtraAbiData(uint256 index) internal view returns (uint256 extraAbiData) {

320:     function isSystemCall() internal view returns (bool) {

329:     function isSystemContract(address _address) internal pure returns (bool) {

```

```solidity
File: contracts/libraries/SystemContractsCaller.sol

149:     function systemCallWithPropagatedRevert(

213:     function getFarCallABI(

```

```solidity
File: contracts/libraries/TransactionHelper.sol

93:     function isEthToken(uint256 _addr) internal pure returns (bool) {

99:     function encodeHash(Transaction calldata _transaction) internal view returns (bytes32 resultHash) {

361:     function processPaymasterInput(Transaction calldata _transaction) internal {

394:     function payToTheBootloader(Transaction calldata _transaction) internal returns (bool success) {

404:     function totalRequiredBalance(Transaction calldata _transaction) internal pure returns (uint256 requiredBalance) {

```

```solidity
File: contracts/libraries/UnsafeBytesCalldata.sol

18:     function readUint16(bytes calldata _bytes, uint256 _start) internal pure returns (uint16 result) {

25:     function readUint64(bytes calldata _bytes, uint256 _start) internal pure returns (uint64 result) {

```

```solidity
File: contracts/libraries/Utils.sol

19:     function safeCastToU128(uint256 _x) internal pure returns (uint128) {

25:     function safeCastToU32(uint256 _x) internal pure returns (uint32) {

31:     function safeCastToU24(uint256 _x) internal pure returns (uint24) {

38:     function bytecodeLenInBytes(bytes32 _bytecodeHash) internal pure returns (uint256 codeLength) {

50:     function isContractConstructing(bytes32 _bytecodeHash) internal pure returns (bool) {

57:     function constructingBytecodeHash(bytes32 _bytecodeHash) internal pure returns (bytes32) {

76:     function hashL2Bytecode(bytes calldata _bytecode) internal view returns (bytes32 hashedBytecode) {

```

```solidity
File: contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol

22:     function safeTransfer(

33:     function safeTransferFrom(

52:     function safeApprove(

70:     function safeIncreaseAllowance(

86:     function safeDecreaseAllowance(

109:     function safePermit(

```

```solidity
File: contracts/openzeppelin/utils/Address.sol

60:     function sendValue(address payable recipient, uint256 amount) internal {

91:     function functionCall(address target, bytes memory data)

110:     function functionCall(

280:     function verifyCallResult(

```

---
## Exclusive Findings
---
### Gas Optimizations Report
| No. | Issue |
| --- | --- |
| 1 | [Splitting `require` which contains `&&` saves gas](#G-01-splitting-require-which-contains--saves-gas)|
| 2 | [Reduce the size of error messages (Long revert Strings)](#G-02-reduce-the-size-of-error-messages-long-revert-strings)|
| 3 | [Use Custom Errors rather than revert() / require() strings to save deployment gas [68 gas per instance]](#G-03-use-custom-errors-rather-than-revert--require-strings-to-save-deployment-gas-68-gas-per-instance)|
| 4 | [Usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#G-04-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead)|
| 5 | [++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops](#G-05-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for-loop-and-while-loops)|
| 6 | [Optimize names to save gas [22 gas per instance]](#G-06-optimize-names-to-save-gas-22-gas-per-instance)|
| 7 | [Comparison operators](#G-07-comparison-operators)|
| 8 | [Use a more recent version of solidity](#G-08-use-a-more-recent-version-of-solidity)|
| 9 | [`<array>.length` should not be looked up in every loop of a for-loop](#G-09-arraylength-should-not-be-looked-up-in-every-loop-of-a-for-loop)|
| 10 | [`x += y` costs more gas than `x = x + y` for state variables](#G-10-x--y-costs-more-gas-than-x--x--y-for-state-variables)|
---
### [G-01] Splitting `require` which contains `&&` saves gas

---
**Description:**

Using multiple require instead of operator && can save more gas. When a require statement has 2 or more expressions with &&, separate them into individual expression results in lesser gas.

**Lines of Code:** 

- [ContractDeployer.sol#L72-L76](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L72-L76)
- [KnownCodesStorage.sol#L128](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol#L128)

---
### [G-02] Reduce the size of error messages (Long revert Strings)

---
**Description:**

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition has been met. 

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

**Recommendation:**

Shorten the revert strings to fit in 32 bytes. 

Or in contracts using solc version 0.8.4 or greater use the Custom Errors feature.

**Lines of Code:** 

- [AccountCodeStorage.sol#L25](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol#L25)
- [AccountCodeStorage.sol#L36](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol#L36)
- [AccountCodeStorage.sol#L50](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol#L50)
- [BytecodeCompressor.sol#L42](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol#L42)
- [BytecodeCompressor.sol#L46](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol#L46)
- [BytecodeCompressor.sol#L51](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol#L51)
- [BytecodeCompressor.sol#L56](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol#L56)
- [ContractDeployer.sol#L75](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L75)
- [ContractDeployer.sol#L233](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L233)
- [ContractDeployer.sol#L242](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L242)
- [ContractDeployer.sol#L256](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L256)
- [DefaultAccount.sol#L103](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L103)
- [DefaultAccount.sol#L205](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L205)
- [ImmutableSimulator.sol#L34](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ImmutableSimulator.sol#L34)
- [KnownCodesStorage.sol#L24](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol#L24)
- [KnownCodesStorage.sol#L128](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol#L128)
- [L2EthToken.sol#L45](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L45)
- [NonceHolder.sol#L65](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L65)
- [NonceHolder.sol#L135](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L135)
- [SystemContext.sol#L117](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L117)
- [SystemContractHelper.sol#L310](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L310)
- [SystemContractHelper.sol#L342](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L342)
- [TransactionHelper.sol#L362](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L362)
- [TransactionHelper.sol#L368](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L368)
- [SafeERC20.sol#L62](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L62)
- [SafeERC20.sol#L95](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L95)
- [SafeERC20.sol#L124](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L124)
- [SafeERC20.sol#L147](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L147)
- [Address.sol#L69](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/utils/Address.sol#L69)
- [Address.sol#L157](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/utils/Address.sol#L157)

---
### [G-03] Use Custom Errors rather than revert() / require() strings to save deployment gas [68 gas per instance]

---
**Description:**

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.

<https://blog.soliditylang.org/2021/04/21/custom-errors/>

**Recommendation:**

Use the Custom Errors feature.

**Lines of Code:** 

- [AccountCodeStorage.sol#L25](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol#L25)
- [AccountCodeStorage.sol#L36](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol#L36)
- [AccountCodeStorage.sol#L50](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol#L50)
- [BootloaderUtilities.sol#L91](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L91)
- [BootloaderUtilities.sol#L190](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L190)
- [BootloaderUtilities.sol#L285](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L285)
- [BytecodeCompressor.sol#L42](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol#L42)
- [BytecodeCompressor.sol#L43](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol#L43)
- [BytecodeCompressor.sol#L44-L47](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol#L44-L47)
- [BytecodeCompressor.sol#L51](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol#L51)
- [BytecodeCompressor.sol#L56](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol#L56)
- [ContractDeployer.sol#L27](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L27)
- [ContractDeployer.sol#L72-L76](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L72-L76)
- [ContractDeployer.sol#L233](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L233)
- [ContractDeployer.sol#L242](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L242)
- [ContractDeployer.sol#L255](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L255)
- [ContractDeployer.sol#L256](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L256)
- [ContractDeployer.sol#L259-L262](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L259-L262)
- [ContractDeployer.sol#L264](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L264)
- [ContractDeployer.sol#L296](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L296)
- [DefaultAccount.sol#L96](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L96)
- [DefaultAccount.sol#L103](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L103)
- [DefaultAccount.sol#L163](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L163)
- [DefaultAccount.sol#L176](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L176)
- [DefaultAccount.sol#L187](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L187)
- [DefaultAccount.sol#L205](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L205)
- [ImmutableSimulator.sol#L34](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ImmutableSimulator.sol#L34)
- [KnownCodesStorage.sol#L19](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol#L19)
- [KnownCodesStorage.sol#L24](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol#L24)
- [KnownCodesStorage.sol#L112](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol#L112)
- [KnownCodesStorage.sol#L128](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol#L128)
- [KnownCodesStorage.sol#L130](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol#L130)
- [L2EthToken.sol#L29](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L29)
- [L2EthToken.sol#L41-L46](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L41-L46)
- [L2EthToken.sol#L49](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L49)
- [NonceHolder.sol#L65](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L65)
- [NonceHolder.sol#L84](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L84)
- [NonceHolder.sol#L88](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L88)
- [NonceHolder.sol#L114](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L114)
- [NonceHolder.sol#L135](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L135)
- [SystemContext.sol#L116](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L116)
- [SystemContext.sol#L117](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L117)
- [EfficientCall.sol#L37](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/EfficientCall.sol#L37)
- [EfficientCall.sol#L46](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/EfficientCall.sol#L46)
- [SystemContractHelper.sol#L310](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L310)
- [SystemContractHelper.sol#L340-L343](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L340-L343)
- [TransactionHelper.sol#L362](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L362)
- [TransactionHelper.sol#L366-L369](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L366-L369)
- [Utils.sol#L20](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/Utils.sol#L20)
- [Utils.sol#L26](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/Utils.sol#L26)
- [Utils.sol#L32](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/Utils.sol#L32)
- [Utils.sol#L78](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/Utils.sol#L78)
- [Utils.sol#L81](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/Utils.sol#L81)
- [Utils.sol#L82](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/Utils.sol#L82)
- [SafeERC20.sol#L60-L63](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L60-L63)
- [SafeERC20.sol#L93-L96](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L93-L96)
- [SafeERC20.sol#L122-L125](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L122-L125)
- [SafeERC20.sol#L145-L148](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L145-L148)
- [Address.sol#L61-L64](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/utils/Address.sol#L61-L64)
- [Address.sol#L67-L70](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/utils/Address.sol#L67-L70)
- [Address.sol#L155-L158](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/utils/Address.sol#L155-L158)
- [Address.sol#L266](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/utils/Address.sol#L266)

---
### [G-04] Usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

---
**Description:**

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so.

**Recommendation:**

Use a larger size then downcast where needed

**Lines of Code:** 

- [BootloaderUtilities.sol#L67](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L67)
- [BootloaderUtilities.sol#L163](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L163)
- [BootloaderUtilities.sol#L258](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L258)
- [BytecodeCompressor.sol#L53](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol#L53)
- [BytecodeCompressor.sol#L54](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol#L54)
- [Constants.sol#L18](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/Constants.sol#L18)
- [Constants.sol#L22](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/Constants.sol#L22)
- [DefaultAccount.sol#L142](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L142)
- [DefaultAccount.sol#L144](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L144)
- [DefaultAccount.sol#L164](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L164)
- [KnownCodesStorage.sol#L107](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol#L107)
- [KnownCodesStorage.sol#L127](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol#L127)
- [L1Messenger.sol#L27](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L1Messenger.sol#L27)
- [IMailbox.sol#L9](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IMailbox.sol#L9)
- [EfficientCall.sol#L262](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/EfficientCall.sol#L262)
- [EfficientCall.sol#L266](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/EfficientCall.sol#L266)
- [RLPEncoder.sol#L44](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/RLPEncoder.sol#L44)
- [RLPEncoder.sol#L51](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/RLPEncoder.sol#L51)
- [RLPEncoder.sol#L55](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/RLPEncoder.sol#L55)
- [SystemContractHelper.sol#L17](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L17)
- [SystemContractHelper.sol#L18](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L18)
- [SystemContractHelper.sol#L19](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L19)
- [SystemContractHelper.sol#L20](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L20)
- [SystemContractHelper.sol#L21](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L21)
- [SystemContractHelper.sol#L22](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L22)
- [SystemContractHelper.sol#L94](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L94)
- [SystemContractHelper.sol#L106](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L106)
- [SystemContractHelper.sol#L125](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L125)
- [SystemContractHelper.sol#L126](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L126)
- [SystemContractHelper.sol#L127](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L127)
- [SystemContractHelper.sol#L128](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L128)
- [SystemContractHelper.sol#L129](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L129)
- [SystemContractHelper.sol#L147](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L147)
- [SystemContractHelper.sol#L164](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L164)
- [SystemContractHelper.sol#L221](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L221)
- [SystemContractHelper.sol#L231](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L231)
- [SystemContractHelper.sol#L240](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L240)
- [SystemContractHelper.sol#L248](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L248)
- [SystemContractHelper.sol#L257](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L257)
- [SystemContractHelper.sol#L266](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L266)
- [SystemContractsCaller.sol#L75](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L75)
- [SystemContractsCaller.sol#L78](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L78)
- [SystemContractsCaller.sol#L82](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L82)
- [SystemContractsCaller.sol#L123](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L123)
- [SystemContractsCaller.sol#L125](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L125)
- [SystemContractsCaller.sol#L150](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L150)
- [SystemContractsCaller.sol#L152](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L152)
- [SystemContractsCaller.sol#L214](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L214)
- [SystemContractsCaller.sol#L215](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L215)
- [SystemContractsCaller.sol#L216](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L216)
- [SystemContractsCaller.sol#L217](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L217)
- [SystemContractsCaller.sol#L218](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L218)
- [SystemContractsCaller.sol#L219](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L219)
- [SystemContractsCaller.sol#L250](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L250)
- [SystemContractsCaller.sol#L251](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L251)
- [TransactionHelper.sol#L15](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L15)
- [TransactionHelper.sol#L18](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L18)
- [TransactionHelper.sol#L20](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L20)
- [TransactionHelper.sol#L22](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L22)
- [TransactionHelper.sol#L170](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L170)
- [TransactionHelper.sol#L248](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L248)
- [TransactionHelper.sol#L320](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L320)
- [UnsafeBytesCalldata.sol#L18](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/UnsafeBytesCalldata.sol#L18)
- [UnsafeBytesCalldata.sol#L25](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/UnsafeBytesCalldata.sol#L25)
- [IERC20Permit.sol#L41](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol#L41)
- [SafeERC20.sol#L115](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L115)

---
### [G-05] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops

---
**Description:**

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

**Recommendation:**

Using Solidity's unchecked block saves the overflow checks.

**Proof Of Concept:**

<https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g011---unnecessary-checked-arithmetic-in-for-loop>

**Lines of Code:** 

- [ContractDeployer.sol#L239](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L239)
- [ContractDeployer.sol#L244](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L244)
- [ImmutableSimulator.sol#L37](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ImmutableSimulator.sol#L37)
- [KnownCodesStorage.sol#L34](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol#L34)

---
### [G-06] Optimize names to save gas [22 gas per instance]

---
**Description:**

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions.

**Recommendation:**

Find a lower `method ID` name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas. For example, the function IDs in the L1GraphTokenGateway.sol contract will be the most used; A lower method ID may be given.

**Proof Of Concept:**

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

**Lines of Code:** 

- [AccountCodeStorage.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol)
- [BootloaderUtilities.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol)
- [BytecodeCompressor.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol)
- [Constants.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/Constants.sol)
- [ContractDeployer.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol)
- [DefaultAccount.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol)
- [EmptyContract.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/EmptyContract.sol)
- [ImmutableSimulator.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ImmutableSimulator.sol)
- [KnownCodesStorage.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol)
- [L1Messenger.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L1Messenger.sol)
- [L2EthToken.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol)
- [MsgValueSimulator.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/MsgValueSimulator.sol)
- [NonceHolder.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol)
- [SystemContext.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol)
- [IAccount.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IAccount.sol)
- [IAccountCodeStorage.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IAccountCodeStorage.sol)
- [IBootloaderUtilities.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IBootloaderUtilities.sol)
- [IBytecodeCompressor.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IBytecodeCompressor.sol)
- [IContractDeployer.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IContractDeployer.sol)
- [IEthToken.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IEthToken.sol)
- [IImmutableSimulator.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IImmutableSimulator.sol)
- [IKnownCodesStorage.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IKnownCodesStorage.sol)
- [IL1Messenger.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IL1Messenger.sol)
- [IL2StandardToken.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IL2StandardToken.sol)
- [IMailbox.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IMailbox.sol)
- [INonceHolder.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/INonceHolder.sol)
- [IPaymaster.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IPaymaster.sol)
- [IPaymasterFlow.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IPaymasterFlow.sol)
- [ISystemContext.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/ISystemContext.sol)
- [EfficientCall.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/EfficientCall.sol)
- [RLPEncoder.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/RLPEncoder.sol)
- [SystemContractHelper.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol)
- [SystemContractsCaller.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol)
- [TransactionHelper.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol)
- [UnsafeBytesCalldata.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/UnsafeBytesCalldata.sol)
- [Utils.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/Utils.sol)
- [IERC20.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/IERC20.sol)
- [IERC20Permit.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol)
- [SafeERC20.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol)
- [Address.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/utils/Address.sol)

---
### [G-07] Comparison operators

---
**Description:**

In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `>` and `=`. Using strict comparison operators hence saves gas.

**Recommendation:**

 Replace `<=` with `<`, and `>=` with `>`. Do not forget to increment/decrement the compared variable.

**Lines of Code:** 

- [AccountCodeStorage.sol#L78](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol#L78)
- [BootloaderUtilities.sol#L71](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L71)
- [BootloaderUtilities.sol#L167](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L167)
- [BootloaderUtilities.sol#L262](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L262)
- [BytecodeCompressor.sol#L43](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol#L43)
- [DefaultAccount.sol#L96](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L96)
- [DefaultAccount.sol#L103](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L103)
- [DefaultAccount.sol#L187](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L187)
- [L2EthToken.sol#L49](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L49)
- [NonceHolder.sol#L65](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L65)
- [SystemContext.sol#L116](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L116)
- [SystemContractHelper.sol#L152](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L152)
- [SystemContractHelper.sol#L330](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L330)
- [TransactionHelper.sol#L174](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L174)
- [TransactionHelper.sol#L252](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L252)
- [TransactionHelper.sol#L324](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L324)
- [TransactionHelper.sol#L362](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L362)
- [TransactionHelper.sol#L367](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L367)
- [Utils.sol#L20](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/Utils.sol#L20)
- [Utils.sol#L26](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/Utils.sol#L26)
- [Utils.sol#L32](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/Utils.sol#L32)
- [SafeERC20.sol#L94](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L94)
- [Address.sol#L62](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/utils/Address.sol#L62)
- [Address.sol#L156](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/utils/Address.sol#L156)

---
### [G-08] Use a more recent version of solidity

---
**Description:**

Solidity 0.8.10 has a useful change that reduced gas costs of external calls which expect a return value.

In 0.8.15 the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller.

In 0.8.17 prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. More efficient overflow checks for multiplication.

**Lines of Code:** 

- [AccountCodeStorage.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol)
- [BootloaderUtilities.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol)
- [BytecodeCompressor.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol)
- [Constants.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/Constants.sol)
- [ContractDeployer.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol)
- [DefaultAccount.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol)
- [EmptyContract.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/EmptyContract.sol)
- [ImmutableSimulator.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ImmutableSimulator.sol)
- [KnownCodesStorage.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol)
- [L1Messenger.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L1Messenger.sol)
- [L2EthToken.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol)
- [MsgValueSimulator.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/MsgValueSimulator.sol)
- [NonceHolder.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol)
- [SystemContext.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol)
- [IAccount.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IAccount.sol)
- [IAccountCodeStorage.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IAccountCodeStorage.sol)
- [IBootloaderUtilities.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IBootloaderUtilities.sol)
- [IBytecodeCompressor.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IBytecodeCompressor.sol)
- [IContractDeployer.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IContractDeployer.sol)
- [IEthToken.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IEthToken.sol)
- [IImmutableSimulator.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IImmutableSimulator.sol)
- [IKnownCodesStorage.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IKnownCodesStorage.sol)
- [IL1Messenger.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IL1Messenger.sol)
- [IL2StandardToken.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IL2StandardToken.sol)
- [IMailbox.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IMailbox.sol)
- [INonceHolder.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/INonceHolder.sol)
- [IPaymaster.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IPaymaster.sol)
- [IPaymasterFlow.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IPaymasterFlow.sol)
- [ISystemContext.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/ISystemContext.sol)
- [EfficientCall.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/EfficientCall.sol)
- [RLPEncoder.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/RLPEncoder.sol)
- [SystemContractHelper.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol)
- [SystemContractsCaller.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol)
- [TransactionHelper.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol)
- [UnsafeBytesCalldata.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/UnsafeBytesCalldata.sol)
- [Utils.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/Utils.sol)
- [IERC20.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/IERC20.sol)
- [IERC20Permit.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol)
- [SafeERC20.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol)
- [Address.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/utils/Address.sol)

---
### [G-09] `<array>.length` should not be looked up in every loop of a for-loop

---
**Description:**

The overheads outlined below are *PER LOOP*, excluding the first loop 

- storage arrays incur a Gwarmaccess (100 gas)

- memory arrays use `MLOAD` (3 gas)

- calldata arrays use `CALLDATALOAD` (3 gas)

Caching the length changes each of these to a `DUP<N>` (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset



**Lines of Code:** 

- [BytecodeCompressor.sol#L49](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol#L49)

---
### [G-10] `x += y` costs more gas than `x = x + y` for state variables

---
**Lines of Code:** 

- [L2EthToken.sol#L54](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L54)
- [L2EthToken.sol#L73](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L73)
- [L2EthToken.sol#L74](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L74)
- [L2EthToken.sol#L85](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L85)
- [L2EthToken.sol#L86](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L86)