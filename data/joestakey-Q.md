# Report


## Low Issues


| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) |  Silent failure of `MsgValueSimulator.fallback()` can lead to loss of `ETH` in edge case | 1 |
| [L-2](#L-2) |  Use `require` instead of `assert` | 2 |

### Silent failure of `MsgValueSimulator.fallback()` can lead to loss of `ETH` in edge case

`MsgValueSimulator` allows the zkEVM to simulate the `msg.value` behavior.

This is handled in `fallback()`: if there is a non-zero value, a call is made to the `L2EthToken` contract to update the `balanceOf` the caller and the callee, then sets the value for the next call using `setValueForNextFarCall`.

```solidity
File: MsgValueSimulator.sol
35: if (value != 0) {
36:             (bool success, ) = address(ETH_TOKEN_SYSTEM_CONTRACT).call(
37:                 abi.encodeCall(ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo, (msg.sender, to, value))
38:             );
```

```solidity
File: MsgValueSimulator.sol
60: SystemContractHelper.setValueForNextFarCall(uint128(value));
```

Before making the `setValueForNextFarCall` call, the function checks `value > MAX_MSG_VALUE`, to ensure the value does not exceed the limit the context can have.

```solidity
File: MsgValueSimulator.sol
48:         if (value > MAX_MSG_VALUE) {
49:             // The if above should never be true, since noone should be able to have
50:             // MAX_MSG_VALUE wei of ether. However, if it does happen for some reason,
51:             // we will revert(0,0).
52:             // Note, that we use raw revert here instead of `panic` to emulate behaviour close to
53:             // the EVM's one, i.e. returndata should be empty.
54:             assembly {
55:                 return(0, 0)//@audit - should revert
56:             }
57:         }
```

The issue is that while the code should actually reverts when reaching this block, it actually returns, meaning the call would silently fail.


#### Impact

In theory in such case, while the balance of the caller and the callee have been updated, the `msg.value` for the call has not been set, meaning that it can potentially be a loss for the caller (depending on the call, examples given below).


#### Probability

`type(uint128).max ~3e38`, ie `~3e20 ETH`.
Given that the current supply of `ETH` is ~`120e6 ETH` , and that the current issuance rate is approximately `4.5%` (ie `5.25e6 ETH/ year`), this means it is not possible to have `msg.value > 1e128` (except in a very far future).

Now in the scenario where a call with `msg.value > type(uint128).max` happens:

#### Case 1: an EOA sending a transaction with `msg.value > type(uint128).max`

```
-> DefaultAccount._execute()
    -> EfficientCall.rawCall(gas, to, value, data)
        -> call(msgValueSimulator, callAddr, _value, _address, 0xFFFF, forwardMask, 0)
            -> MsgValueSimulator.fallback()
```

Because `_execute()` checks that `_transaction.value <= type(uint128).max`, the problematic code block mentioned above cannot actually be reached. -> no issue

#### Case 2: a smart contract making a call with `msg.value > type(uint128).max` to another smart contract

There is no check in the system on `msg.value`, so the call would go through, but without the `setValueForNextFarCall` call.
It would then depend on the receiving smart contract to perform checks on `msg.value`.

If there is no check, the caller essentially lost their `ETH`.

For instance a simple vault deposit call:

```solidity

vault.deposit{value: contractBalance}();
```

where `contractBalance > type(uint128).max`

```solidity
deposit() external payable {
    balances[msg.sender] += msg.value;
}
```

In such case, while the vault `ETH` balance will have been updated in `L2EthToken`, the `msg.value` passed is zero (because of the return [here](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/MsgValueSimulator.sol#L48-L56)): the `balances` mapping of the calling smart contract is not updated, meaning they have lost `contractBalance`.

This issue is very unlikely to arise as stated above, but ensure the following code reverts:

```diff
File: MsgValueSimulator.sol
48: if (value > MAX_MSG_VALUE) {
49:             // The if above should never be true, since noone should be able to have
50:             // MAX_MSG_VALUE wei of ether. However, if it does happen for some reason,
51:             // we will revert(0,0).
52:             // Note, that we use raw revert here instead of `panic` to emulate behaviour close to
53:             // the EVM's one, i.e. returndata should be empty.
54:             assembly {
-55:                 return(0, 0)
+55:                 revert(0, 0)
56:             }
57:         }
```



### L-2  Use `require` instead of `assert`
  As per Solidity’s [documentation](https://docs.soliditylang.org/en/v0.8.17/control-structures.html?highlight=assert#panic-via-assert-and-error-via-require):
 "The assert function creates an error of type Panic(uint256). The same error is created by the compiler in certain situations as listed below. 
Assert should only be used to test for internal errors, and to check invariants. Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix"

*Instances (2)*:
```solidity
File: DefaultAccount.sol

225:         assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);

```

```solidity
File: libraries/RLPEncoder.sol

45:         assert(_len != 1);

```

## Non Critical Issues


| |Issue|
|-|:-|
| [NC-1](#NC-1) | Use named constants |
| [NC-2](#NC-2) | Contract layout not following Solidity standard practice |
| [NC-3](#NC-3) | Order of functions not following Solidity standard practice |
| [NC-4](#NC-4) | Naming conventions of functions |
| [NC-5](#NC-5) | Inconsistent use of 0x00 |
| [NC-6](#NC-6) | Implicit conversion should be avoided |
| [NC-7](#NC-7) | Bit shifts instead of long bit masks of a single bit are better for readability |
| [NC-8](#NC-8) | Typos |
| [NC-9](#NC-9) | Imports should be cleaner |
| [NC-10](#NC-10) | Missing Natspec |
| [NC-11](#NC-11) | Large numbers should use an underscore |
| [NC-12](#NC-12) | Testing code should be removed from production code |
| [NC-13](#NC-13) | The prefix "I" should be reserved for interfaces |
| [NC-14](#NC-14) | Constants on the left are better |

### NC-1 Use named constants
Using named constants makes inline assembly easier to read and verify

*Instances (7)*:
```solidity
File: DefaultAccount.sol
172:             r := mload(add(_signature, 0x20)) //@audit - use a `OneWord` constant instead of 0x20
173:             s := mload(add(_signature, 0x40)) //@audit - use a `TwoWords` constant instead of 0x40

```

```solidity
File: libraries/EfficientCall.sol


218: assembly {
219:                 returndatacopy(add(returnData, 0x20), 0, size) //@audit - use a `OneWord` constant instead of 0x20
220:             }
```

```solidity
File: libraries/RLPEncoder.sol
13: mstore(add(encoded, 0x20),  //@audit - use a `OneWord` constant instead of 0x20
```

```solidity
File: libraries/SystemContractsCaller.sol
79: assembly {
80:             dataStart := add(data, 0x20) //@audit - use a `OneWord` constant instead of 0x20
81:         }


136: assembly {
137:             returndatacopy(add(returnData, 0x20), 0, size) //@audit - use a `OneWord` constant instead of 0x20
138:         }


159: assembly {
160:                 let size := mload(returnData)
161:                 revert(add(returnData, 0x20), size) //@audit - use a `OneWord` constant instead of 0x20
162:             }

```


### NC-3 Order of functions not following Solidity standard practice
Follow Solidity's [style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) for function ordering: constructor, receive/fallback, external, public, internal and private.
Instances below show internal functions declared after external functions, external functions declared after public ones, and internal after private.

*Instances (10)*:
```solidity
File: AccountCodeStorage.sol


63:     function getRawCodeHash(address _address) public view override returns (bytes32 codeHash) {

74:     function getCodeHash(uint256 _input) external view override returns (bytes32) {

102:     function getCodeSize(uint256 _input) external view override returns (uint256 codeSize) {

```


```solidity
File: ContractDeployer.sol


53:     function _storeAccountInfo(address _address, AccountInfo memory _newInfo) internal {

60:     function updateAccountVersion(AccountAbstractionVersion _version) external onlySystemCall {

69:     function updateNonceOrdering(AccountNonceOrdering _nonceOrdering) external onlySystemCall {


```

```solidity
File: DefaultAccount.sol

162:     function _isValidSignature(bytes32 _hash, bytes memory _signature) internal view returns (bool) {

223:     fallback() external {

230:     receive() external payable {

```


```solidity
File: KnownCodesStorage.sol

107:     function _burnGas(uint32 _gasToPay) internal view {

117:     function getMarker(bytes32 _hash) public view override returns (uint256 marker) {


```

```solidity
File: L2EthToken.sol

97:     function _getL1WithdrawMessage(address _to, uint256 _amount) internal pure returns (bytes memory) {

103:     function name() external pure override returns (string memory) {

109:     function symbol() external pure override returns (string memory) {

115:     function decimals() external pure override returns (uint8) {

```

```solidity
File: MsgValueSimulator.sol

22:     function _getAbiParams() internal view returns (uint256 value, bool isSystemCall, address to) {

32:     fallback(bytes calldata _data) external payable onlySystemCall returns (bytes memory) {

```

```solidity
File: NonceHolder.sol


146:     function isNonceUsed(address _address, uint256 _nonce) public view returns (bool) {

158:     function validateNonceUsage(address _address, uint256 _key, bool _shouldBeUsed) external view {


```

```solidity
File: SystemContext.sol

97:     function getBlockTimestamp() public view returns (uint256 timestamp) {

132:     function unsafeOverrideBlock(uint256 _newTimestamp, uint256 number, uint256 _baseFee) external onlyBootloader {

```

```solidity
File: libraries/EfficientCall.sol

210:     function _verifyCallResult(bool _success) private pure returns (bytes memory returnData) {

227:     function propagateRevert() internal pure {

```



### NC-4 Naming conventions of functions
follow Solidity standard naming conventions: internal and private functions should start with an underscore

*Instances (2)*:
```solidity
File: BootloaderUtilities.sol


138:     function encodeEIP2930TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) {

228:     function encodeEIP1559TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) {

```



### NC-5 Inconsistent use of 0x00
To specify bytes 0, `0x00` is used across all the contracts, except for one location where `0x0` is used.

*Instances (1)*:


```solidity
File: ContractDeployer.sol

260:             ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.getCodeHash(uint256(uint160(_newAddress))) == 0x0,

```

### NC-6 Implicit conversion should be avoided
`ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.getRawCodeHash(_address)` returns a `bytes32`, but is compared to a `uint256` here.

*Instances (1)*:


```solidity
File: ContractDeployer.sol

45:         if (ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.getRawCodeHash(_address) == 0) {
46:             return AccountAbstractionVersion.Version1;
47:         }

```



### NC-7 Bit shifts instead of long bit masks of a single bit are better for readability

*Instances (3)*:

```solidity
File: libraries/RLPEncoder.sol

13:             mstore(add(encoded, 0x20), 0x9400000000000000000000000000000000000000000000000000000000000000)


```



```solidity
File: libraries/Utils.sol

13:         0x00ff000000000000000000000000000000000000000000000000000000000000;

17:         0x0001000000000000000000000000000000000000000000000000000000000000;


```

### NC-8 Typos

*Instances (2)*:

```solidity
File: SystemContext.sol
71:     /// @dev Just like the blockhash in the EVM, it returns bytes32(0), when//@audit extra "when" to be removed
72:     /// when queried about hashes that are older than 256 blocks ago.



```

```solidity
File: SystemContext.sol
106:     /// @dev Whie _expectedNewNumber can be derived as prevBlockNumber + 1, we still//@audit Whie -> While
107:     /// manually supply it here for consistency checks.

```




### NC-9 Imports should be cleaner
For readability, it is good practice to only import the libraries and contracts your need from a file.

Update your imports as such `import {contract1 , contract2} from "file.sol";`

*Instances (42)*:
```solidity
File: AccountCodeStorage.sol

5: import "./interfaces/IAccountCodeStorage.sol";

6: import "./libraries/Utils.sol";

```

```solidity
File: BootloaderUtilities.sol

5: import "./interfaces/IBootloaderUtilities.sol";

6: import "./libraries/TransactionHelper.sol";

7: import "./libraries/RLPEncoder.sol";

8: import "./libraries/EfficientCall.sol";

```

```solidity
File: BytecodeCompressor.sol

5: import "./interfaces/IBytecodeCompressor.sol";

6: import "./Constants.sol";

7: import "./libraries/Utils.sol";

8: import "./libraries/UnsafeBytesCalldata.sol";

```

```solidity
File: ContractDeployer.sol

6: import "./interfaces/IContractDeployer.sol";

9: import "./libraries/Utils.sol";

10: import "./libraries/EfficientCall.sol";

```

```solidity
File: DefaultAccount.sol

5: import "./interfaces/IAccount.sol";

6: import "./libraries/TransactionHelper.sol";

7: import "./libraries/SystemContractHelper.sol";

8: import "./libraries/EfficientCall.sol";

```

```solidity
File: ImmutableSimulator.sol

5: import "./interfaces/IImmutableSimulator.sol";

```

```solidity
File: KnownCodesStorage.sol

5: import "./interfaces/IKnownCodesStorage.sol";

6: import "./libraries/Utils.sol";

7: import "./libraries/SystemContractHelper.sol";

```

```solidity
File: L1Messenger.sol

5: import "./interfaces/IL1Messenger.sol";

6: import "./libraries/SystemContractHelper.sol";

7: import "./libraries/EfficientCall.sol";

```

```solidity
File: MsgValueSimulator.sol

5: import "./libraries/EfficientCall.sol";

```

```solidity
File: NonceHolder.sol

5: import "./interfaces/INonceHolder.sol";

6: import "./interfaces/IContractDeployer.sol";

```

```solidity
File: libraries/EfficientCall.sol

5: import "./SystemContractHelper.sol";

6: import "./Utils.sol";

```

```solidity
File: libraries/SystemContractHelper.sol

7: import "./SystemContractsCaller.sol";

8: import "./Utils.sol";

```

```solidity
File: libraries/SystemContractsCaller.sol

6: import "./Utils.sol";

```

```solidity
File: libraries/TransactionHelper.sol

5: import "../openzeppelin/token/ERC20/IERC20.sol";

6: import "../openzeppelin/token/ERC20/utils/SafeERC20.sol";

8: import "../interfaces/IPaymasterFlow.sol";

9: import "../interfaces/IContractDeployer.sol";

11: import "./RLPEncoder.sol";

12: import "./EfficientCall.sol";

```

```solidity
File: libraries/Utils.sol

4: import "./EfficientCall.sol";

```


### NC-10 Missing natspec
Function parameters should have appropriate natspec comments

*Instances (1)*:
```solidity
File: SystemContext.sol

103:     /// @param _prevBlockHash The hash of the previous block.
104:     /// @param _newTimestamp The timestamp of the new block.
105:     /// @param _expectedNewNumber The new block's number
106:     /// @dev Whie _expectedNewNumber can be derived as prevBlockNumber + 1, we still
107:     /// manually supply it here for consistency checks.
108:     /// @dev The correctness of the _prevBlockHash and _newTimestamp should be enforced on L1.
109:     function setNewBlock(
110:         bytes32 _prevBlockHash,
111:         uint256 _newTimestamp,
112:         uint256 _expectedNewNumber,
113:         uint256 _baseFee//@audit missing natspec


```


### NC-11 Large numbers should use an underscore
To improve readability, use an underscore for multiples of 1000

*Instances (1)*:
```solidity
File: SystemContext.sol

40:     uint256 public difficulty = 2500000000000000;

```


### NC-12 Testing code should be removed from production code
Ensure `unsafeOverrideBlock()` is removed from code before deployment

*Instances (1)*:
```solidity
File: /SystemContext.sol
130:     /// @notice A testing method that manually sets the current blocks' number and timestamp.
131:     /// @dev Should be used only for testing / ethCalls and should never be used in production.
132:     function unsafeOverrideBlock(uint256 _newTimestamp, uint256 number, uint256 _baseFee) external onlyBootloader {
133:         currentBlockInfo = (number) * BLOCK_INFO_BLOCK_NUMBER_PART + _newTimestamp;
134:         baseFee = _baseFee;
135:     }

```


### NC-13 The prefix "I" should be reserved for interfaces
abstract contracts should not use it, prefer the suffix `"Base"` instead

*Instances (1)*:
```solidity
File: SystemContractHelper.sol
336: abstract contract ISystemContract {

```

### NC-14 Constants on the left are better
This is [safer in case you forget to put a double equals sign](https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html), as the compiler wil throw an error instead of assigning the value to the variable.

*Instances (53)*:
```solidity
File: AccountCodeStorage.sol

25:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

87:         if (codeHash == 0x00 && NONCE_HOLDER_SYSTEM_CONTRACT.getRawNonce(account) > 0) {

```

```solidity
File: BootloaderUtilities.sol

27:         if (_transaction.txType == EIP_712_TX_TYPE) {

29:         } else if (_transaction.txType == LEGACY_TX_TYPE) {

31:         } else if (_transaction.txType == EIP_1559_TX_TYPE) {

33:         } else if (_transaction.txType == EIP_2930_TX_TYPE) {

91:             require(vInt == 27 || vInt == 28, "Invalid v value");

91:             require(vInt == 27 || vInt == 28, "Invalid v value");

190:             require(vInt == 27 || vInt == 28, "Invalid v value");

190:             require(vInt == 27 || vInt == 28, "Invalid v value");

285:             require(vInt == 27 || vInt == 28, "Invalid v value");

285:             require(vInt == 27 || vInt == 28, "Invalid v value");

```

```solidity
File: BytecodeCompressor.sol

56:                 require(encodedChunk == realChunk, "Encoded chunk does not match the original bytecode");

```

```solidity
File: ContractDeployer.sol

27:         require(msg.sender == address(this), "Callable only by self");

73:             _nonceOrdering == AccountNonceOrdering.Arbitrary &&

74:                 currentInfo.nonceOrdering == AccountNonceOrdering.Sequential,

233:         require(msg.sender == FORCE_DEPLOYER, "Can only be called by FORCE_DEPLOYER_CONTRACT");

242:         require(msg.value == sumOfValues, "`value` provided is not equal to the combined `value`s of deployments");

```

```solidity
File: DefaultAccount.sol

95:         if (_transaction.to == uint256(uint160(address(DEPLOYER_SYSTEM_CONTRACT)))) {

146:         if (to == address(DEPLOYER_SYSTEM_CONTRACT)) {

163:         require(_signature.length == 65, "Signature length is incorrect");

176:         require(v == 27 || v == 28, "v is neither 27 nor 28");

176:         require(v == 27 || v == 28, "v is neither 27 nor 28");

191:         return recoveredAddress == address(this) && recoveredAddress != address(0);

```

```solidity
File: ImmutableSimulator.sol

34:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

```

```solidity
File: KnownCodesStorage.sol

19:         require(msg.sender == BOOTLOADER_FORMAL_ADDRESS, "Callable only by the bootloader");

24:         require(msg.sender == address(BYTECODE_COMPRESSOR_CONTRACT), "Callable only by the bytecode compressor");

128:         require(version == 1 && _bytecodeHash[1] == bytes1(0), "Incorrectly formatted bytecodeHash");

```

```solidity
File: L2EthToken.sol

29:         require(msg.sender == BOOTLOADER_FORMAL_ADDRESS, "Callable only by the bootloader");

42:             msg.sender == MSG_VALUE_SYSTEM_CONTRACT ||

43:                 msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||

44:                 msg.sender == BOOTLOADER_FORMAL_ADDRESS,

```

```solidity
File: NonceHolder.sol

87:         if (accountInfo.nonceOrdering == IContractDeployer.AccountNonceOrdering.Sequential && _key != 0) {

114:         require(oldMinNonce == _expectedNonce, "Incorrect nonce");

135:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "");

```

```solidity
File: SystemContext.sol

16:         require(msg.sender == BOOTLOADER_FORMAL_ADDRESS);

```

```solidity
File: libraries/EfficientCall.sol

37:         require(returnData.length == 32, "keccak256 returned invalid data");

46:         require(returnData.length == 32, "sha returned invalid data");

126:         if (_value == 0) {

250:         if (_data.length == 0) {

```

```solidity
File: libraries/RLPEncoder.sol

24:                 encoded[0] = (_val == 0) ? bytes1(uint8(128)) : bytes1(uint8(_val));

```

```solidity
File: libraries/SystemContractsCaller.sol

97:         if (value == 0) {

```

```solidity
File: libraries/TransactionHelper.sol

94:         return _addr == uint256(uint160(address(ETH_TOKEN_SYSTEM_CONTRACT))) || _addr == 0;

94:         return _addr == uint256(uint160(address(ETH_TOKEN_SYSTEM_CONTRACT))) || _addr == 0;

100:         if (_transaction.txType == LEGACY_TX_TYPE) {

102:         } else if (_transaction.txType == EIP_712_TX_TYPE) {

104:         } else if (_transaction.txType == EIP_1559_TX_TYPE) {

106:         } else if (_transaction.txType == EIP_2930_TX_TYPE) {

365:         if (paymasterInputSelector == IPaymasterFlow.approvalBased.selector) {

384:         } else if (paymasterInputSelector == IPaymasterFlow.general.selector) {

```


```solidity
File: openzeppelin/utils/Address.sol

263:             if (returndata.length == 0) {

```