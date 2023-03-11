### Issues Template
| Letter | Name | Description |
|:--:|:-------:|:-------:|
| L  | Low risk | Potential risk |
| NC |  Non-critical | Non risky findings |
| R  | Refactor | Code changes |
| O | Ordinary | Commonly found issues |

| Total Found Issues | 20 |
|:--:|:--:|

### Non-Critical
| Count | Title | Instances |
|:--:|:-------|:--:|
| [N-01] | For mordern and more readable code, update import usages | 52 |
| [N-02] | Constant values such as a call to keccak256(), should use immutable rather than constant | 2 |
| [N-03] | Omission of important parameters in events emitted | 2 |
| [N-04] | Missing event for critical parameters and change | 4 |

| Total Non-Critical Issues | 4 |
|:--:|:--:|

### Refactor Issues
| Count | Title | Instances |
|:--:|:-------|:--:|
| [R-01] | Unecessary initialization of named return variable  | 1 |
| [R-02] | Unnecessary return statements in functions that have already define named return variable(s) | 1 |
| [R-03] | Use underscores _ for number literals | 1 |
| [R-04] | Move deployer address checks to modifier  | 2 |
| [R-05] | Use nested `if` statements to avoid multiple check combinations using `&&` | 4 |
| [R-06] | Split require statements to avoid multiple check combinations using `&&` | 2 |
| [R-07] | Use ternary operators to shorten if/else statements | 2 |
| [R-08] | No need to explicitly upcast `uint` variables | 16 |
| [R-09] | Use `type<uint>.max` instead of exponentiation | 1 |
| [R-10] | Remove unused code | 1 |

| Total Refactor Issues | 10 |
|:--:|:--:|

### Ordinary Issues
| Count | Explanation | Instances |
|:--:|:-------|:--:|
| [O-01] | Unlocked pragma | All |
| [O-02] | NatSpec Comments should be increased in contracts | All |
| [O-03] | Include return parameters in NatSpec comments | 49 |
| [O-04] | Function naming suggestion of internal and private functions starts with _ | 81 |
| [O-05] | Contract layout that does not comply with solidity style guide | 1 |
| [O-06] | Order of function does not comply with the solidity style guide | 8 |

| Total Ordinary Issues | 6 |
|:--:|:--:|

### [N-01] For mordern and more readable code, update import usages
Context:
```solidity
52 results - 17 files

/ImmutableSimulator.sol
5: import "./interfaces/IImmutableSimulator.sol";

/L1Messenger.sol
5: import "./interfaces/IL1Messenger.sol";
6: import "./libraries/SystemContractHelper.sol";
7: import "./libraries/EfficientCall.sol";

/MsgValueSimulator.sol
5: import "./libraries/EfficientCall.sol";

/BytecodeCompressor.sol
5: import "./interfaces/IBytecodeCompressor.sol";
6: import "./Constants.sol";
7: import "./libraries/Utils.sol";
8: import "./libraries/UnsafeBytesCalldata.sol";

/AccountCodeStorage.sol
5: import "./interfaces/IAccountCodeStorage.sol";
6: import "./libraries/Utils.sol";

/KnownCodesStorage.sol
5: import "./interfaces/IKnownCodesStorage.sol";
6: import "./libraries/Utils.sol";
7: import "./libraries/SystemContractHelper.sol";

/NonceHolder.sol
5: import "./interfaces/INonceHolder.sol";
6: import "./interfaces/IContractDeployer.sol";

/DefaultAccount.sol
5: import "./interfaces/IAccount.sol";
6: import "./libraries/TransactionHelper.sol";
7: import "./libraries/SystemContractHelper.sol";
8: import "./libraries/EfficientCall.sol";

/ContractDeployer.sol
 6: import "./interfaces/IContractDeployer.sol";
 9: import "./libraries/Utils.sol";
10: import "./libraries/EfficientCall.sol";

/BootloaderUtilities.sol
5: import "./interfaces/IBootloaderUtilities.sol";
6: import "./libraries/TransactionHelper.sol";
7: import "./libraries/RLPEncoder.sol";
8: import "./libraries/EfficientCall.sol";

/SystemContractHelper.sol
8: import "./SystemContractsCaller.sol";
9: import "./Utils.sol";

/Utils.sol
4: import "./EfficientCall.sol";

/SafeERC20.sol
6: import "../IERC20.sol";
7: import "../extensions/IERC20Permit.sol";
8: import "../../../utils/Address.sol";

/SystemContractsCaller.sol
6: import "./Utils.sol";

/EfficientCall.sol
5: import "./SystemContractHelper.sol";
6: import "./Utils.sol";

/TransactionHelper.sol
 5: import "../openzeppelin/token/ERC20/IERC20.sol";
 6: import "../openzeppelin/token/ERC20/utils/SafeERC20.sol";
 8: import "../interfaces/IPaymasterFlow.sol";
 9: import "../interfaces/IContractDeployer.sol";
11: import "./RLPEncoder.sol";
12: import "./EfficientCall.sol";

/Constants.sol
 5: import "./interfaces/IAccountCodeStorage.sol";
 6: import "./interfaces/INonceHolder.sol";
 7: import "./interfaces/IContractDeployer.sol";
 8: import "./interfaces/IKnownCodesStorage.sol";
 9: import "./interfaces/IImmutableSimulator.sol";
10: import "./interfaces/IEthToken.sol";
11: import "./interfaces/IL1Messenger.sol";
12: import "./interfaces/ISystemContext.sol";
13: import "./interfaces/IBytecodeCompressor.sol";
14: import "./BootloaderUtilities.sol";
```

