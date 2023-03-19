# [01] Emit events before external calls

Multiple functions in the project emit an event as the last statement. Wherever possible, consider emitting events before external calls. In case of reentrancy, funds are not at risk (for external call + event ordering), however emitting events after external calls can damage frontends and monitoring tools in case of reentrancy attacks.

For example, the event `ContractDeployer.forceDeployOnAddress().ContractDeployed` could be emitted before `ContractDeployer.forceDeployAddress()._constructContract()` which will call `ContratDeployer._constructContract().ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo()`.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L226

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L223

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L318

Similarly, the event `L1Messenger.sendToL1().L1MessageSent` could be emitted before `L1Messenger.sendToL1().SystemContractHelper.precompileCall()`, which will call `SystemContractHelper.precompileCall.staticall()`.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L1Messenger.sol#L51

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L1Messenger.sol#L43-L46

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol#L157

# [02] gasLimit should be uint64 instead of uint256

For the EVM, gasLimit is uint64. Even if the value will be modified before mainnet launch, the variable type should be the same as the EVM. Otherwise, for values bigger than uint64, it would be considered valid on the ZKEVM and invalid on the EVM.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol#L31-L33

https://github.com/ethereum/go-ethereum/blob/79a478bb6176425c2400e949890e668a3d9a3d05/core/types/tx_legacy.go#L29

# [03] Unsafe casts

Even if it's unlikely for `gasleft` to overflow 32 bits in `DefaultAccount._validateTransaction()` and `value` from `MsgValueSimulator._getAbiParams()` used in `MsgValueSimulator.fallback()` to overflow 128 bits, consider applying the casting with a safecast function for these variables, to prevent silent/unexpected overflows.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L83

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/MsgValueSimulator.sol#L60

# [04] Pragma float

All the contracts in scope are floating the pragma.

Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.

Note that pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or a package.

# [05] Avoid hardcoding OpenZeppelin contracts

It's recommended to use OpenZeppelin through a build system/smart contract framework. Since the project is using hardhat, consider using OpenZeppelin through npm, instead of copying the files.

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/openzeppelin

# [06] Lack of checks-effects-interactions in `MsgValueSimulator.fallback()`

Consider updating the state before external calls to follow the checks-effects-interactions pattern.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/MsgValueSimulator.sol#L36-L38

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/MsgValueSimulator.sol#L60

# [07] Prevent `immutableDataStorage` from receiving address zero

Consider adding a check for address zero in `ImmutableSimulator.setImmutable()` for the input `_dest`.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ImmutableSimulator.sol#L33-L43

# [08] `ContractDeployer.forceDeployOnAddress()` could be private instead of external with onlySelf

If there's a reason why the function needs to be external + onlySelf instead of being private, consider adding a comment.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L212

# [09] v == 27 && v == 28 check is no longer needed:

Please refer to a full description on this [thread](https://twitter.com/alexberegszaszi/status/1534530195721437184).

# [10] Add and event for functions involving parameter changes

It can facilitate offchain monitoring.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol#L60-L62

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol#L66-L68

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ImmutableSimulator.sol#L33-L43

# [11] Check for stale values on setter functions that emit an event

Add a check ensuring that the new value if different than the current value to avoid emitting unnecessary events.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L60-L64

# [12] Lack of old and new value for events related to parameter updates

Events that mark critical parameter changes should contain both the old and the new value.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L60-L64

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L69-L82

# [13] Replace assert with require or custom error

As described on the solidity [documentation](https://docs.soliditylang.org/en/v0.8.15/control-structures.html#panic-via-assert-and-error-via-require). "The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix.

Even if the code is expected to be unreachable, consider using a custom error instead of assert.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L225

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/RLPEncoder.sol#L45

# [14] Usage of return named variables and explicit values

Some functions return named variables, others return explicit values.

Following function returns an explicit value.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/AccountCodeStorage.sol#L96

Following function return returns a named variable.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/AccountCodeStorage.sol#L102

Consider adopting the same approach throughout the codebase to improve the explicitness and readability of the code.

# [15] Missing NATSPEC

Consider adding NATSPEC on all functions to improve documentation.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L249

# [16] Order of functions

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) recommends the following order for functions:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private

The instance bellow shows public above external.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L181-L186

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L212

# [17] Add a limit for the maximum number of characters per line

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) recommends a maximum of 120 characters.

Consider adding a limit of 120 characters or less to prevent large lines.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L7

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L101

# [18] Repeated validation statements 

Repeated statements used for validation can be converted into a function modifier to improve code reusability.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/utils/Address.sol#L61-L64

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/openzeppelin/utils/Address.sol#L155-L158

# [19] Replace exponentiation with type(uint<n>).max for multiples of 2

`2**128` can be rewritten as `type(uint128).max + 1`

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol#L48

# [20] Named imports can be used

It's possible to name more imports to improve code readability and consistency. E.g. `import "../openzeppelin/token/ERC20/IERC20.sol";` can be rewritten as `import {IERC20} from "../openzeppelin/token/ERC20/IERC20.sol";`

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L5-L9

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L11-L12

# [21] Imports can be group together

Consider grouping the imports, e.g. interfaces first, then libraries and then constants.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L5-L8

# [22] Contract can be renamed

Consider renaming `SystemContractHelper` to `SystemContractsHelper` or `SystemContractsCaller` to `SystemContractCaller` to make the naming more consistent (note the plural vs singular for contracts).

# [23] Can use ternary

The [conditional](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol#L74-L78) in `SystemContext.getBlockHashEVM()` can be replaced with:

```solidity
hash = block.number < _block || block.number - _block > 256
    ? hash = bytes32(0)
    : hash = blockHash[_block];
```

# [24] Downcasting can be reused

The [snippet](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/AccountCodeStorage.sol#L77-L80) on `AccountCodeStorage.getCodeHash()` can reuse a downcasted value to make the code more reusable and also save computations. Consider refactoring to the following;

```solidity
uint160 inputUint160 = uint160(_input);
address account = address(inputUint160);
if (inputUint160 <= CURRENT_MAX_PRECOMPILE_ADDRESS) {
    return EMPTY_STRING_KECCAK;
}
```
