**Overview**

Risk Rating | Number of issues
--- | ---
Low Risk | 8 (+ 1 OOS mitigation)
Non-Critical Risk | 13

**Table of Contents**

- [1. Low Risk Issues](#1-low-risk-issues)
  - [1.1. The `value > MAX_MSG_VALUE` check in `MsgValueSimulator.fallback()` should be made before the `value != 0` check](#11-the-value--max_msg_value-check-in-msgvaluesimulatorfallback-should-be-made-before-the-value--0-check)
  - [1.2. Add `verifyingContract` to `EIP712_DOMAIN_TYPEHASH`](#12-add-verifyingcontract-to-eip712_domain_typehash)
  - [1.3. `BytecodeCompressor.publishCompressedBytecode()` can receive funds](#13-bytecodecompressorpublishcompressedbytecode-can-receive-funds)
  - [1.4. No event emitted when updating a state variable](#14-no-event-emitted-when-updating-a-state-variable)
  - [1.5. Lack of a `L2EthToken.burn` method only callable by the Bootloader](#15-lack-of-a-l2ethtokenburn-method-only-callable-by-the-bootloader)
  - [1.6. Unjustified `unchecked`](#16-unjustified-unchecked)
  - [1.7. Create an `onlyDeployerSystemContract` modifier and avoid a missing a friendly revert string](#17-create-an-onlydeployersystemcontract-modifier-and-avoid-a-missing-a-friendly-revert-string)
  - [1.8. `modifier onlyBootloader` is declared at 3 places and is missing a friendly revert string at one](#18-modifier-onlybootloader-is-declared-at-3-places-and-is-missing-a-friendly-revert-string-at-one)
  - [1.9. (OOS) Discouraged use of `safeApprove`. Use `safeIncreaseAllowance` here instead](#19-oos-discouraged-use-of-safeapprove-use-safeincreaseallowance-here-instead)
- [2. Non-Critical Issues](#2-non-critical-issues)
  - [2.1. Offsets for `META_HEAP_SIZE_OFFSET` are excessively confusing](#21-offsets-for-meta_heap_size_offset-are-excessively-confusing)
  - [2.2. No need to check that `v == 27 || v == 28` with `ecrecover`](#22-no-need-to-check-that-v--27--v--28-with-ecrecover)
  - [2.3. Tautology when checking `recoveredAddress`](#23-tautology-when-checking-recoveredaddress)
  - [2.4. Document the mechanism under `SystemContext.getBlockNumberAndTimestamp()`](#24-document-the-mechanism-under-systemcontextgetblocknumberandtimestamp)
  - [2.5. Document why all fields under `ZkSyncMeta` aren't returned in `SystemContractHelper.getZkSyncMeta()`](#25-document-why-all-fields-under-zksyncmeta-arent-returned-in-systemcontracthelpergetzksyncmeta)
  - [2.6. `require()` should be used for checking error conditions on inputs and return values while `assert()` should be used for invariant checking](#26-require-should-be-used-for-checking-error-conditions-on-inputs-and-return-values-while-assert-should-be-used-for-invariant-checking)
  - [2.7. Typos](#27-typos)
  - [2.8. Consider only declaring 1 contract, or 1 library, or 1 interface, or 1 abstract contract in a file](#28-consider-only-declaring-1-contract-or-1-library-or-1-interface-or-1-abstract-contract-in-a-file)
  - [2.9. Default Visibility](#29-default-visibility)
  - [2.10. Adding a `return` statement when the function defines a named return variable, is redundant](#210-adding-a-return-statement-when-the-function-defines-a-named-return-variable-is-redundant)
  - [2.11. `2500000000000000` should be changed to `2.5e15` for readability reasons](#211-2500000000000000-should-be-changed-to-25e15-for-readability-reasons)
  - [2.12. Use `string.concat()` or `bytes.concat()` along with a more recent version of Solidity](#212-use-stringconcat-or-bytesconcat-along-with-a-more-recent-version-of-solidity)
  - [2.13. Import declarations should import specific identifiers, rather than the whole file](#213-import-declarations-should-import-specific-identifiers-rather-than-the-whole-file)

# 1. Low Risk Issues

## 1.1. The `value > MAX_MSG_VALUE` check in `MsgValueSimulator.fallback()` should be made before the `value != 0` check

*Category: Incorrect Function*

The [fallback](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/MsgValueSimulator.sol#L32-L63) method first checks that `value != 0` before executing code with it and then checks for the `value > MAX_MSG_VALUE` revert condition. These checks should be swapped for the logic of the `fallback` to be right:

```diff
    fallback(bytes calldata _data) external payable onlySystemCall returns (bytes memory) {
        (uint256 value, bool isSystemCall, address to) = _getAbiParams();

+        if (value > MAX_MSG_VALUE) {
+            // The if above should never be true, since noone should be able to have
+            // MAX_MSG_VALUE wei of ether. However, if it does happen for some reason,
+            // we will revert(0,0).
+            // Note, that we use raw revert here instead of `panic` to emulate behaviour close to
+            // the EVM's one, i.e. returndata should be empty.
+            assembly {
+                return(0, 0)
+            }
+        }
+
        if (value != 0) {
            (bool success, ) = address(ETH_TOKEN_SYSTEM_CONTRACT).call(
                abi.encodeCall(ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo, (msg.sender, to, value))
            );

            // If the transfer of ETH fails, we do the most Ethereum-like behaviour in such situation: revert(0,0)
            if (!success) {
                assembly {
                    revert(0, 0)
                }
            }
        }

-        if (value > MAX_MSG_VALUE) { //@audit-issue low: this should be above, before the `value != 0` check
-            // The if above should never be true, since noone should be able to have
-            // MAX_MSG_VALUE wei of ether. However, if it does happen for some reason,
-            // we will revert(0,0).
-            // Note, that we use raw revert here instead of `panic` to emulate behaviour close to
-            // the EVM's one, i.e. returndata should be empty.
-            assembly {
-                return(0, 0)
-            }
-        }

        // For the next call this `msg.value` will be used.
        SystemContractHelper.setValueForNextFarCall(uint128(value));

        return EfficientCall.mimicCall(gasleft(), to, _data, msg.sender, false, isSystemCall);
    }
```

## 1.2. Add `verifyingContract` to `EIP712_DOMAIN_TYPEHASH`

*Category: Known protection against attacks*

- [EIP712](https://eips.ethereum.org/EIPS/eip-712#definition-of-domainseparator)
- [EthVigil note about domain separator](https://ethvigil.com/docs/eip712_sign_example_code/#a-note-about-domain_separator)

Usually, as a protection to phishing attacks, there's an `address verifyingContract` being used when defining the EIP-712 typehash:

```diff
File: TransactionHelper.sol
80:     /// @notice The EIP-712 typehash for the contract's domain
- 81:     bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");
+ 81:     bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");
```

And quite often, the `verifyingContract` is a simple `address(this)`:

```diff
File: TransactionHelper.sol
137:         bytes32 domainSeparator = keccak256(
- 138:             abi.encode(EIP712_DOMAIN_TYPEHASH, keccak256("zkSync"), keccak256("2"), block.chainid)
+ 138:             abi.encode(EIP712_DOMAIN_TYPEHASH, keccak256("zkSync"), keccak256("2"), block.chainid, address(this))
139:         );
```

If adding a `verifyingContract` isn't an option for some reason, consider documenting why.

## 1.3. `BytecodeCompressor.publishCompressedBytecode()` can receive funds

*Category: State handling*

[BytecodeCompressor.sol#L38](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L38) is `payable` for no apparent reason, so the contract can receive funds and there are no apparent mechanism to retrieve them. If `payable` is really relevant and the contract is expected to be able to receive funds, consider documenting it, otherwise it's possible that funds could be locked (no immediately visible/apparent/documented ways to retrieve funds).

## 1.4. No event emitted when updating a state variable

*Category: State handling*

The [increaseMinNonce](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L64-L75), [incrementMinNonceIfEquals](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L109-L119) and [incrementDeploymentNonce](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L134-L144) methods in [NonceHolder](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol) don't emit events.

Not emitting events when changing state variables is a bad practice and can be seen as a trust issue

## 1.5. Lack of a `L2EthToken.burn` method only callable by the Bootloader

*Category: State handling*

While there exist a [L2EthToken.withdraw](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L80-L94) method that silently burns tokens, it's quite possible to `mint` more token that could ever be `withdraw`n due to the [presence of `msg.value`](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L81). While this would be self-sabotage or a mistake in the Bootloader, it still means that burning tokens is a lot more difficult than minting it. Consider adding a `L2EthToken.burn` method only callable by the Bootloader.

## 1.6. Unjustified `unchecked`

*Category: Issues with comments*

The [following](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L83-L87) is not documented and it's not obvious to understand why it could justified. Given how `unchecked` blocks can be dangerous, the lack of comment here is a real issue:

```solidity
File: L2EthToken.sol
83:         // Silent burning of the ether
84:         unchecked {
85:             balance[address(this)] -= amount;
86:             totalSupply -= amount;
87:         }
```

## 1.7. Create an `onlyDeployerSystemContract` modifier and avoid a missing a friendly revert string

*Category: Issues with comments*

This `require` statement is copy-pasted many times and is even [missing a revert string](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L135) in one of the copy-pastes. Consider creating a `modifier` that can be imported and used just like [onlySystemCall](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractHelper.sol#L334-L346) for the following:

```solidity
contracts/AccountCodeStorage.sol:
  25:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

contracts/ImmutableSimulator.sol:
  34:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

contracts/NonceHolder.sol:
  135:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "");
```

## 1.8. `modifier onlyBootloader` is declared at 3 places and is missing a friendly revert string at one

*Category: Issues with comments*

The `modifier onlyBootloader` is copy-pasted many times and is even [missing a revert string](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L16) in one of the copy-pastes. Consider doing just like [onlySystemCall](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractHelper.sol#L334-L346) for the following:

```solidity
contracts/KnownCodesStorage.sol:
  18:     modifier onlyBootloader() {
  19          require(msg.sender == BOOTLOADER_FORMAL_ADDRESS, "Callable only by the bootloader");
  20          _;
  21      }

contracts/L2EthToken.sol:
  28:     modifier onlyBootloader() {
  29          require(msg.sender == BOOTLOADER_FORMAL_ADDRESS, "Callable only by the bootloader");
  30          _;
  31      }

contracts/SystemContext.sol:
  15:     modifier onlyBootloader() {
  16          require(msg.sender == BOOTLOADER_FORMAL_ADDRESS);
  17          _;
  18      }
```

## 1.9. (OOS) Discouraged use of `safeApprove`. Use `safeIncreaseAllowance` here instead

*Category: Use of a deprecated function*

*This finding is out of scope due to [4naly3er](https://gist.github.com/Picodes/4fda93fac0db99cf9f2e260a073b38e4#l-2-do-not-use-deprecated-library-functions), therefore only some additional information are being provided here*

`safeApprove` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/1a60b061d5bb809c3d7e4ee915c77a00b1eca95d/contracts/token/ERC20/utils/SafeERC20.sol#L39-L45) by OpenZeppelin but the mention also exists in the [project itself](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L45-L59):

```solidity
File: 2023-03-zksync/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol
45:     /**
46:      * @dev Deprecated. This function has issues similar to the ones found in
47:      * {IERC20-approve}, and its usage is discouraged.
48:      *
49:      * Whenever possible, use {safeIncreaseAllowance} and
50:      * {safeDecreaseAllowance} instead.
51:      */
52:     function safeApprove(
53:         IERC20 token,
54:         address spender,
55:         uint256 value
56:     ) internal {
57:         // safeApprove should only be called when setting an initial allowance,
58:         // or when resetting it to zero. To increase and decrease it, use
59:         // 'safeIncreaseAllowance' and 'safeDecreaseAllowance'
60:         require(
```

Given how it's used, `safeIncreaseAllowance` is what's expected [here](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L381-L382):

```diff
File: TransactionHelper.sol
376:             uint256 currentAllowance = IERC20(token).allowance(address(this), paymaster);
377:             if (currentAllowance < minAllowance) {
- 378:                 // Some tokens, e.g. USDT require that the allowance is firsty set to zero
- 379:                 // and only then updated to the new value.
- 380: 
- 381:                 IERC20(token).safeApprove(paymaster, 0);
- 382:                 IERC20(token).safeApprove(paymaster, minAllowance);
+ 378:                 IERC20(token).safeIncreaseAllowance(paymaster, minAllowance - currentAllowance);
383:             }
```

# 2. Non-Critical Issues

## 2.1. Offsets for `META_HEAP_SIZE_OFFSET` are excessively confusing

Given [`struct ZkSyncMeta`](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractHelper.sol#L16-L23):

```solidity
File: SystemContractHelper.sol
16: struct ZkSyncMeta {
17:     uint32 gasPerPubdataByte;
18:     uint32 heapSize;
19:     uint32 auxHeapSize;
20:     uint8 shardId;
21:     uint8 callerShardId;
22:     uint8 codeShardId;
23: }
```

We should be able to understand the different offset values of the constants under [`SystemContractsCaller.sol`](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractsCaller.sol#L40-L46):

```solidity
File: SystemContractsCaller.sol
40: // All the offsets are in bits
41: uint256 constant META_GAS_PER_PUBDATA_BYTE_OFFSET = 0 * 8;
42: uint256 constant META_HEAP_SIZE_OFFSET = 8 * 8;
43: uint256 constant META_AUX_HEAP_SIZE_OFFSET = 12 * 8;
44: uint256 constant META_SHARD_ID_OFFSET = 28 * 8;
45: uint256 constant META_CALLER_SHARD_ID_OFFSET = 29 * 8;
46: uint256 constant META_CODE_SHARD_ID_OFFSET = 30 * 8;
```

However, they are not following what could've been expected, meaning:

```diff
File: SystemContractsCaller.sol
40: // All the offsets are in bits
41: uint256 constant META_GAS_PER_PUBDATA_BYTE_OFFSET = 0 * 8;
- 42: uint256 constant META_HEAP_SIZE_OFFSET = 8 * 8;
+ 42: uint256 constant META_HEAP_SIZE_OFFSET = 4 * 8;
- 43: uint256 constant META_AUX_HEAP_SIZE_OFFSET = 12 * 8;
+ 43: uint256 constant META_AUX_HEAP_SIZE_OFFSET = 8 * 8;
- 44: uint256 constant META_SHARD_ID_OFFSET = 28 * 8;
+ 44: uint256 constant META_SHARD_ID_OFFSET = 9 * 8;
- 45: uint256 constant META_CALLER_SHARD_ID_OFFSET = 29 * 8;
+ 45: uint256 constant META_CALLER_SHARD_ID_OFFSET = 10 * 8;
- 46: uint256 constant META_CODE_SHARD_ID_OFFSET = 30 * 8;
+ 46: uint256 constant META_CODE_SHARD_ID_OFFSET = 11 * 8;
```

This is due to the following [rust code](https://github.com/matter-labs/era-zkevm_opcode_defs/blob/main/src/definitions/abi/meta.rs#L14) which is present in the contest's `README` but not in the project itself.

```rust
    pub const fn to_u256(self) -> U256 {
        let mut result = U256::zero();
        result.0[0] = self.ergs_per_pubdata_byte as u64;
        result.0[1] = (self.heap_size as u64) | ((self.aux_heap_size as u64) << 32);

        let tmp = (self.this_shard_id as u64)
            | ((self.caller_shard_id as u64) << 8)
            | ((self.code_shard_id as u64) << 16);
        result.0[3] = tmp << 32;

        result
    }
```

As offsets can be confusing and not trivial to get right, thoroughly documenting why such offset has such value would be a good addition for future auditors and bug bounty hunters.

With the Rust code, my own understanding here is that the offsets are indeed as they should be:

```solidity
File: SystemContractsCaller.sol
40: // All the offsets are in bits
41: uint256 constant META_GAS_PER_PUBDATA_BYTE_OFFSET = 0 * 8;  //@audit [0] : u64 => +8
42: uint256 constant META_HEAP_SIZE_OFFSET = 8 * 8; //@audit [1] u64 => +8
43: uint256 constant META_AUX_HEAP_SIZE_OFFSET = 12 * 8;//@audit [1] u64 << 32 => +4
44: uint256 constant META_SHARD_ID_OFFSET = 28 * 8; //@audit [2]: missing and filling the gap on the 32 bytes and [3] calculated backward from << 32 => 32 - 4 == 28
45: uint256 constant META_CALLER_SHARD_ID_OFFSET = 29 * 8; //@audit << 8 => +1
46: uint256 constant META_CODE_SHARD_ID_OFFSET = 30 * 8; //@audit << 16 => +2
```

## 2.2. No need to check that `v == 27 || v == 28` with `ecrecover`

This [Tweeter thread from Alex Beregszaszi aka Axic](https://twitter.com/alexberegszaszi/status/1534461421454606336?s=20&t=H0Dv3ZT2bicx00hLWJk7Fg) explains it well and the [Yellow Paper has been changed](https://pbs.twimg.com/media/FUu--C-XEAELc2A?format=jpg&name=large) in direct consequence to it.

> The Yellow Paper makes it look like nothing is checked.

> Digging into the clients, it turns out the precompile actually checks if the value is 27 or 28. No need to do this on the caller side!

> The change has been merged into the Yellow Paper

Additionally, the input validation is also already made in the project's [Ecrecover.yul#L60-L61](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/precompiles/Ecrecover.yul#L60-L61):

```solidity
2023-03-zksync/contracts/precompiles/Ecrecover.yul
60:             // Validate the input by the yellow paper rules (Appendix E. Precompiled contracts)
61:             let vIsInvalid := iszero(or(eq(v, 27), eq(v, 28)))
```

Therefore, the check can be deleted [here](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L176):

```solidity
File: DefaultAccount.sol
176:         require(v == 27 || v == 28, "v is neither 27 nor 28");
```

## 2.3. Tautology when checking `recoveredAddress`

While taking into account the `address(0)` output of `ecrecover` is something that must always be done: in this particular case the check is a [tautology](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L191):

```solidity
File: DefaultAccount.sol
189:         address recoveredAddress = ecrecover(_hash, v, r, s);  
190: 
191:         return recoveredAddress == address(this) && recoveredAddress != address(0);  
```

Indeed:

- if `recoveredAddress == address(this)` is true, then `recoveredAddress != address(0)` will always be true
- if `recoveredAddress == address(this)` is false, then `recoveredAddress != address(0)` will never be evaluated

## 2.4. Document the mechanism under `SystemContext.getBlockNumberAndTimestamp()`

This mechanism being a clever use of maths, consider adding a comment on the fact that this will work until around year 10^27 (which is way after the Big Freeze / death of all life in the universe), which corresponds to a timestamp `< 2**128`:

```solidity
File: SystemContext.sol
81:     /// @notice Returns the current blocks' number and timestamp.
82:     /// @return blockNumber and blockTimestamp tuple of the current block's number and the current block's timestamp
83:     function getBlockNumberAndTimestamp() public view returns (uint256 blockNumber, uint256 blockTimestamp) {
84:         uint256 blockInfo = currentBlockInfo;
85:         blockNumber = blockInfo / BLOCK_INFO_BLOCK_NUMBER_PART;
86:         blockTimestamp = blockInfo % BLOCK_INFO_BLOCK_NUMBER_PART; //@audit-issue This mechanism being a clever use of maths, add a comment that this will work until around year 10^27 (which is way after the Big Freeze / death of all life in the universe) 
87:     }
```

## 2.5. Document why all fields under `ZkSyncMeta` aren't returned in `SystemContractHelper.getZkSyncMeta()`

This is the `ZkSyncMeta` struct:

```solidity
File: SystemContractHelper.sol
16: struct ZkSyncMeta {
17:     uint32 gasPerPubdataByte;
18:     uint32 heapSize;
19:     uint32 auxHeapSize;
20:     uint8 shardId;
21:     uint8 callerShardId;
22:     uint8 codeShardId;
23: }
```

This is the `ZkSyncMeta` getter:

```solidity
File: SystemContractHelper.sol
270:     /// @notice Retrieves the ZkSyncMeta structure.
271:     /// @return meta The ZkSyncMeta execution context parameters.
272:     function getZkSyncMeta() internal view returns (ZkSyncMeta memory meta) {
273:         uint256 metaPacked = getZkSyncMetaBytes(); //@audit-issue heapSize and auxHeapSize are missing, while they are individually queryable. Missing fields retrieved here on a struct telling "0". At least comment why, this is confusing. The fields are not tightly packed according to comment L196
274:         meta.gasPerPubdataByte = getGasPerPubdataByteFromMeta(metaPacked);
275:         meta.shardId = getShardIdFromMeta(metaPacked);
276:         meta.callerShardId = getCallerShardIdFromMeta(metaPacked);
277:         meta.codeShardId = getCodeShardIdFromMeta(metaPacked);
278:     }
```

As the `@audit-issue` tag says, `heapSize` and `auxHeapSize` are missing and this is probably due to the fact that [the fields are not tightly packed](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/README.md?plain=1#L148). However, it is still not 100% clear why returning 0 for `heapSize` and `auxHeapSize` would be expected. Consider adding some comments/documentation.

## 2.6. `require()` should be used for checking error conditions on inputs and return values while `assert()` should be used for invariant checking

Properly functioning code should **never** reach a failing assert statement, unless there is a bug in your contract you should fix. [Here](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L225), I believe the `assert` should be a `require` statement, as it's checking what a specific user is doing:

```solidity
DefaultAccount.sol:225:        assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);  
```

As the Solidity version is > 0.8.* the remaining gas would still be refunded in case of failure.

Notice that, on the opposite side, [the following use](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/RLPEncoder.sol#L45) of `assert` is totally justified:

```solidity
contracts/libraries/RLPEncoder.sol
44:     function encodeNonSingleBytesLen(uint64 _len) internal pure returns (bytes memory) {
45:         assert(_len != 1);
46:         return _encodeLength(_len, 0x80);
47:     }
```

This is due to the fact that, in all of the codebase, each call to `RLPEncoder.encodeNonSingleBytesLen()` is already made under the condition that the input is `!= 1`, making this `assert` an effective "invariant check":

```solidity
contracts/BootloaderUtilities.sol:
   68              if (txDataLen != 1) {
   69                  // If the length is not equal to one, then only using the length can it be encoded definitely.
   70:                 encodedDataLength = RLPEncoder.encodeNonSingleBytesLen(txDataLen);

  164              if (txDataLen != 1) {
  165                  // If the length is not equal to one, then only using the length can it be encoded definitely.
  166:                 encodedDataLength = RLPEncoder.encodeNonSingleBytesLen(txDataLen);

  259              if (txDataLen != 1) {
  260                  // If the length is not equal to one, then only using the length can it be encoded definitely.
  261:                 encodedDataLength = RLPEncoder.encodeNonSingleBytesLen(txDataLen);

contracts/libraries/TransactionHelper.sol:
  171              if (txDataLen != 1) {
  172                  // If the length is not equal to one, then only using the length can it be encoded definitely.
  173:                 encodedDataLength = RLPEncoder.encodeNonSingleBytesLen(txDataLen);

  249              if (txDataLen != 1) {
  250                  // If the length is not equal to one, then only using the length can it be encoded definitely.
  251:                 encodedDataLength = RLPEncoder.encodeNonSingleBytesLen(txDataLen);

  321              if (txDataLen != 1) {
  322                  // If the length is not equal to one, then only using the length can it be encoded definitely.
  323:                 encodedDataLength = RLPEncoder.encodeNonSingleBytesLen(txDataLen);
```

As an additional note: this repeated sentence could be rephrased as it's not easy to understand/feels grammatically incorrect: `If the length is not equal to one, then only using the length can it be encoded definitely`.

## 2.7. Typos

```diff
- libraries/SystemContractHelper.sol:236:    /// of the auxilary heap in bytes.
+ libraries/SystemContractHelper.sol:236:    /// of the auxiliary heap in bytes.
- libraries/SystemContractHelper.sol:238:    /// @return auxHeapSize The size of the auxilary memory in bytes byte.
+ libraries/SystemContractHelper.sol:238:    /// @return auxHeapSize The size of the auxiliary memory in bytes byte.
- libraries/SystemContractHelper.sol:239:    /// @dev You can read more on auxilary memory in the VM1.2 documentation.
+ libraries/SystemContractHelper.sol:239:    /// @dev You can read more on auxiliary memory in the VM1.2 documentation.
- libraries/SystemContractHelper.sol:318:    /// @notice Retuns whether the current call is a system call.
+ libraries/SystemContractHelper.sol:318:    /// @notice Returns whether the current call is a system call.
- DefaultAccount.sol:64:    /// @dev Besides the params above, it also accepts unused first paramter "_txHash", which
+ DefaultAccount.sol:64:    /// @dev Besides the params above, it also accepts unused first parameter "_txHash", which
- DefaultAccount.sol:161:    /// @return EIP1271_SUCCESS_RETURN_VALUE if the signaure is correct. It reverts otherwise.
+ DefaultAccount.sol:161:    /// @return EIP1271_SUCCESS_RETURN_VALUE if the signature is correct. It reverts otherwise.
- SystemContext.sol:106:    /// @dev Whie _expectedNewNumber can be derived as prevBlockNumber + 1, we still
+ SystemContext.sol:106:    /// @dev While _expectedNewNumber can be derived as prevBlockNumber + 1, we still
```

## 2.8. Consider only declaring 1 contract, or 1 library, or 1 interface, or 1 abstract contract in a file

`SystemContractHelper.sol` is the only file where there are 2 declarations:

```solidity
41: library SystemContractHelper {
...
336: abstract contract ISystemContract {
```

Consider moving `ISystemContract` to its own file.

## 2.9. Default Visibility

The following constants are using the default visibility:

```solidity
libraries/Utils.sol:12:    bytes32 constant IS_CONSTRUCTOR_BYTECODE_HASH_BIT_MASK =
libraries/Utils.sol:16:    bytes32 constant SET_IS_CONSTRUCTOR_MARKER_BIT_MASK =
AccountCodeStorage.sol:22:    bytes32 constant EMPTY_STRING_KECCAK = 0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470;
NonceHolder.sol:27:    uint256 constant DEPLOY_NONCE_MULTIPLIER = 2 ** 128;
NonceHolder.sol:30:    uint256 constant MAXIMAL_MIN_NONCE_INCREMENT = 2 ** 32;
SystemContext.sol:48:    uint256 constant BLOCK_INFO_BLOCK_NUMBER_PART = 2 ** 128;  
```

For readability, consider explicitly declaring them as `internal`.

## 2.10. Adding a `return` statement when the function defines a named return variable, is redundant

While **not** consuming more gas: using both named returns and a return statement isn't necessary. Removing one of those can improve code clarity.

Affected code:

```solidity
contracts/ContractDeployer.sol:
  32:     function getAccountInfo(address _address) external view returns (AccountInfo memory info) {  
  33:         return _accountInfo[_address]; 

contracts/NonceHolder.sol:
  124:     function getDeploymentNonce(address _address) external view returns (uint256 deploymentNonce) {  
  125          uint256 addressAsKey = uint256(uint160(_address));
  126          (deploymentNonce, ) = _splitRawNonce(rawNonces[addressAsKey]);
  127  
  128:         return deploymentNonce; 
  129      }
```

## 2.11. `2500000000000000` should be changed to `2.5e15` for readability reasons

```solidity
SystemContext.sol:40:    uint256 public difficulty = 2500000000000000;  
```

## 2.12. Use `string.concat()` or `bytes.concat()` along with a more recent version of Solidity

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)
Solidity version 0.8.12 introduces `string.concat()` (vs `abi.encodePacked(<str>,<str>)`)

```solidity
libraries/TransactionHelper.sol:132:                keccak256(abi.encodePacked(_transaction.factoryDeps)),
libraries/TransactionHelper.sol:141:        return keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));  
L2EthToken.sol:98:        return abi.encodePacked(IMailbox.finalizeEthWithdrawal.selector, _to, _amount);
```

## 2.13. Import declarations should import specific identifiers, rather than the whole file

Using import declarations of the form `import {<identifier_name>} from "some/file.sol"` avoids polluting the symbol namespace making flattened files smaller, and speeds up compilation

```solidity
libraries/EfficientCall.sol:5:import "./SystemContractHelper.sol";
libraries/EfficientCall.sol:6:import "./Utils.sol";
libraries/SystemContractHelper.sol:7:import "./SystemContractsCaller.sol";
libraries/SystemContractHelper.sol:8:import "./Utils.sol";
libraries/SystemContractsCaller.sol:6:import "./Utils.sol";
libraries/TransactionHelper.sol:5:import "../openzeppelin/token/ERC20/IERC20.sol";
libraries/TransactionHelper.sol:6:import "../openzeppelin/token/ERC20/utils/SafeERC20.sol";
libraries/TransactionHelper.sol:8:import "../interfaces/IPaymasterFlow.sol";
libraries/TransactionHelper.sol:9:import "../interfaces/IContractDeployer.sol";
libraries/TransactionHelper.sol:11:import "./RLPEncoder.sol";
libraries/TransactionHelper.sol:12:import "./EfficientCall.sol";
libraries/Utils.sol:4:import "./EfficientCall.sol";
AccountCodeStorage.sol:5:import "./interfaces/IAccountCodeStorage.sol";
AccountCodeStorage.sol:6:import "./libraries/Utils.sol";
BootloaderUtilities.sol:5:import "./interfaces/IBootloaderUtilities.sol";
BootloaderUtilities.sol:6:import "./libraries/TransactionHelper.sol";
BootloaderUtilities.sol:7:import "./libraries/RLPEncoder.sol";
BootloaderUtilities.sol:8:import "./libraries/EfficientCall.sol";
BytecodeCompressor.sol:5:import "./interfaces/IBytecodeCompressor.sol";
BytecodeCompressor.sol:6:import "./Constants.sol";
BytecodeCompressor.sol:7:import "./libraries/Utils.sol";
BytecodeCompressor.sol:8:import "./libraries/UnsafeBytesCalldata.sol";
Constants.sol:5:import "./interfaces/IAccountCodeStorage.sol";
Constants.sol:6:import "./interfaces/INonceHolder.sol";
Constants.sol:7:import "./interfaces/IContractDeployer.sol";
Constants.sol:8:import "./interfaces/IKnownCodesStorage.sol";
Constants.sol:9:import "./interfaces/IImmutableSimulator.sol";
Constants.sol:10:import "./interfaces/IEthToken.sol";
Constants.sol:11:import "./interfaces/IL1Messenger.sol";
Constants.sol:12:import "./interfaces/ISystemContext.sol";
Constants.sol:13:import "./interfaces/IBytecodeCompressor.sol";
Constants.sol:14:import "./BootloaderUtilities.sol";
ContractDeployer.sol:6:import "./interfaces/IContractDeployer.sol";
ContractDeployer.sol:9:import "./libraries/Utils.sol";
ContractDeployer.sol:10:import "./libraries/EfficientCall.sol";
DefaultAccount.sol:5:import "./interfaces/IAccount.sol";
DefaultAccount.sol:6:import "./libraries/TransactionHelper.sol";
DefaultAccount.sol:7:import "./libraries/SystemContractHelper.sol";
DefaultAccount.sol:8:import "./libraries/EfficientCall.sol";
ImmutableSimulator.sol:5:import "./interfaces/IImmutableSimulator.sol";
KnownCodesStorage.sol:5:import "./interfaces/IKnownCodesStorage.sol";
KnownCodesStorage.sol:6:import "./libraries/Utils.sol";
KnownCodesStorage.sol:7:import "./libraries/SystemContractHelper.sol";
L1Messenger.sol:5:import "./interfaces/IL1Messenger.sol";
L1Messenger.sol:6:import "./libraries/SystemContractHelper.sol";
L1Messenger.sol:7:import "./libraries/EfficientCall.sol";
MsgValueSimulator.sol:5:import "./libraries/EfficientCall.sol";
NonceHolder.sol:5:import "./interfaces/INonceHolder.sol";
NonceHolder.sol:6:import "./interfaces/IContractDeployer.sol";
```