Description:
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct polluted the source code with an unnecessary object we were not using because we did not need it.
This was breaking the rule of modularity and modular programming: only import what you need Specific imports with curly braces allow us to apply this rule better.
https://betterprogramming.pub/solidity-tutorial-all-about-imports-c65110e41f3a

Recommendation:
 `import {contract1 , contract2} from "filename.sol"`; 

### [N-02] Constant values such as a call to keccak256(), should use immutable rather than constant
```solidity
2 results - 1 file

/TransactionHelper.sol
81: bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");
84: bytes32 constant EIP712_TRANSACTION_TYPE_HASH =
85:     keccak256(
86:         "Transaction(uint256 txType,uint256 from,uint256 to,uint256 gasLimit,uint256 gasPerPubdataByteLimit,uint256 maxFeePerGas,uint256 maxPriorityFeePerGas,uint256 paymaster,uint256 nonce,uint256 value,bytes data,bytes32[] factoryDeps,bytes paymasterInput)"
87:          );
```

Description:
There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.

Recommendation:
Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.



### [N-03] Omission of important parameters in events emitted
```solidity
2 results - 1 file

/ContractDeployer.sol
60:    function updateAccountVersion(AccountAbstractionVersion _version) external onlySystemCall {
61:        _accountInfo[msg.sender].supportedAAVersion = _version;
62:
63:        emit AccountVersionUpdated(msg.sender, _version);
64:    }

69:    function updateNonceOrdering(AccountNonceOrdering _nonceOrdering) external onlySystemCall {
70:        AccountInfo memory currentInfo = _accountInfo[msg.sender];
71:
72:        require(
73:            _nonceOrdering == AccountNonceOrdering.Arbitrary &&
74:                currentInfo.nonceOrdering == AccountNonceOrdering.Sequential,
75:            "It is only possible to change from sequential to arbitrary ordering"
76:        );
77:
78:        currentInfo.nonceOrdering = _nonceOrdering;
79:        _storeAccountInfo(msg.sender, currentInfo);
80:
81:        emit AccountNonceOrderingUpdated(msg.sender, _nonceOrdering);
82:    }
```

Description:
Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters.

Recommendation:
The events should include the new value and old value where possible

### [N-04] Missing event for critical parameters and change
```solidity
4 results - 3 files

/ImmutableSimulator.sol
 33: function setImmutables(address _dest, ImmutableData[] calldata _immutables) external override

/SystemContext.sol
 60: function setTxOrigin(address _newOrigin) external onlyBootloader
 66: function setGasPrice(uint256 _gasPrice) external onlyBootloader

109:    function setNewBlock(
110:        bytes32 _prevBlockHash,
111:        uint256 _newTimestamp,
112:        uint256 _expectedNewNumber,
113:        uint256 _baseFee
114:    ) external onlyBootloader

/SystemContractHelper.sol
164: function setValueForNextFarCall(uint128 _value) internal returns (bool success)
```

