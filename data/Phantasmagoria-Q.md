### L-1 Empty Function Body
Empty blocks should emitting an event or reverting.
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L230 

### L-2 Outdated OpenZeppelin version
The project is using OpenZeppelin v4.6.0 and v4.4.1. Consider updating to the latest version to ensure the library contracts contain the latest security fixes.
```
File: contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol

2: // OpenZeppelin Contracts v4.4.1 (token/ERC20/extensions/IERC20Permit.sol)
```
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol#L2
```
File: contracts/openzeppelin/token/ERC20/IERC20.sol

2: // OpenZeppelin Contracts (last updated v4.6.0) (token/ERC20/IERC20.sol)
```
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/IERC20.sol#L2

### L-3 Floating pragma 
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
Instances: All contracts

### NC-1 404 Links
These links do not work

https://eips.ethereum.org/EIPS/eip-2612[EIP-2612]
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol#L8

https://eips.ethereum.org/EIPS/eip-1884[EIP1884]
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/utils/Address.sol#L48

### NC-2 Big numbers should use _    
Consider using underscores for number values to improve readability.
```
File: contracts/SystemContext.sol

40: uint256 public difficulty = 2500000000000000;
```
https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L40

### NC-3 Use require() instead of assert()
The Solidity assert() function is meant to assert invariants. Properly functioning code should never reach a failing assert statement.
```
File: contracts/libraries/RLPEncoder.sol

45: assert(_len != 1);
```
https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/RLPEncoder.sol#L45 
 
```
File: contracts/DefaultAccount.sol

225: assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);
```
https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L225    

### NC-4 NatSpec is incomplete
Consider adding NATSPEC on all public/external functions
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IContractDeployer.sol#L43-L63
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IAccount.sol#L26-L47
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/ISystemContext.sol#L11-L29
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IAccountCodeStorage.sol#L6-L14

### NC-5 Unnecessary return statement applied
Delete return
```
File: contracts/NonceHolder.sol

128: return deploymentNonce;
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L128


### NC-6 Add a limit for the maximum number of characters per line
Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length.
https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L7
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L85

### NC-7 Use of bytes.concat() instead of abi.encodePacked() for appending bytes
Since version 0.8.4, bytes.concat()can be used for appending bytes
```
File: contracts/libraries/TransactionHelper.sol

141: return keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L141

### NC-8 Include @param and @return parameters in NatSpec comments
Some code analysis programs do analysis by reading NatSpec details, if they can't see the tags (@param, @dev, @return), they do incomplete analysis. So 
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/INonceHolder.sol#L17-L47

### NC-9 Follow a standard convertion function names such as using get for getter (view/pure) functions
Follow a standard convertion function names such as using get for getter (view/pure) functions. Clear function names can increase readability.
```
File: contracts/libraries/Utils.sol

38: function bytecodeLenInBytes(bytes32 _bytecodeHash) internal pure returns (uint256 codeLength) {
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/Utils.sol#L38
```
File: contracts/libraries/Utils.sol

