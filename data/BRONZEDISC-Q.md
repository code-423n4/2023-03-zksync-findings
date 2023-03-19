## QA
---

### Layout Order [1]

- The best-practices for layout within a contract is the following order: state variables, events, modifiers, constructor and functions.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol

```solidity
// struct coming after functions
196:    struct ForceDeployment {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IEthToken.sol

```solidity
// events should come before functions
22:    event Mint(address indexed account, uint256 amount);
24:    event Transfer(address indexed from, address indexed to, uint256 value);
26:    event Withdrawal(address indexed _l2Sender, address indexed _l1Receiver, uint256 _amount);
```

---

### Function Visibility [2]

- Order of Functions: Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), public, external, internal, private. Within a grouping, place the view and pure functions last.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol

```solidity
// public functions coming after external ones
83:    function getBlockNumberAndTimestamp() public view returns (uint256 blockNumber, uint256 blockTimestamp) {
91:    function getBlockNumber() public view returns (uint256 blockNumber) {
97:    function getBlockTimestamp() public view returns (uint256 timestamp) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol

```solidity
// public function coming after external ones
146:    function isNonceUsed(address _address, uint256 _nonce) public view returns (bool) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol

```solidity
// internal function coming before external ones
97:    function _getL1WithdrawMessage(address _to, uint256 _amount) internal pure returns (bytes memory) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/KnownCodesStorage.sol

```solidity
// public function coming after external and internal ones
117:    function getMarker(bytes32 _hash) public view override returns (uint256 marker) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol

```solidity
// internal functions coming before external ones
77:    function _validateTransaction(
140:    function _execute(Transaction calldata _transaction) internal {
162:    function _isValidSignature(bytes32 _hash, bytes memory _signature) internal view returns (bool) {

// receive and fallback should come before all the other functions
223    fallback() external {
230:    receive() external payable {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol

```solidity

// public functions coming after external ones
38:    function extendedAccountVersion(address _address) public view returns (AccountAbstractionVersion) {
90:    function getNewAddressCreate2(
110:    function getNewAddressCreate(
160:    function create2Account(
181:    function createAccount(

// internal function coming before public and external ones
53:    function _storeAccountInfo(address _address, AccountInfo memory _newInfo) internal {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/AccountCodeStorage.sol

```solidity
// public function coming after external ones
63:    function getRawCodeHash(address _address) public view override returns (bytes32 codeHash) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol

```solidity
// internal functions coming after private ones
361:    function processPaymasterInput(Transaction calldata _transaction) internal {
394:    function payToTheBootloader(Transaction calldata _transaction) internal returns (bool success) {
404:    function totalRequiredBalance(Transaction calldata _transaction) internal pure returns (uint256 requiredBalance) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol

```solidity
// non-view and non-pure functions coming after view ones
164:    function setValueForNextFarCall(uint128 _value) internal returns (bool success) {
177:    function eventInitialize(uint256 initializer, uint256 value1) internal {
187:    function eventWrite(uint256 value1, uint256 value2) internal {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol

```solidity
// non-view functions coming after view ones
56:    function call(
85:    function delegateCall(
102:    function mimicCall(
120:    function rawCall(
168:    function rawDelegateCall(uint256 _gas, address _address, bytes calldata _data) internal returns (bool success) {
186:    function rawMimicCall(
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/INonceHolder.sol

```solidity
// view functions should come after non-views
17:    function getMinNonce(address _address) external view returns (uint256);
21:    function getRawNonce(address _address) external view returns (uint256);
30:    function getValueUnderNonce(uint256 _key) external view returns (uint256);
37:    function getDeploymentNonce(address _address) external view returns (uint256);
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IImmutableSimulator.sol

```solidity
// view functions should come after non-views
11:    function getImmutable(address _dest, uint256 _index) external view returns (bytes32);
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IEthToken.sol

```solidity
// view functions should come after non-views
6:    function balanceOf(uint256) external view returns (uint256);

// view functions should come after non-views
10:    function totalSupply() external view returns (uint256);

// pure functions should come after non-pures
12:    function name() external pure returns (string memory);

// pure functions should come after non-pures
14:    function symbol() external pure returns (string memory);

// pure functions should come after non-pures
16:    function decimals() external pure returns (uint8);
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IContractDeployer.sol

```solidity
// view functions should come after non-views
43:    function getNewAddressCreate2(

// pure functions should come after non-pures
50:    function getNewAddressCreate(address _sender, uint256 _senderNonce) external pure returns (address newAddress);

// view functions should come after non-views
84:    function getAccountInfo(address _address) external view returns (AccountInfo memory info);
```

---

### natSpec missing [3]

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol

```solidity
// @param missing
60:    function setTxOrigin(address _newOrigin) external onlyBootloader {
132:    function unsafeOverrideBlock(uint256 _newTimestamp, uint256 number, uint256 _baseFee) external onlyBootloader {

// @param and @return missing
73:    function getBlockHashEVM(uint256 _block) external view returns (bytes32 hash) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol

```solidity
146:    function isNonceUsed(address _address, uint256 _nonce) public view returns (bool) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/MsgValueSimulator.sol

```solidity
// @return missing
22:    function _getAbiParams() internal view returns (uint256 value, bool isSystemCall, address to) {

32:    fallback(bytes calldata _data) external payable onlySystemCall returns (bytes memory) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L1Messenger.sol

```solidity
22:    function sendToL1(bytes calldata _message) external override returns (bytes32 hash) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/KnownCodesStorage.sol

```solidity
// @param missing
117:    function getMarker(bytes32 _hash) public view override returns (uint256 marker) {
126:    function _validateBytecode(bytes32 _bytecodeHash) internal pure {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol

```solidity
// @return missing
66:    function validateTransaction(
77:    function _validateTransaction(

// @params missing
117:    function executeTransaction(
215:    function prepareForPaymaster(
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol

```solidity
// @param and @return missing
32:    function getAccountInfo(address _address) external view returns (AccountInfo memory info) {
249:    function _ensureBytecodeIsKnown(bytes32 _bytecodeHash) internal view {

// @param missing
38:    function extendedAccountVersion(address _address) public view returns (AccountAbstractionVersion) {
53:    function _storeAccountInfo(address _address, AccountInfo memory _newInfo) internal {

// @return missing
110:    function getNewAddressCreate(
128:    function create2(
144:    function create(
160:    function create2Account(
181:    function createAccount(

249:    function _nonSystemDeployOnAddress(


```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BytecodeCompressor.sol

```solidity
// @return missing
35:    function publishCompressedBytecode(
75:    function _decodeRawBytecode(
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/Utils.sol

```solidity
19:    function safeCastToU128(uint256 _x) internal pure returns (uint128) {

25:    function safeCastToU32(uint256 _x) internal pure returns (uint32) {

31:    function safeCastToU24(uint256 _x) internal pure returns (uint24) {

// @param missing
38:    function bytecodeLenInBytes(bytes32 _bytecodeHash) internal pure returns (uint256 codeLength) {
43:    function bytecodeLenInWords(bytes32 _bytecodeHash) internal pure returns (uint256 codeLengthInWords) {
50:    function isContractConstructing(bytes32 _bytecodeHash) internal pure returns (bool) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/UnsafeBytesCalldata.sol

```solidity
18:    function readUint16(bytes calldata _bytes, uint256 _start) internal pure returns (uint16 result) {

25:    function readUint64(bytes calldata _bytes, uint256 _start) internal pure returns (uint64 result) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol

```solidity
// @param and @return missing
99:    function encodeHash(Transaction calldata _transaction) internal view returns (bytes32 resultHash) {
117:    function _encodeHashEIP712Transaction(Transaction calldata _transaction) private view returns (bytes32) {
394:    function payToTheBootloader(Transaction calldata _transaction) internal returns (bool success) {

// @param missing
147:    function _encodeHashLegacyTransaction(Transaction calldata _transaction) private view returns (bytes32) {
218:    function _encodeHashEIP2930Transaction(Transaction calldata _transaction) private view returns (bytes32) {
288:    function _encodeHashEIP1559Transaction(Transaction calldata _transaction) private view returns (bytes32) {
361:    function processPaymasterInput(Transaction calldata _transaction) internal {
404:    function totalRequiredBalance(Transaction calldata _transaction) internal pure returns (uint256 requiredBalance) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol

```solidity
16:   struct ZkSyncMeta {

// @return missing
63:    function getCodeAddress() internal view returns (address addr) {
164:    function setValueForNextFarCall(uint128 _value) internal returns (bool success) {

// @param missing
85:    function ptrPackIntoActivePtr(uint256 _farCallAbi) internal view {
94:    function ptrAddIntoActive(uint32 _value) internal view {
106:    function ptrShrinkIntoActive(uint32 _shrink) internal view {
309:    function getExtraAbiData(uint256 index) internal view returns (uint256 extraAbiData) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/RLPEncoder.sol

```solidity
5:    library RLPEncoder {

6:    function encodeAddress(address _val) internal pure returns (bytes memory encoded) {

19:    function encodeUint256(uint256 _val) internal pure returns (bytes memory encoded) {

55:    function _encodeLength(uint64 _len, uint256 _offset) private pure returns (bytes memory encoded) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/ISystemContext.sol

```solidity
11:    function chainId() external view returns (uint256);

13:    function origin() external view returns (address);

15:    function gasPrice() external view returns (uint256);

17:    function blockGasLimit() external view returns (uint256);

19:    function coinbase() external view returns (address);

21:    function difficulty() external view returns (uint256);

23:    function baseFee() external view returns (uint256);

25:    function blockHash(uint256 _block) external view returns (bytes32);

27:    function getBlockHashEVM(uint256 _block) external view returns (bytes32);

29:    function getBlockNumberAndTimestamp() external view returns (uint256 blockNumber, uint256 blockTimestamp);

35:    function getBlockTimestamp() external view returns (uint256);
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IPaymasterFlow.sol

```solidity
13:    function general(bytes calldata input) external;

15:    function approvalBased(address _token, uint256 _minAllowance, bytes calldata _innerInput) external;
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IPaymaster.sol

```solidity
7:    enum ExecutionResult {

14:   interface IPaymaster {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/INonceHolder.sol

```solidity
14:    event ValueSetUnderNonce(address indexed accountAddress, uint256 indexed key, uint256 value);
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IMailbox.sol

```solidity
5:    interface IMailbox {

6:    function finalizeEthWithdrawal(
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IL2StandardToken.sol

```solidity
5:  interface IL2StandardToken {

6:    event BridgeMint(address indexed _account, uint256 _amount);

8:    event BridgeBurn(address indexed _account, uint256 _amount);

10:    function bridgeMint(address _account, uint256 _amount) external;

12:    function bridgeBurn(address _account, uint256 _amount) external;

14:    function l1Address() external view returns (address);

16:    function l2Bridge() external view returns (address);
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IL1Messenger.sol

```solidity
5:    interface IL1Messenger {

10:    function sendToL1(bytes memory _message) external returns (bytes32);
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IKnownCodesStorage.sol

```solidity
5:    interface IKnownCodesStorage {

6:    event MarkedAsKnown(bytes32 indexed bytecodeHash, bool indexed sendBytecodeToL1);

8:    function markFactoryDeps(bool _shouldSendToL1, bytes32[] calldata _hashes) external;

10:    function markBytecodeAsPublished(

16:    function getMarker(bytes32 _hash) external view returns (uint256);
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IImmutableSimulator.sol

```solidity
5:    struct ImmutableData {

10:   interface IImmutableSimulator {

11:    function getImmutable(address _dest, uint256 _index) external view returns (bytes32);

13:    function setImmutables(address _dest, ImmutableData[] calldata _immutables) external;
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IEthToken.sol

```solidity
5:   interface IEthToken {

6:    function balanceOf(uint256) external view returns (uint256);

8:    function transferFromTo(address _from, address _to, uint256 _amount) external;

10:    function totalSupply() external view returns (uint256);

12:    function name() external pure returns (string memory);

14:    function symbol() external pure returns (string memory);

16:    function decimals() external pure returns (uint8);

18:    function mint(address _account, uint256 _amount) external;

20:    function withdraw(address _l1Receiver) external payable;

22:    event Mint(address indexed account, uint256 amount);

24:    event Transfer(address indexed from, address indexed to, uint256 value);

26:    event Withdrawal(address indexed _l2Sender, address indexed _l1Receiver, uint256 _amount);
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IContractDeployer.sol

```solidity
5:    interface IContractDeployer {

28:    struct AccountInfo {

33:    event ContractDeployed(

39:    event AccountNonceOrderingUpdated(address indexed accountAddress, AccountNonceOrdering nonceOrdering);

41:    event AccountVersionUpdated(address indexed accountAddress, AccountAbstractionVersion aaVersion);

43:    function getNewAddressCreate2(

50:    function getNewAddressCreate(address _sender, uint256 _senderNonce) external pure returns (address newAddress);

52:    function create2(

58:    function create2Account(

// @params and @return missing
68:    function create(

// @params and @return missing
76:    function createAccount(

// @params and @return missing
84:    function getAccountInfo(address _address) external view returns (AccountInfo memory info);

// @params missing
87:    function updateAccountVersion(AccountAbstractionVersion _version) external;

// @params missing
90:    function updateNonceOrdering(AccountNonceOrdering _nonceOrdering) external;
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IBytecodeCompressor.sol

```solidity
5:    interface IBytecodeCompressor {

6:    function publishCompressedBytecode(
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IBootloaderUtilities.sol

```solidity
7:    interface IBootloaderUtilities {

8:    function getTransactionHashes(
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IAccountCodeStorage.sol

```solidity
5:   interface IAccountCodeStorage {

6:    function storeAccountConstructingCodeHash(address _address, bytes32 _hash) external;

8:    function markAccountCodeHashAsConstructed(address _address) external;

10:    function getRawCodeHash(address _address) external view returns (bytes32 codeHash);

12:    function getCodeHash(uint256 _input) external view returns (bytes32 codeHash);

14:    function getCodeSize(uint256 _input) external view returns (uint256 codeSize);
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IAccount.sol

```solidity
9:    interface IAccount {

26:    function executeTransaction(

34:    function executeTransactionFromOutside(Transaction calldata _transaction) external payable;

36:    function payForTransaction(

42:    function prepareForPaymaster(
```

---

### State variable and function names [4]

- Variables should be named according to their specifications
- private and internal `variables` should preppend with `underline`
- private and internal `functions` should preppend with `underline`
- constant state variables should be UPPER_CASE

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol

```solidity
// private and internal `variables` should preppend with `underline`
35:    mapping(uint256 => uint256) internal rawNonces;
40:    mapping(uint256 => mapping(uint256 => uint256)) internal nonceValues;

//  private and internal `functions` should preppend with `underline`
171:    function _splitRawNonce(uint256 _rawMinNonce) internal pure returns (uint256 deploymentNonce, uint256 minNonce) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ImmutableSimulator.sol

```solidity
//  private and internal `variables` should preppend with `underline`
20:    mapping(uint256 => mapping(uint256 => bytes32)) internal immutableDataStorage;
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BootloaderUtilities.sol

```solidity
43:    function encodeLegacyTransactionHash(Transaction calldata _transaction) internal view returns (bytes32 txHash) {
138:    function encodeEIP2930TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) {
228:    function encodeEIP1559TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/Utils.sol

```solidity
//  private and internal `functions` should preppend with `underline`
19:    function safeCastToU128(uint256 _x) internal pure returns (uint128) {
25:    function safeCastToU32(uint256 _x) internal pure returns (uint32) {
31:    function safeCastToU24(uint256 _x) internal pure returns (uint24) {
38:    function bytecodeLenInBytes(bytes32 _bytecodeHash) internal pure returns (uint256 codeLength) {
43:    function bytecodeLenInWords(bytes32 _bytecodeHash) internal pure returns (uint256 codeLengthInWords) {
50:    function isContractConstructing(bytes32 _bytecodeHash) internal pure returns (bool) {
57:    function constructingBytecodeHash(bytes32 _bytecodeHash) internal pure returns (bytes32) {
65:    function constructedBytecodeHash(bytes32 _bytecodeHash) internal pure returns (bytes32) {
76:    function hashL2Bytecode(bytes calldata _bytecode) internal view returns (bytes32 hashedBytecode) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol

```solidity
//  private and internal `functions` should preppend with `underline`
93:    function isEthToken(uint256 _addr) internal pure returns (bool) {
99:    function encodeHash(Transaction calldata _transaction) internal view returns (bytes32 resultHash) {
361:    function processPaymasterInput(Transaction calldata _transaction) internal {
394:    function payToTheBootloader(Transaction calldata _transaction) internal returns (bool success) {
404:    function totalRequiredBalance(Transaction calldata _transaction) internal pure returns (uint256 requiredBalance) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractsCaller.sol

```solidity
//  private and internal `functions` should preppend with `underline`
75:    function systemCall(uint32 gasLimit, address to, uint256 value, bytes memory data) internal returns (bool success) {
122:    function systemCallWithReturndata(
149:    function systemCallWithPropagatedRevert(
213:    function getFarCallABI(
249:    function getFarCallABIWithEmptyFatPointer(
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol

```solidity
// private and internal `functions` should preppend with `underline`
48:    function toL1(bool _isService, bytes32 _key, bytes32 _value) internal {
63:    function getCodeAddress() internal view returns (address addr) {
74:    function loadCalldataIntoActivePtr() internal view {
85:    function ptrPackIntoActivePtr(uint256 _farCallAbi) internal view {
94:    function ptrAddIntoActive(uint32 _value) internal view {
106:    function ptrShrinkIntoActive(uint32 _shrink) internal view {
124:    function packPrecompileParams(
147:    function precompileCall(uint256 _rawParams, uint32 _gasToBurn) internal view returns (bool success) {
164:    function setValueForNextFarCall(uint128 _value) internal returns (bool success) {
177:    function eventInitialize(uint256 initializer, uint256 value1) internal {
187:    function eventWrite(uint256 value1, uint256 value2) internal {
198:    function getZkSyncMetaBytes() internal view returns (uint256 meta) {
210:    function extractNumberFromMeta(uint256 meta, uint256 offset, uint256 size) internal pure returns (uint256 result) {
221:    function getGasPerPubdataByteFromMeta(uint256 meta) internal pure returns (uint32 gasPerPubdataByte) {
231:    function getHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 heapSize) {
240:    function getAuxHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 auxHeapSize) {
248:    function getShardIdFromMeta(uint256 meta) internal pure returns (uint8 shardId) {
257:    function getCallerShardIdFromMeta(uint256 meta) internal pure returns (uint8 callerShardId) {
266:    function getCodeShardIdFromMeta(uint256 meta) internal pure returns (uint8 codeShardId) {
272:    function getZkSyncMeta() internal view returns (ZkSyncMeta memory meta) {
287:    function getCallFlags() internal view returns (uint256 callFlags) {
298:    function getCalldataPtr() internal view returns (uint256 ptr) {
309:    function getExtraAbiData(uint256 index) internal view returns (uint256 extraAbiData) {
320:    function isSystemCall() internal view returns (bool) {
329:    function isSystemContract(address _address) internal pure returns (bool) {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/RLPEncoder.sol

```solidity
6:    function encodeAddress(address _val) internal pure returns (bytes memory encoded) {
19:    function encodeUint256(uint256 _val) internal pure returns (bytes memory encoded) {
44:    function encodeNonSingleBytesLen(uint64 _len) internal pure returns (bytes memory) {
51:    function encodeListLen(uint64 _len) internal pure returns (bytes memory) {

```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol

```solidity
// private and internal `functions` should preppend with `underline`
35:    function keccak(bytes calldata _data) internal view returns (bytes32) {
44:    function sha(bytes calldata _data) internal view returns (bytes32) {
56:    function call(
71:    function staticCall(
85:    function delegateCall(
102:    function mimicCall(
120:    function rawCall(
154:    function rawStaticCall(uint256 _gas, address _address, bytes calldata _data) internal view returns (bool success) {
168:    function rawDelegateCall(uint256 _gas, address _address, bytes calldata _data) internal returns (bool success) {
186:    function rawMimicCall(
```

---

### Initialized variables [5]

- Variables are default initialized with 0 for `uint / int`, 0x0 for `address` and false for `bool`

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol

```solidity
// address is address(0) by default
26:    address __DEPRECATED_l2Bridge = address(0);
```