Description:
Events help non-contract tools to track changes, and events prevent users from being surprised by changes


Recommendation:
Add Event-Emit

### [R-01] Unecessary initialization of named return variable 
Context:

Some functions declared a named return variable but the variable is not used elsewhere:
```solidity
1 result - 1 file

/ContractDeployer.sol
32:    function getAccountInfo(address _address) external view returns (AccountInfo memory info) {
33:        return _accountInfo[_address];
34:    }
```
Reccomendation:
Consider returning an unnamed variable

### [R-02] Unnecessary return statements in functions that have already define named return variable(s)
Context:

Some functions declared a named return variable so the return statement is not necessary:
```solidity
1 result - 1 file

/NonceHolder.sol
124:    function getDeploymentNonce(address _address) external view returns (uint256 deploymentNonce) {
125:        uint256 addressAsKey = uint256(uint160(_address));
126:        (deploymentNonce, ) = _splitRawNonce(rawNonces[addressAsKey]);
127:
128:        return deploymentNonce;
129:    }
```

Recommendation:
Remove `return` statements for functions that have already define named return variable(s)


### [R-03] Use underscores _ for number literals
Context:
```solidity
1 result - 1 file

/SystemContext.sol
40: uint256 public difficulty = 2500000000000000;
```

Description:
There are occasions where certain numbers have been hardcoded, either in variable or in the code itself. Large numbers can become hard to read.

Recommendation:
Consider using underscores for number literals to improve its readability.


### [R-04] Move deployer address checks to modifier
```solidity
2 results - 2 files

/ImmutableSimulator.sol
33:    function setImmutables(address _dest, ImmutableData[] calldata _immutables) external override {
34:        require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

/NonceHolder.sol
134:    function incrementDeploymentNonce(address _address) external onlySystemCall returns (uint256 prevDeploymentNonce) {
135:        require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "");
```

Recommendation:
Use a modifier consistently similar to other contracts for deployer address checks to improve readability

Example in `AccountCodeStorage.sol`:
```solidity
24:    modifier onlyDeployer() {
25:        require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");
26:        _;
27:    }
```

### [R-05] Use nested `if` statements to avoid multiple check combinations using `&&`
Context:
```solidity
4 results - 2 files

/AccountCodeStorage.sol
74:    function getCodeHash(uint256 _input) external view override returns (bytes32)
87:        if (codeHash == 0x00 && NONCE_HOLDER_SYSTEM_CONTRACT.getRawNonce(account) > 0)

102:    function getCodeSize(uint256 _input) external view override returns (uint256 codeSize)
116:        if (
117:            uint160(account) > CURRENT_MAX_PRECOMPILE_ADDRESS &&
118:            codeHash != 0x00 &&
119:            !Utils.isContractConstructing(codeHash)

/NonceHolder.sol
81:    function setValueUnderNonce(uint256 _key, uint256 _value) public onlySystemCall 
87:        if (accountInfo.nonceOrdering == IContractDeployer.AccountNonceOrdering.Sequential && _key != 0)
```

Recommendation:
Replacing `&&` used within `if` statements with nested `if` statements can help improve readability and even saves gas

### [R-06] Split require statements to avoid multiple check combinations using `&&`
```solidity
2 results - 2 files

/KnownCodesStorage.sol
126:    function _validateBytecode(bytes32 _bytecodeHash) internal pure
128:        require(version == 1 && _bytecodeHash[1] == bytes1(0), "Incorrectly formatted bytecodeHash");

/ContractDeployer.sol
69:    function updateNonceOrdering(AccountNonceOrdering _nonceOrdering) external onlySystemCall
72:        require(
73:            _nonceOrdering == AccountNonceOrdering.Arbitrary &&
74:                currentInfo.nonceOrdering == AccountNonceOrdering.Sequential,
75:            "It is only possible to change from sequential to arbitrary ordering"
76:        );
```

Recommendation:
Splitting `require` statements that uses `&&` can help improve readability and even saves gas

