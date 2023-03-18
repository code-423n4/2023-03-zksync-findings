
### Table of Contents
- [L-1: `heapSize` and `auxHeapSize` of `ZkSyncMeta` returned by `getZkSyncMeta()` are always `0`](#l-1-heapsize-and-auxheapsize-of-zksyncmeta-returned-by-getzksyncmeta-are-always-0)
- [L-2: Some variables are not cleaned up before `call` in inline assembler](#l-2-some-variables-are-not-cleaned-up-before-call-in-inline-assembler)
- [L-3: The amount of gas burned by `precompileCall` is slightly unfair depending on the amount of `gasleft()`](#l-3-the-amount-of-gas-burned-by-precompilecall-is-slightly-unfair-depending-on-the-amount-of-gasleft)
- [L-4: No `require` in `increaseMinNonce` when `nonceOrdering` is `Sequential`](#l-4-no-require-in-increaseminnonce-when-nonceordering-is-sequential)
- [NC-1: The `baseFee` description `SystemContext` is incorrect](#nc-1-the-basefee-description-systemcontext-is-incorrect)
- [NC-2: The description of call flags in `getCallFlags` is incorrect](#nc-2-the-description-of-call-flags-in-getcallflags-is-incorrect)
- [NC-3: Unify whether to add `override` to interface functions](#nc-3-unify-whether-to-add-override-to-interface-functions)
- [NC-4: Whether or not `_value > 0` should be checked in `increaseMinNonce`](#nc-4-whether-or-not-_value--0-should-be-checked-in-increaseminnonce)
- [NC-5: Whether or not `msg.value > 0` should be checked in `withdraw` of `L2EthToken`](#nc-5-whether-or-not-msgvalue--0-should-be-checked-in-withdraw-of-l2ethtoken)
- [NC-6 (out of scope): CALL param 0 in `extra_abi_data` in VM specs is incorrect](#nc-6-out-of-scope-call-param-0-in-extra_abi_data-in-vm-specs-is-incorrect)

### L-1: `heapSize` and `auxHeapSize` of `ZkSyncMeta` returned by `getZkSyncMeta()` are always `0`

In `getZkSyncMeta()` (https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol#L198), `heapSize` and `auxHeapSize` are not getting.

```
    function getZkSyncMeta() internal view returns (ZkSyncMeta memory meta) {
        uint256 metaPacked = getZkSyncMetaBytes();
        meta.gasPerPubdataByte = getGasPerPubdataByteFromMeta(metaPacked);
        meta.shardId = getShardIdFromMeta(metaPacked);
        meta.callerShardId = getCallerShardIdFromMeta(metaPacked);
        meta.codeShardId = getCodeShardIdFromMeta(metaPacked);
    }
```

The following two lines must be added.

```
meta.heapSize = getHeapSizeFromMeta(metaPacked);
meta.auxHeapSize = getAuxHeapSizeFromMeta(metaPacked);
```

Fortunately, `getZkSyncMeta` is not used by any system contract and does not lead to direct attacks. In the future, it must be fixed as it may cause attacks or failures when `getZkSyncMeta` is used.


### L-2: Some variables are not cleaned up before `call` in the inline assembler

The following cleanup is done for the function arguments that are less than 32 bytes. However, some variables are not cleaned up.

```solidity
// Clearing values before usage in assembly, since Solidity doesn't do it by default
whoToMimic := and(whoToMimic, cleanupMask)
```

List of locations where cleanups appear:
- https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol#L201
- https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol#L52
- https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol#L99
- https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol#L156
- https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol#L169

List of variables that are not cleaned up:
- https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol#L131
	- `_address` is `address` type and not cleaned up.
- https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol#L144
	- `_address` is `address` type and not cleaned up.
- https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol#L159
	- `_address` is `address` type and not cleaned up.
- https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol#L173
	- `_address` is `address` type and not cleaned up.
- https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol#L203
	- `_address` is `address` type and not cleaned up.
- https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractsCaller.sol#L100
	- `to` is `address` type and not cleaned up.
- https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractsCaller.sol#L109
	- `to` is `address` type and not cleaned up.

However, in general, manual cleanup as above is not required for Ethereum because solc (not zksolc) cleans up function arguments that are stacked on the stack ([ref](https://docs.soliditylang.org/en/latest/ internals/variable_cleanup.html)). For example, if the following function with a variable of `address` type as an argument is compiled with solc and the mnemonics are checked, it can be confirm that it is cleaned up. The same is true for other types (`uintN`, `bool`, etc.).

```Solidity
contract A {
    function f(address x) public pure {}
}
```

The mnemonics: 
```
...
0x050: PUSH20 0xffffffffffffffffffffffffffffffffffffffff
0x065: DUP3
0x066: AND
...
```

We can see that the mask is pushed onto the stack with the `PUSH20` opcode and cleaned up with the `AND` opcode.

Hence, the cleanup processes present in the system contracts seem necessary for the zkEVM/zksolc specifications (registers, memory management, etc.). (If they are unnecessarily doing cleanup, they should be removed).

Therefore, depending on the zkEVM/zksolc specification, the variables listed earlier that are not cleaned up should be done. Also, if these do not need to be cleaned up, it would be better to clearly state in the comments why they do not need to be cleaned up.

### L-3: The amount of gas burned by `precompileCall` is slightly unfair depending on the amount of `gasleft()`

In https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol#L152, it is more fair to put `require(gasleft() >= _gasToBurn);` after `uint256 cleanupMask = UINT32_MASK;`.

If gas is sufficient, the `require` statement consumes `_gasToBurn` + α no matter where it is placed. However, if gas is just barely enough to meet `gasleft() >= _gasToBurn`, then for example, if `gasleft()` is just `_gasToBrun`, it is lower because the execution cost of `uint256 cleanupMask = UINT32_MASK;` can be ignored.

### L-4: No `require` in `increaseMinNonce` when `nonceOrdering` is `Sequential`


In `setValueUnderNonce` (https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L81), as follows, when `nonceOrdering` is sequential, the check exists to see if the previous nonce is used.

```
if (accountInfo.nonceOrdering == IContractDeployer.AccountNonceOrdering.Sequential && _key != 0) {
	require(isNonceUsed(msg.sender, _key - 1), "Previous nonce has not been used");
}
```

On the other hand, in `increaseMinNonce` (https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L64), since there is no check when `nonceOrdering` is sequential, it is possible to increase nonce by two or more despite sequential.

However, as the following statement on https://github.com/code-423n4/2023-03-zksync/blob/main/README.md?plain=1#L641 says, It might be better to remove the check on the `setValueUnderNonce` .

```
This ordering is not enforced in any way by system contracts, but it is more of a suggestion to the operator on how it should order the transactions in the mempool.
```

Anyway, it should be consistent whether `nonceOrdering` is checked or not.

### NC-1: The `baseFee` description `SystemContext` is incorrect

At https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol#L43, they say that `baseFee` is a constant. On the other hand, https://github.com/code-423n4/2023-03-zksync/blob/main/README.md?plain=1#L327 says that `baseFee` is given by the operator and will be hard-coded (constant) in the future. In other words, it is currently dynamic, not constant.

Also, in `bootloader.yul`, it can be confirmed that `baseFee` is calculated dynamically and `baseFee` is not a constant, as shown below.

```
let baseFee, GAS_PRICE_PER_PUBDATA := getBaseFee(L1_GAS_PRICE, FAIR_L2_GAS_PRICE)
```

```
/// @dev Returns the baseFee for this block based on the
/// L1 gas price and the fair L2 gas price.
function getBaseFee(l1GasPrice, fairL2GasPrice) -> baseFee, gasPricePerPubdata {
	// By default, we want to provide the fair L2 gas price.
	// That it means that the operator controls
	// what the value of the baseFee will be. In the future, 
	// a better system, aided by EIP1559 should be added. 

	let pubdataBytePriceETH := safeMul(l1GasPrice, L1_GAS_PER_PUBDATA_BYTE(), "aoa")

	baseFee := max(
		fairL2GasPrice,
		ceilDiv(pubdataBytePriceETH, MAX_L2_GAS_PER_PUBDATA())
	)
	gasPricePerPubdata := ceilDiv(pubdataBytePriceETH, baseFee)
}
```

Therefore, the comment is incorrect and needs to be changed.

### NC-2: The description of call flags in `getCallFlags` is incorrect

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol#L282 and https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/EventWriter.yul#L49 say `Call flags are the value of the first register at the start of the call.`, but, as described in  https://github.com/code-423n4/2023-03-zksync/blob/main/README.md?plain=1#L156 , call flags are in `r2`. Therefore the comment is incorrect and needs to be changed to `the second register`.

### NC-3: Unify whether to add `override` to interface functions

Since Solidity 0.8.8, the  `override` keyword is optional for interface functions ([ref](https://blog.soliditylang.org/2021/09/27/solidity-0.8.8-release-announcement/)). The `NonceHolder` functions do not have it (https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L45), while the `AccountCodeStorage` functions do (https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/AccountCodeStorage.sol#L34). It can be added or not, but it would be easier to understand if it is not added to all functions or added to all functions.

### NC-4: Whether or not `_value > 0` should be checked in `increaseMinNonce`

`increaseMinNonce` (https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L64) can be executed even if `_value` is 0. It should be clearly commented on whether it contains `0` or not.

### NC-5: Whether or not `msg.value > 0` should be checked in `withdraw` of `L2EthToken`

`withdraw` (https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L80
) can be executed even if `msg.value` is 0. It should be clearly commented on whether it contains `0` or not.

### NC-6 (out of scope): CALL param 0 in `extra_abi_data` in VM specs is incorrect

In https://github.com/code-423n4/2023-03-zksync/blob/main/docs/VM-specific_v1.3.0_opcodes_simulation.pdf, `CALL param 0 (gas)` in `extra_abi_data` is 0, but the correct value is an index.

