# NON-CRITICAL

## 1. Use a more recent version of Solidity

- Use a solidity version of at least 0.8.2 to get compiler automatic inlining  
- Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads  
- Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings  
- Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value
- Use a solidity version of at least 0.8.12 to get `string.concat()` to be used instead of `abi.encodePacked(<str>,<str>)`
- Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions
- Use a solidity version of 0.8.15 where conditions necessary for inlining are relaxed. It shows significant dicrease in the bytecode size (and thus reduced deployment cost)
- Use a solidity version of 0.8.17 to prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero; More efficient overflow checks for multiplication

```
All contracts
```

## 2. Use of `bytes.concat()` instead of `abi.encodePacked()`

Solidity version 0.8.4 introduces `bytes.concat()`, use it rather than  `abi.encodePacked(<bytes>,<bytes>)`

```
141      return keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));
```
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol


## 3. For modern and more readable code; update import usages

Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it.

This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

Recommendation
`import {contract1 , contract2} from "filename.sol";`

```
All contracts
```


## 4. For functions, follow Solidity standard naming conventions

The above codes don’t follow Solidity’s standard naming convention,

- internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)

- public and external functions : only mixedCase

Some internal funciton within the project are not following the naming convention.

Only contracts where this is followed correctly are below, all other do not follow it:
```
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/KnownCodesStorage.sol
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L2EthToken.sol
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BytecodeCompressor.sol
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol
```


## 5. Put all constants in `Constants.sol`

Since there is a Constants.sol file, it should contain all constants used in the project, but there are a lot constants that are left in other contracts.

This will help with readability and easier maintenance for future changes.


## 6. Expressions for constant values such as a call to `keccak256()`, should use immutable rather than constant

```
81       bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");
```
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol


## 7. `require()`/`revert()` statements should have descriptive reason strings

```
152	require(gasleft() >= _gasToBurn);
```
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol
```
135	require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "");
```
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol
```
16	require(msg.sender == BOOTLOADER_FORMAL_ADDRESS);
```
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol
```
47	require(precompileCallSuccess);
```
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L1Messenger.sol


## 8. `require()` should be used instead of `assert()`

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.

```
45       assert(_len != 1);
```
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/RLPEncoder.sol


## 9. Avoid nested `if` statements

For better readability and analysis it is better to avoid nested `if` blocks if possible.

```
365	if (paymasterInputSelector == IPaymasterFlow.approvalBased.selector) {
377		if (currentAllowance < minAllowance) {
384	} else if (paymasterInputSelector == IPaymasterFlow.general.selector) {
```
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol
```
262	if (success) {
263		if (returndata.length == 0) {
```
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/utils/Address.sol
```
150	} else {
151		bool success = EfficientCall.rawCall(gas, to, value, data);
152		if (!success) {
```
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol
```
65	if (getMarker(_bytecodeHash) == 0) {
68		if (_shouldSendToL1) {
```
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/KnownCodesStorage.sol
```
35	if (value != 0) {
41		if (!success) {
```
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol


## 10. `Function writing` that does not comply with the `Solidity Style Guide`


Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- within a grouping, place the view and pure functions last

Most contracts where there are public, externa and internal functions they are all in mixed order and do not comply with the standard above.