### [R-07] Use ternary operators to shorten if/else statements
```solidity
2 results - 2 files

/SystemContext.sol
73:    function getBlockHashEVM(uint256 _block) external view returns (bytes32 hash) {
74:        if (block.number < _block || block.number - _block > 256) {
75:            hash = bytes32(0);
76:        } else {
77:            hash = blockHash[_block];
78:        }

/DefaultAccount.sol
77:    function _validateTransaction
105:        if (_isValidSignature(txHash, _transaction.signature)) {
106:            magic = ACCOUNT_VALIDATION_SUCCESS_MAGIC;
107:        } else {
108:            magic = bytes4(0);
109:        }
```

Reccomendation:
Use ternary operators to shorten if/else statements to improve readability and shorten SLOC

```solidity
function getBlockHashEVM(uint256 _block) external view returns (bytes32 hash) {
    hash = (block.number < _block || block.number - _block > 256) ? bytes32(0): blockHash[_block];
}


function _validateTransaction
    magic = (_isValidSignature(txHash, _transaction.signature)) ? ACCOUNT_VALIDATION_SUCCESS_MAGIC : bytes4(0);
```

### [R-08] No need to explicitly upcast `uint` variables
Context:
```solidity
16 results - 4 files

/AccountCodeStorage.sol
 34:    function storeAccountConstructingCodeHash(address _address, bytes32 _hash) external override onlyDeployer
 38:        uint256 addressAsKey = uint256(uint160(_address));

 46:    function markAccountCodeHashAsConstructed(address _address) external override onlyDeployer
 54:        uint256 addressAsKey = uint256(uint160(_address));

 63:    function getRawCodeHash(address _address) public view override returns (bytes32 codeHash)
 64:        uint256 addressAsKey = uint256(uint160(_address));

/NonceHolder.sol
 45:    function getMinNonce(address _address) public view returns (uint256)
 46:        uint256 addressAsKey = uint256(uint160(_address));

 56:    function getRawNonce(address _address) public view returns (uint256) {
 57:        uint256 addressAsKey = uint256(uint160(_address));

 64:    function increaseMinNonce(uint256 _value) public onlySystemCall returns (uint256 oldMinNonce)
 67:        uint256 addressAsKey = uint256(uint160(msg.sender));

 81:    function setValueUnderNonce(uint256 _key, uint256 _value) public onlySystemCall
 91:        uint256 addressAsKey = uint256(uint160(msg.sender));

101:    function getValueUnderNonce(uint256 _key) public view returns (uint256) {
102:        uint256 addressAsKey = uint256(uint160(msg.sender));

109:    function incrementMinNonceIfEquals(uint256 _expectedNonce) external onlySystemCall {
110:        uint256 addressAsKey = uint256(uint160(msg.sender));

124:    function getDeploymentNonce(address _address) external view returns (uint256 deploymentNonce) {
125:        uint256 addressAsKey = uint256(uint160(_address));

134:    function incrementDeploymentNonce(address _address) external onlySystemCall returns (uint256 prevDeploymentNonce)
136:        uint256 addressAsKey = uint256(uint160(_address));

146:    function isNonceUsed(address _address, uint256 _nonce) public view returns (bool) {
147:        uint256 addressAsKey = uint256(uint160(_address));

 77:    function _validateTransaction
 95:        if (_transaction.to == uint256(uint160(address(DEPLOYER_SYSTEM_CONTRACT))))

/BootloaderUtilities.sol
 43:    function encodeLegacyTransactionHash(Transaction calldata _transaction) internal view returns (bytes32 txHash)
 90:            uint256 vInt = uint256(uint8(_transaction.signature[64]));

138:    function encodeEIP2930TransactionHash(Transaction calldata _transaction) internal view returns (bytes32)
189:            uint256 vInt = uint256(uint8(_transaction.signature[64])); 

228:    function encodeEIP1559TransactionHash(Transaction calldata _transaction) internal view returns (bytes32)
284:            uint256 vInt = uint256(uint8(_transaction.signature[64]));

/Constants.sol
32: uint256 constant CURRENT_MAX_PRECOMPILE_ADDRESS = uint256(uint160(SHA256_SYSTEM_CONTRACT));
```

