## [LOW - 1] Any transaction sending more than 2**128 ETH will loose them

The `MsgValueSimulator` is a system contract used to emulate the Solidity `msg.value` and is used absolutely everytime there is an ETH transfer in a smart contract call (when "value" is attached).
It is exposed with a `fallback()` function, which unpacks the data in the calldata and calls the `L2EthToken` contract in order to transfer those ETH from the sender to the contract.
There is a security check, which should make the transaction revert in the case that more than `2**128 - 1` ETH are transferred. And in this case should revert, but currently simply [`returns` nothing](https://github.com/code-423n4/2023-03-zksync/blob/01379615bc20c2926d81c0a182f1abb6e922b93a/contracts/MsgValueSimulator.sol#L55). This was submitted as a QA because theoretically, nobody should ever have that much ETH to spend as the current total supply of ETH is at this time about [`120.000` ETH](https://ultrasound.money/).

## [LOW - 2] Dangerous function should be removed

The function [`unsafeOverrideBlock()`](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol#L132-L135) can be called by the bootloader in order to completely change the blockchain state (block number, timestamp, basefee) and it is currently very dangerous to leave it in this state. Changing such informations would **break** the blockchain nature because it would overwrite old and final blocks, change the timestamp timeline, and ask the users to pay too much or few `baseFee` for their transactions. It should be removed before production.

## [QA - 1] Unclear block gas limit

Currently, it is unclear which gas limit is the right one. And the one that is used in the system contracts may be wrong as well. The current `blockGasLimit` was set to `1 << 30` in the [`SystemContext`](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol#L33), with a `@dev` natspec comment explaining that it currently mimics the Ethereum block gas limit, which is wrong. Currently, the Ethereum gas limit is **30M**, which is far less than `1 << 30`. In fact, this value approximately equals **35** times the Ethereum gas limit and should likely be changed before deploying.
It is as well stated in the [`README.md`](https://github.com/code-423n4/2023-03-zksync/blob/main/README.md#temporary-data-for-debug--transaction-processing-purposes) that the operator's trusted gas limit is somewhere between `trustedGasLimit` and at maximum `MAX_TX_GAS()`, which is about `80M`, nearly **13** smaller than `1 << 30`.

## [QA - 2] Silent down-casting

In the `unsafeBytesCalldata` contract, which can be used to read numbers from an array of bytes, we firstly pick the value where we would like to read the number from (`_start`), and then compute the offset in this bytes array (remove 30 bytes for uint16 because uint16 is 2 bytes long and that 32 - 30 == 2, and do as well for the `readUint64`).
Using the `calldataload` loads a full word (32 bytes) from the calldata and could lure the reader by thinking that it would load neighbour values in the array, which is not true because the return type is silently casted to [`uint16`](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/UnsafeBytesCalldata.sol#L18) or [`uint64`](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/UnsafeBytesCalldata.sol#L25).

## [QA - 3] Unhelpful revert message

Revert messages are usually useful to debug transactions or even to understand what's going on for the end user. In the `NonceHolder`, when the `incrementDeploymentNonce` is called (quite frequently), there is an access control check to make sure that the sender was the `ContractDeployer` contract, responsible for deploying contracts on the L2.
On this line, the [revert message](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L135) is empty and thus, unhelpful.

## [QA - 4] Function name is not descriptive enough

The `EfficientCall` library can be used to do various calls like for precompiles, but also others commonly used by the system contracts such as call, delegate-call, mimic-call, or static-call.
For precompiles, which are [`ecrecover`, `sha256`, and `keccak256`](https://github.com/code-423n4/2023-03-zksync/tree/01379615bc20c2926d81c0a182f1abb6e922b93a/contracts/precompiles), two of those are helper functions in this library. [`keccak`](https://github.com/code-423n4/2023-03-zksync/blob/01379615bc20c2926d81c0a182f1abb6e922b93a/contracts/libraries/EfficientCall.sol#L35), and [`sha`](https://github.com/code-423n4/2023-03-zksync/blob/01379615bc20c2926d81c0a182f1abb6e922b93a/contracts/libraries/EfficientCall.sol#L44). `sha` name is not very descriptive as it could make believe the interfacer that it's calling the `sha1` cipher implicitely, which is not the case.
If such a precompile is added in the future, it may cause troubles and breaking changes.

## [QA - 5] No need for a "default" path which is unreachable

The [`EventWriter`](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/EventWriter.yul) contract is the one that is responsible for emitting events within the system contracts. The maximum of indexed topic is 4 and this is checked [here](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/EventWriter.yul#L119-L121). Later, there is a `switch / case` statement which calls one function or another in order to write the topics to the memory and does the check for all the indexed events cases (0..4) but leaves a "default" block. [This block](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/EventWriter.yul#L152-L155) is unreachable and thus, useless.
The `case 4` can then be replaced by this `default` block without any issue.