# QA Report
## Finding Summary

[**Low Severity**](#Low-Severity)
1. [**assert Used Over require**](#1-assert-Used-Over-require)
2. [**Consider SafeCast**](#2-Consider-SafeCast)

[**Non-Critical**](#Non-Critical)
1. [**Contracts Missing Full NatSpec Header**](#1-Contracts-Missing-Full-NatSpec-Header)
2. [**Inconsistent Named Returns**](#2-Inconsistent-Named-Returns)
3. [**Spelling Mistake**](#3-Spelling-Mistake)
4. [**Underscore Notation Not Used or Not Used Consistently**](#4-Underscore-Notation-Not-Used-or-Not-Used-Consistently)
5. [**bytes.concat() Can Be Used Over abi.encodePacked()**](#5-bytesconcat-can-be-used-over-abiencodepacked)
6. [**Space Between License and Pragma**](#6-Space-Between-License-and-Pragma)
7. [**Inconsistent Trailing Period**](#7-Inconsistent-Trailing-Period)
8. [**ERC20 Token Recovery**](#8-ERC20-Token-Recovery)
9. [**Gas Greif If Relayed**](#9-Gas-Greif-If-Relayed)
10. [**Lack of Interface NatSpec**](#10-Lack-of-Interface-NatSpec)
11. [**Inconsistent NatSpec Style**](#11-Inconsistent-NatSpec-Style)
12. [**NatSpec Comments Without Tag**](#12-NatSpec-Comments-Without-Tag)

[**Style Guide Violations**](#Style-Guide-Violations)
1. [**Maximum Line Length**](#1-Maximum-Line-Length)
2. [**Order of Functions**](#2-Order-of-Functions)
3. [**Whitespace in Expressions**](#3-Whitespace-in-Expressions)
4. [**Function Declaration Style**](#4-Function-Declaration-Style)
5. [**Order of Layout**](#5-Order-of-Layout)

# Low Severity

## 1. assert Used Over require

`assert` should only be used in tests. Consider changing all occurrences of `assert` to `require` if not explicitly required. Prior to Solidity 0.8 `require` will refund all remaining gas whereas `assert` will not. Even after Solidity 0.8 [`assert` will result in a panic](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#panic-via-assert-and-error-via-require) which should not occur in production code. As stated in the Solidity Documentation: "[p]roperly functioning code should never create a Panic".

*/contracts/DefaultAccount.sol*

```solidity
225:	assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);
```

## 2. Consider SafeCast

As of Solidity 0.8 overflows are handled automatically; however, not for casting. For example `uint32(4294967300)` will result in `4` without reversion. Consider using OpenZepplin's SafeCast library. Even if variables do not seem like they can overflow, it is best practice to use a SafeCast.

*/contracts/ImmutableSimulator.sol*

```solidity
27:	return immutableDataStorage[uint256(uint160(_dest))][_index];
40:	immutableDataStorage[uint256(uint160(_dest))][index] = value;
```

*/contracts/MsgValueSimulator.sol*

```solidity
29:	to = address(uint160(addressAsUint));
60:	SystemContractHelper.setValueForNextFarCall(uint128(value));
```

*/contracts/AccountCodeStorage.sol*

```solidity
38:	uint256 addressAsKey = uint256(uint160(_address));
54:	uint256 addressAsKey = uint256(uint160(_address));
64:	uint256 addressAsKey = uint256(uint160(_address));
77:	address account = address(uint160(_input));
78:	if (uint160(account) <= CURRENT_MAX_PRECOMPILE_ADDRESS) {
105:	address account = address(uint160(_input));
117:	uint160(account) > CURRENT_MAX_PRECOMPILE_ADDRESS &&
```

*/contracts/L2EthToken.sol*

```solidity
65:	return balance[address(uint160(_account))];
```

*/contracts/KnownCodesStorage.sol*

```solidity
127:	uint8 version = uint8(_bytecodeHash[0]);
```

*/contracts/DefaultAccount.sol*

```solidity
83:	uint32(gasleft()),
141:	address to = address(uint160(_transaction.to));
```

*/contracts/ContractDeployer.sol*

```solidity
101:	bytes.concat(CREATE2_PREFIX, bytes32(uint256(uint160(_sender))), _salt, _bytecodeHash, constructorInputHash)
104:	newAddress = address(uint160(uint256(hash)));
120:	newAddress = address(uint160(uint256(hash)));
256:	require(uint160(_newAddress) > MAX_SYSTEM_CONTRACT_ADDRESS, "Can not deploy contracts in kernel space");
260:	ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.getCodeHash(uint256(uint160(_newAddress))) == 0x0,
320:	SystemContractHelper.setValueForNextFarCall(uint128(value));
```

*/contracts/BootloaderUtilities.sol*

```solidity
60:	bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));
67:	uint64 txDataLen = uint64(_transaction.data.length);
90:	uint256 vInt = uint256(uint8(_transaction.signature[64]));
115:	encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
146:	bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));
163:	uint64 txDataLen = uint64(_transaction.data.length);
189:	uint256 vInt = uint256(uint8(_transaction.signature[64]));
206:	encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
240:	bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));
258:	uint64 txDataLen = uint64(_transaction.data.length);
301:	encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
```

*/contracts/libraries/SystemContractHelper.sol*

```solidity
222:	gasPerPubdataByte = uint32(extractNumberFromMeta(meta, META_GAS_PER_PUBDATA_BYTE_OFFSET, 32));
232:	heapSize = uint32(extractNumberFromMeta(meta, META_HEAP_SIZE_OFFSET, 32));
241:	auxHeapSize = uint32(extractNumberFromMeta(meta, META_AUX_HEAP_SIZE_OFFSET, 32));
249:	shardId = uint8(extractNumberFromMeta(meta, META_SHARD_ID_OFFSET, 8));
258:	callerShardId = uint8(extractNumberFromMeta(meta, META_CALLER_SHARD_ID_OFFSET, 8));
267:	codeShardId = uint8(extractNumberFromMeta(meta, META_CODE_SHARD_ID_OFFSET, 8));
330:	return uint160(_address) <= uint160(MAX_SYSTEM_CONTRACT_ADDRESS);
```

*/contracts/libraries/Utils.sol*

```solidity
22:	return uint128(_x);
28:	return uint32(_x);
34:	return uint24(_x);
45:	codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
```

*/contracts/libraries/RLPEncoder.sol*

```solidity
64:	encoded[0] = bytes1(uint8(_offset + hbs + 56));
```

*/contracts/libraries/SystemContractsCaller.sol*

```solidity
82:	uint32 dataLength = uint32(Utils.safeCastToU32(data.length));
```

*/contracts/libraries/EfficientCall.sol*

```solidity
252:	SystemContractHelper.ptrShrinkIntoActive(uint32(msg.data.length));
260:	SystemContractHelper.ptrAddIntoActive(uint32(dataOffset));
262:	uint32 shrinkTo = uint32(msg.data.length - (_data.length + dataOffset));
```

*/contracts/libraries/TransactionHelper.sol*

```solidity
94:	return _addr == uint256(uint160(address(ETH_TOKEN_SYSTEM_CONTRACT))) || _addr == 0;
163:	bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));
170:	uint64 txDataLen = uint64(_transaction.data.length);
198:	encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
231:	bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));
248:	uint64 txDataLen = uint64(_transaction.data.length);
270:	encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
302:	bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));
320:	uint64 txDataLen = uint64(_transaction.data.length);
342:	encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
374:	address paymaster = address(uint160(_transaction.paymaster));
405:	if (address(uint160(_transaction.paymaster)) != address(0)) {
```

# Non-Critical

## 1. Contracts Missing Full NatSpec Header

Most contracts in scope do not have a fully annotated NatSpec contract header (`@title`, `@author`, etc. see [here](https://docs.soliditylang.org/en/v0.8.17/natspec-format.html) for reference). 39 out of 40 of the contracts in scope are missing a `@title` tag. 

[EmptyContract.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/EmptyContract.sol), [ImmutableSimulator.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ImmutableSimulator.sol), [L1Messenger.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L1Messenger.sol), [MsgValueSimulator.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/MsgValueSimulator.sol), [BytecodeCompressor.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol), [AccountCodeStorage.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol), [L2EthToken.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol), [SystemContext.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol), [KnownCodesStorage.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol), [NonceHolder.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol), [DefaultAccount.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol), [ContractDeployer.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol), [BootloaderUtilities.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol), [SystemContractHelper.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol), [UnsafeBytesCalldata.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/UnsafeBytesCalldata.sol), [Utils.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/Utils.sol), [RLPEncoder.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/RLPEncoder.sol), [SystemContractsCaller.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol), [EfficientCall.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/EfficientCall.sol), [Address.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/utils/Address.sol), [TransactionHelper.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol), [IL1Messenger.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IL1Messenger.sol), [IPaymasterFlow.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IPaymasterFlow.sol), [IBootloaderUtilities.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IBootloaderUtilities.sol), [IBytecodeCompressor.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IBytecodeCompressor.sol), [IAccountCodeStorage.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IAccountCodeStorage.sol), [IImmutableSimulator.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IImmutableSimulator.sol), [IL2StandardToken.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IL2StandardToken.sol), [IMailbox.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IMailbox.sol), [IKnownCodesStorage.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IKnownCodesStorage.sol), [IEthToken.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IEthToken.sol), [INonceHolder.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/INonceHolder.sol), [IERC20Permit.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol), [ISystemContext.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/ISystemContext.sol), [IERC20.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/token/ERC20/IERC20.sol), [IPaymaster.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IPaymaster.sol), [IAccount.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IAccount.sol), [IContractDeployer.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IContractDeployer.sol), and [Constants.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/Constants.sol) are missing a `@title` tag.

## 2. Inconsistent Named Returns

Some functions use named returns and others do not. It is best for code clearity to keep a consistent style.

1. The following contracts only have named returns (EX. `returns(uint256 foo)`): [L1Messenger.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L1Messenger.sol), [MsgValueSimulator.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/MsgValueSimulator.sol), [BytecodeCompressor.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol), [SystemContext.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol), [KnownCodesStorage.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol), [UnsafeBytesCalldata.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/UnsafeBytesCalldata.sol), and [SystemContractsCaller.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol).
2. The following contracts only have non-named returns (EX. `returns(uint256)`): [ImmutableSimulator.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ImmutableSimulator.sol), [L2EthToken.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol), and [Address.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/utils/Address.sol).
3. The following contracts have both: [AccountCodeStorage.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol), [NonceHolder.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol), [DefaultAccount.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol), [ContractDeployer.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol), [BootloaderUtilities.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol), [SystemContractHelper.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol), [Utils.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/Utils.sol), [RLPEncoder.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/RLPEncoder.sol), [EfficientCall.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/EfficientCall.sol), and [TransactionHelper.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol).

## 3. Spelling Mistake

*contracts/libraries/SystemContractHelper.sol*

* The word `returns` is misspelled as [`retuns`](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol#L318).

## 4. Underscore Notation Not Used or Not Used Consistently

Consider using underscore notation to help with contract readability (Ex. `23453` -> `23_453`).

*/contracts/SystemContext.sol*

```solidity
40:	uint256 public difficulty = 2500000000000000;
```

## 5. bytes.concat() Can Be Used Over abi.encodePacked()

Consider using `bytes.concat()` instead of `abi.encodePacked()` in contracts with Solidity version >= 0.8.4.

*/contracts/L2EthToken.sol*

```solidity
98:	return abi.encodePacked(IMailbox.finalizeEthWithdrawal.selector, _to, _amount);
```

*/contracts/libraries/TransactionHelper.sol*

```solidity
132:	keccak256(abi.encodePacked(_transaction.factoryDeps)),
141:	return keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));
```

## 6. Space Between License and Pragma

Almost all contracts in scope have a space between the license statement and pragma ([ex](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L2)). Although it does not void the [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html), all example contracts in the Style Guide do not have a space between the license statement and pragma ([ex](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#blank-lines)). This format can also be seen throughout the Solidy documentation ([ex](https://docs.soliditylang.org/en/v0.8.17/natspec-format.html)). Consider removing the needless space.

It is also good to note the following [contract](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/Utils.sol#L2) does not have a space between the license and pragma. At the very least all contracts should have a consistant style. 

## 7. Inconsistent Trailing Period

Some comments like [this](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L59) have a trailing `.` but other lines like [this](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol#L49) do not.

Consider sticking to a single comment style for a less distracting developer experience.

## 8. ERC20 Token Recovery

Consider adding a recovery function for Tokens that are accidently sent to the core contracts of the protocol. 

## 9. Gas Greif If Relayed

Although it may not be possible in the current system design, it is good to note a possible gas greif if transactions are relayed. 

Even if the `returnData` is left of of the following statement `(bool success, ) = target.call()`,  `returnData` is still copied to memory. This can lead to a gas greif if relayed. The code [here](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/utils/Address.sol#L66), and [here](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L36) use `(bool success, ) = target.call()`.

For more information, see [this](https://twitter.com/pashovkrum/status/1607024043718316032?t=xs30iD6ORWtE2bTTYsCFIQ&s=19) post.

## 10. Lack of Interface NatSpec

The [Solidity documentation](https://docs.soliditylang.org/en/v0.8.17/natspec-format.html#natspec-format) states: 

> [I]t is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI).

[Some](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IEthToken.sol) interfaces have no NatSpec documentation, [some](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IL1Messenger.sol) have partial documentation, and [some](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/INonceHolder.sol) have full documentation (still missing `@title`). Consider adding full NatSpec documentation to all interfaces.

## 11. Inconsistent NatSpec Style

There is a general inconsistency with the way NatSpec comments are formatted. A NatSpec comment [here](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/INonceHolder.sol#L23) is single line and uses `//`. [This](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/IERC20.sol#L25) NatSpec comment is a single line, but uses the multi-line `/**`. Although both `//` and `/**` are valid NatSpec notation, it is best to keep to a single comment style.

## 12. NatSpec Comments Without Tag

The [Solidity documentation](https://docs.soliditylang.org/en/v0.8.19/natspec-format.html#tags) states: 

> "[I]f no tags are used then the Solidity compiler will interpret a /// or /** comment in the same way as if it were tagged with @notice".

There are some instances ([ex1](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IContractDeployer.sol#L66), [ex2](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IContractDeployer.sol#L67), [ex3](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IContractDeployer.sol#L75), ...) where no NatSpec tag is specified. It is best to be explicit with NatSpec tags. 

# Style Guide Violations

## 1. Maximum Line Length

Lines with greater length than 120 characters are used. The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) suggests that all lines should be 120 characters or less in width.

The following lines are longer than 120 characters, it is suggested to shorten these lines:

*contracts/AccountCodeStorage.sol*
*  [13](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol#L13), [16](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol#L16). 

*contracts/L2EthToken.sol*
*  [6](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L6), [13](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L13). 

*contracts/KnownCodesStorage.sol*
*  [14](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol#L14), [88](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol#L88). 

*contracts/NonceHolder.sol*
*  [23](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L23). 

*contracts/DefaultAccount.sol*
*  [9](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L9). 

*contracts/ContractDeployer.sol*
*  [7](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L7). 

*contracts/BootloaderUtilities.sol*
*  [20](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L20). 

*contracts/libraries/SystemContractHelper.sol*
*  [105](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L105). 

*contracts/libraries/UnsafeBytesCalldata.sol*
*  [8](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/UnsafeBytesCalldata.sol#L8). 

*contracts/libraries/Utils.sol*
*  [49](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/Utils.sol#L49). 

*contracts/libraries/EfficientCall.sol*
*  [15](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/EfficientCall.sol#L15), [24](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/EfficientCall.sol#L24), [248](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/EfficientCall.sol#L248). 

*contracts/openzeppelin/utils/Address.sol*
*  [58](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/utils/Address.sol#L58), [82](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin/utils/Address.sol#L82). 

*contracts/libraries/TransactionHelper.sol*
*  [85](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L85), [290](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L290). 

## 2. Order of Functions

The Solidity Style Guide suggests the following function order: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private.

The following contracts are not compliant (examples are only to prove the functions are out of order NOT a full description): 

* [AccountCodeStorage.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol): external functions are positioned after public functions.
* [L2EthToken.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol): external functions are positioned after internal functions.
* [SystemContext.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol): external functions are positioned after public functions.
* [KnownCodesStorage.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol): public functions are positioned after internal functions.
* [NonceHolder.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol): external functions are positioned after public functions.
* [DefaultAccount.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol): external functions are positioned after internal functions.
* [ContractDeployer.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol): external functions are positioned after internal functions.
* [EfficientCall.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/EfficientCall.sol): internal functions are positioned after private functions.
* [TransactionHelper.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol): internal functions are positioned after private functions.

## 3. Whitespace in Expressions

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#whitespace-in-expressions) recommends to *"[a]void extraneous whitespace [i]mmediately inside parenthesis, brackets or braces, with the exception of single line function declarations"*. The Style Guide also recommends against spaces before semicolons. Consider removing spaces before semicolons.

*/contracts/MsgValueSimulator.sol*

```solidity
36:	(bool success, ) = address(ETH_TOKEN_SYSTEM_CONTRACT).call(
```

*/contracts/SystemContext.sol*

```solidity
92:	(blockNumber, ) = getBlockNumberAndTimestamp();
```

*/contracts/NonceHolder.sol*

```solidity
126:	(deploymentNonce, ) = _splitRawNonce(rawNonces[addressAsKey]);
143:	(prevDeploymentNonce, ) = _splitRawNonce(oldRawNonce);
```

## 4. Function Declaration Style

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#function-declaration) states that "[f]or short function declarations, it is recommended for the opening brace of the function body to be kept on the same line as the function declaration". It is not clear what length is exactly meant by "short" or "long". The [maximum line length](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) of 120 characters may be an indication, and in that case any expanded function declaration under 120 characters should be on a single line.

The following functions in their respective contracts are not compliant by this standard: 

*contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol*
* [`safeTransfer`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L22), [`safeTransferFrom`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L33), [`safeApprove`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L52), [`safeIncreaseAllowance`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L70), [`safeDecreaseAllowance`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L86). 

*contracts/openzeppelin/utils/Address.sol*
* [`functionCall`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/utils/Address.sol#L91), [`functionCallWithValue`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/utils/Address.sol#L129), [`functionStaticCall`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/utils/Address.sol#L177), [`functionDelegateCall`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/utils/Address.sol#L217), [`_revert`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/utils/Address.sol#L292). 

## 5. Order of Layout

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-layout) suggests the following contract layout order: Type declarations, State variables, Events, Modifiers, Functions.

The following contracts are not compliant (examples are only to prove the layout are out of order NOT a full description): 

* [SystemContext.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol): State is positioned after Modifier.
* [ContractDeployer.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol): State is positioned after Functions.
* [IEthToken.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IEthToken.sol): Events are positioned after Functions.