43: function bytecodeLenInWords(bytes32 _bytecodeHash) internal pure returns (uint256 codeLengthInWords) {
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/Utils.sol#L43
```
File: contracts/libraries/Utils.sol

65: function constructedBytecodeHash(bytes32 _bytecodeHash) internal pure returns (bytes32) {
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/Utils.sol#L65
```
File: contracts/libraries/Utils.sol

76: function hashL2Bytecode(bytes calldata _bytecode) internal view returns (bytes32 hashedBytecode) {
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/Utils.sol#L76
```
File: contracts/ContractDeployer.sol

38: function extendedAccountVersion(address _address) public view returns (AccountAbstractionVersion) {
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L38

### NC-10 Add _ to internal/private functions
It is a common pattern to prefix internal/private function names with _
To improve code readability add _ to internal/private functions

```
File: contracts/libraries/Utils.sol
19: function safeCastToU128(uint256 _x) internal pure returns (uint128) {
25: function safeCastToU32(uint256 _x) internal pure returns (uint32) {
31: function safeCastToU24(uint256 _x) internal pure returns (uint24) {
38: function bytecodeLenInBytes(bytes32 _bytecodeHash) internal pure returns (uint256 codeLength) {
43: function bytecodeLenInWords(bytes32 _bytecodeHash) internal pure returns (uint256 codeLengthInWords){
50: function isContractConstructing(bytes32 _bytecodeHash) internal pure returns (bool) {
57: function constructingBytecodeHash(bytes32 _bytecodeHash) internal pure returns (bytes32) {
65: function constructedBytecodeHash(bytes32 _bytecodeHash) internal pure returns (bytes32) {
76: function hashL2Bytecode(bytes calldata _bytecode) internal view returns (bytes32 hashedBytecode) {
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/Utils.sol
```
File: contracts/libraries/SystemContractHelper.sol

48: function toL1(bool _isService, bytes32 _key, bytes32 _value) internal {
63: function getCodeAddress() internal view returns (address addr) {
74: function loadCalldataIntoActivePtr() internal view {
85: function ptrPackIntoActivePtr(uint256 _farCallAbi) internal view {
94: function ptrAddIntoActive(uint32 _value) internal view {
106: function ptrShrinkIntoActive(uint32 _shrink) internal view {
147: function precompileCall(uint256 _rawParams, uint32 _gasToBurn) internal view returns (bool success) {
164: function setValueForNextFarCall(uint128 _value) internal returns (bool success) {
177: function eventInitialize(uint256 initializer, uint256 value1) internal {
187: function eventWrite(uint256 value1, uint256 value2) internal {
198: function getZkSyncMetaBytes() internal view returns (uint256 meta) {
210: function extractNumberFromMeta(uint256 meta, uint256 offset, uint256 size) internal pure returns (uint256 result) {
221: function getGasPerPubdataByteFromMeta(uint256 meta) internal pure returns (uint32 gasPerPubdataByte) {
231: function getHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 heapSize) {
240: function getAuxHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 auxHeapSize) {
248: function getShardIdFromMeta(uint256 meta) internal pure returns (uint8 shardId) {
257: function getCallerShardIdFromMeta(uint256 meta) internal pure returns (uint8 callerShardId) {
266: function getCodeShardIdFromMeta(uint256 meta) internal pure returns (uint8 codeShardId) {
272: function getZkSyncMeta() internal view returns (ZkSyncMeta memory meta) {
287: function getCallFlags() internal view returns (uint256 callFlags) {
298: function getCalldataPtr() internal view returns (uint256 ptr) {
309: function getExtraAbiData(uint256 index) internal view returns (uint256 extraAbiData) {
320: function isSystemCall() internal view returns (bool) {
329: function isSystemContract(address _address) internal pure returns (bool) {
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol
```
File: contracts/libraries/RLPEncoder.sol

6: function encodeAddress(address _val) internal pure returns (bytes memory encoded) {
19: function encodeUint256(uint256 _val) internal pure returns (bytes memory encoded) {
44: function encodeNonSingleBytesLen(uint64 _len) internal pure returns (bytes memory) {
51: function encodeListLen(uint64 _len) internal pure returns (bytes memory) {
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/RLPEncoder.sol

### NC-11 Consider importing interfaces first, then all libraries, then all utils.
```
File: contracts/L2EthToken.sol

5: import {IEthToken} from "./interfaces/IEthToken.sol";
6: import {MSG_VALUE_SYSTEM_CONTRACT, DEPLOYER_SYSTEM_CONTRACT, BOOTLOADER_FORMAL_ADDRESS, L1_MESSENGER_CONTRACT} from "./Constants.sol";
7: import {SystemContractHelper} from "./libraries/SystemContractHelper.sol";
8: import {IMailbox} from "./interfaces/IMailbox.sol";
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L5-L8

### NC-12 Assembly code should have comments
Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does.

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.
```
File: contracts/libraries/EfficientCall.sol

255: assembly {
256:     dataOffset := _data.offset
257: });
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol#L255-L257
```
File: contracts/libraries/SystemContractsCaller.sol

79: assembly {
80:     dataStart := add(data, 0x20)
81: }
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractsCaller.sol#L79-L81
```
File: contracts/libraries/EfficientCall.sol

213: assembly {
214:     size := returndatasize()
215: }
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol#L213-L215
```
File: contracts/libraries/EfficientCall.sol

218: assembly {
219:     returndatacopy(add(returnData, 0x20), 0, size)
220: }
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol#L218-L220
```
File: contracts/libraries/EfficientCall.sol

255: assembly {
256:     dataOffset := _data.offset
257: }
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol#L255-L257

### NC-13 Return variables can be used
Use defined named return variables like this
Before:
```
File: contracts/ContractDeployer.sol

32: function getAccountInfo(address _address) external view returns (AccountInfo memory info) {
33:     return _accountInfo[_address];
34: }
```
After:
```
function getAccountInfo(address _address) external view returns (AccountInfo memory info) {
    info = _accountInfo[_address];
}
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L32
Before:
```
File: contracts/BootloaderUtilities.sol

...
118: return
119:             keccak256(
120:                 bytes.concat(
121:                     encodedListLength,
122:                     encodedNonce,
123:                     encodedGasParam,
124:                     encodedTo,
125:                     encodedValue,
126:                     encodedDataLength,
127:                     _transaction.data,
128:                     vEncoded,
129:                     rEncoded,
130:                     sEncoded
131:                 )
132:             );
```
After:
```
...
txHash = keccak256(
                bytes.concat(
                    encodedListLength,
                    encodedNonce,
                    encodedGasParam,
                    encodedTo,
                    encodedValue,
                    encodedDataLength,
                    _transaction.data,
                    vEncoded,
                    rEncoded,
                    sEncoded
                )
            );
```
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BootloaderUtilities.sol#L43