Description:
There is no need to explicitly upcast value of `uint` (e.g, `uint160` to `uint256`) when assigning to a variable of the higher `uint` type or when used as a comparison for a conditional statement as implicit conversion will be done by the EVM as long as no information is lost

### [R-09] Use `type<uint>.max` instead of exponentiation
```solidity
1 result - 1 file

/Constants.sol
74: uint256 constant MAX_MSG_VALUE = 2 ** 128 - 1;
```

Recommendation:
Consider using `type(uint128).max` instead of exponentiation for improved readability

### [R-10] Remove unused code
Context:
```solidity
1 result - 1 file

/L2EthToken.sol
26: address __DEPRECATED_l2Bridge = address(0);
```

Recommendation:
Remove unused code from the codebase


### [O-01] Unlocked pragma
Context:

All contracts

Reccomendation:
Locking pragma helps ensure that contracts do not accidentally get deployed using a different compiler version with which they have been tested the most. Hence, it is recommended to lock pragmas to a specific Solidity version.
Solidity compiler bugs: [Known solidity bugs](https://github.com/ethereum/solidity/blob/develop/docs/bugs_by_version.json)
Solidity new features: [Solidity new features](https://github.com/ethereum/solidity/blob/develop/Changelog.md)

### [O-02] NatSpec Comments should be increased in contracts
Context:
All Contracts

Description
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.

In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

Recommendation
NatSpec comments should be increased in contracts.

### [O-03] Include return parameters in NatSpec comments
Context:
```solidity
49 results - 14 files

/L1Messenger.sol
 22: function sendToL1(bytes calldata _message) external override returns (bytes32 hash)

/MsgValueSimulator.sol
 22: function _getAbiParams() internal view returns (uint256 value, bool isSystemCall, address to)
 32: fallback(bytes calldata _data) external payable onlySystemCall returns (bytes memory)

/BytecodeCompressor.sol
 35: function publishCompressedBytecode
 75: function _decodeRawBytecode

/L2EthToken.sol
 64: function balanceOf(uint256 _account) external view override returns (uint256)
 97: function _getL1WithdrawMessage(address _to, uint256 _amount) internal pure returns (bytes memory)
103: function name() external pure override returns (string memory)
109: function symbol() external pure override returns (string memory)
115: function decimals() external pure override returns (uint8)

/SystemContext.sol
 73: function getBlockHashEVM(uint256 _block) external view returns (bytes32 hash)

/KnownCodesStorage.sol
117: function getMarker(bytes32 _hash) public view override returns (uint256 marker)

/DefaultAccount.sol
 66: function validateTransaction
 77: function _validateTransaction

/ContractDeployer.sol
 32: function getAccountInfo(address _address) external view returns (AccountInfo memory info)
 38: function extendedAccountVersion(address _address) public view returns (AccountAbstractionVersion)
110: function getNewAddressCreate
128: function create2
144: function create
160: function create2Account
181: function createAccount

/SystemContractHelper.sol
 63: function getCodeAddress() internal view returns (address addr)
124: function packPrecompileParams
164: function setValueForNextFarCall(uint128 _value) internal returns (bool success)
198: function getZkSyncMetaBytes() internal view returns (uint256 meta)
287: function getCallFlags() internal view returns (uint256 callFlags)

/UnsafeBytesCalldata.sol
 18: function readUint16(bytes calldata _bytes, uint256 _start) internal pure returns (uint16 result)
 25: function readUint64(bytes calldata _bytes, uint256 _start) internal pure returns (uint64 result)

/Utils.sol
 19: function safeCastToU128(uint256 _x) internal pure returns (uint128)
 25: function safeCastToU32(uint256 _x) internal pure returns (uint32)
 31: function safeCastToU24(uint256 _x) internal pure returns (uint24)

/RLPEncoder.sol
  6: function encodeAddress(address _val) internal pure returns (bytes memory encoded)
 19: function encodeUint256(uint256 _val) internal pure returns (bytes memory encoded)
 44: function encodeNonSingleBytesLen(uint64 _len) internal pure returns (bytes memory)
 51: function encodeListLen(uint64 _len) internal pure returns (bytes memory)
 55: function _encodeLength(uint64 _len, uint256 _offset) private pure returns (bytes memory encoded)
 79: function _highestByteSet(uint256 _number) private pure returns (uint256 hbs)

/Address.sol
 91: function functionCall(address target, bytes memory data)
110: function functionCall
129: function functionCallWithValue
149: function functionCallWithValue
177: function functionStaticCall(address target, bytes memory data)
196: function functionStaticCall
217: function functionDelegateCall(address target, bytes memory data)
235: function functionDelegateCall
256: function verifyCallResultFromTarget
280: function verifyCallResult

/TransactionHelper.sol
 99: function encodeHash(Transaction calldata _transaction) internal view returns (bytes32 resultHash)
394: function payToTheBootloader(Transaction calldata _transaction) internal returns (bool success)
404: function totalRequiredBalance(Transaction calldata _transaction) internal pure returns (uint256 requiredBalance)
```

Recommendation
Include return parameters in NatSpec comments:

### [O-04] Function naming suggestion of internal and private functions starts with _
Description:
Proper use of _ as a function name prefix and a common pattern is to prefix internal and private function names with _. 
internal and private functions: the mixedCase format starting with an underscore (_mixedCase starting with an underscore)

Context:
```solidity
81 results - 10 files

/BootloaderUtilities.sol
 43: function encodeLegacyTransactionHash(Transaction calldata _transaction) internal view returns (bytes32 txHash)
138: function encodeEIP2930TransactionHash(Transaction calldata _transaction) internal view returns (bytes32)
228: function encodeEIP1559TransactionHash(Transaction calldata _transaction) internal view returns (bytes32)

/SystemContractHelper.sol
 48: function toL1(bool _isService, bytes32 _key, bytes32 _value) internal
 63: function getCodeAddress() internal view returns (address addr)
 74: function loadCalldataIntoActivePtr() internal view
 85: function ptrPackIntoActivePtr(uint256 _farCallAbi) internal view
 94: function ptrAddIntoActive(uint32 _value) internal view
106: function ptrShrinkIntoActive(uint32 _shrink) internal view
124: function packPrecompileParams
147: function precompileCall(uint256 _rawParams, uint32 _gasToBurn) internal view returns (bool success)
164: function setValueForNextFarCall(uint128 _value) internal returns (bool success)
187: function eventWrite(uint256 value1, uint256 value2) internal
198: function getZkSyncMetaBytes() internal view returns (uint256 meta)
210: function extractNumberFromMeta(uint256 meta, uint256 offset, uint256 size) internal pure returns (uint256 result)
221: function getGasPerPubdataByteFromMeta(uint256 meta) internal pure returns (uint32 gasPerPubdataByte)
231: function getHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 heapSize)
240: function getAuxHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 auxHeapSize)
248: function getShardIdFromMeta(uint256 meta) internal pure returns (uint8 shardId)
257: function getCallerShardIdFromMeta(uint256 meta) internal pure returns (uint8 callerShardId)
266: function getCodeShardIdFromMeta(uint256 meta) internal pure returns (uint8 codeShardId)
272: function getZkSyncMeta() internal view returns (ZkSyncMeta memory meta) 
287: function getCallFlags() internal view returns (uint256 callFlags)
298: function getCalldataPtr() internal view returns (uint256 ptr)
309: function getExtraAbiData(uint256 index) internal view returns (uint256 extraAbiData)
320: function isSystemCall() internal view returns (bool)
329: function isSystemContract(address _address) internal pure returns (bool)

/UnsafeBytesCalldata.sol
 18: function readUint16(bytes calldata _bytes, uint256 _start) internal pure returns (uint16 result)
 25: function readUint64(bytes calldata _bytes, uint256 _start) internal pure returns (uint64 result)

/Utils.sol
 19: function safeCastToU128(uint256 _x) internal pure returns (uint128)
 25: function safeCastToU32(uint256 _x) internal pure returns (uint32)
 31: function safeCastToU24(uint256 _x) internal pure returns (uint24)
 38: function bytecodeLenInBytes(bytes32 _bytecodeHash) internal pure returns (uint256 codeLength)
 43: function bytecodeLenInWords(bytes32 _bytecodeHash) internal pure returns (uint256 codeLengthInWords)
 50: function isContractConstructing(bytes32 _bytecodeHash) internal pure returns (bool)
 57: function constructingBytecodeHash(bytes32 _bytecodeHash) internal pure returns (bytes32)
 65: function constructedBytecodeHash(bytes32 _bytecodeHash) internal pure returns (bytes32)
 76: function hashL2Bytecode(bytes calldata _bytecode) internal view returns (bytes32 hashedBytecode)

/RLPEncoder.sol
  6: function encodeAddress(address _val) internal pure returns (bytes memory encoded)
 19: function encodeUint256(uint256 _val) internal pure returns (bytes memory encoded)
 44: function encodeNonSingleBytesLen(uint64 _len) internal pure returns (bytes memory)
 51: function encodeListLen(uint64 _len) internal pure returns (bytes memory)

/SafeERC20.sol
 22: function safeTransfer
 33: function safeTransferFrom
 52: function safeApprove
 70: function safeIncreaseAllowance
 86: function safeDecreaseAllowance
109: function safePermit

/SystemContractsCaller.sol
 75: function systemCall(uint32 gasLimit, address to, uint256 value, bytes memory data) internal returns (bool success)
122: function systemCallWithReturndata
149: function systemCallWithPropagatedRevert
213: function getFarCallABI
249: function getFarCallABIWithEmptyFatPointer

/EfficientCall.sol
 35: function keccak(bytes calldata _data) internal view returns (bytes32)
 44: function sha(bytes calldata _data) internal view returns (bytes32)
 56: function call
 71: function staticCall
 85: function delegateCall
102: function mimicCall
120: function rawCall
154: function rawStaticCall(uint256 _gas, address _address, bytes calldata _data) internal view returns (bool success)
168: function rawDelegateCall(uint256 _gas, address _address, bytes calldata _data) internal returns (bool success)
186: function rawMimicCall
227: function propagateRevert() internal pure

/Address.sol
 36: function isContract(address account) internal view returns (bool)
 60: function sendValue(address payable recipient, uint256 amount) internal
 91: function functionCall(address target, bytes memory data)
110: function functionCall
129: function functionCallWithValue
149: function functionCallWithValue
177: function functionStaticCall(address target, bytes memory data)
196: function functionStaticCall
217: function functionDelegateCall(address target, bytes memory data)
235: function functionDelegateCall
256: function verifyCallResultFromTarget
280: function verifyCallResult

/TransactionHelper.sol
 93: function isEthToken(uint256 _addr) internal pure returns (bool)
 99: function encodeHash(Transaction calldata _transaction) internal view returns (bytes32 resultHash)
361: function processPaymasterInput(Transaction calldata _transaction) internal
394: function payToTheBootloader(Transaction calldata _transaction) internal returns (bool success)
404: function totalRequiredBalance(Transaction calldata _transaction) internal pure returns (uint256 requiredBalance)
```

### [O-05] Contract layout that does not comply with solidity style guide
Description:
Contract layout ordering helps readers easily identify  definitions 
- Type declarations
- State variables
- Events
- Modifiers
- Functions

Context:

The following contracts does not follow solidity style guide layout
```solidity
1 result - 1 file

/ContractDeployer.sol
196:    struct ForceDeployment {
197:        // The bytecode hash to put on an address
198:        bytes32 bytecodeHash;
199:        // The address on which to deploy the bytecodehash to
200:        address newAddress;
201:        // Whether to run the constructor on the force deployment
202:        bool callConstructor;
203:        // The value with which to initialize a contract
204:        uint256 value;
205:        // The constructor calldata
206:        bytes input;
207:    }
```


### [O-06] Order of function does not comply with the solidity style guide
Description:
Function ordering helps readers easily identify which functions they can call and to find the constructor and fallback definitions

- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private


Context:

The following contracts do not follow solidity style guide for order of functions
```solidity
/MsgValueSimulator.sol
/AccountCodeStorage.sol
/SystemContext.sol
/KnownCodesStorage.sol
/NonceHolder.sol
/DefaultAccount.sol
/ContractDeployer.sol
/TransactionHelper.sol
```
