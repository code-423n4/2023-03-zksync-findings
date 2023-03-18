# Gas Optimizations Report

| |Issue|Instances|
|-|:-|:-:|
|[G-006]|x += y or x -= y costs more gas than x = x + y or x = x - y for state variables|1|
|[G-013]|Functions guaranteed to revert when called by normal users can be marked payable|4|
|[G-024]|Replace modifier with function|1|


## [G-006] x += y or x -= y costs more gas than x = x + y or x = x - y for state variables

### Impact
Using the addition operator instead of plus-equals saves 113 gas. Usually does not work with struct and mappings.

### Findings
Total:1

[contracts/tests/Counter.sol#L9](https://github.com/code-423n4/2023-03-zksync/tree/main//contracts/tests/Counter.sol#L9)
```solidity
9:    counter += 1;
```

### Recommendation
 Same as description

## [G-013] Functions guaranteed to revert when called by normal users can be marked payable

### Impact
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
   The extra opcodes avoided are `CALLVALUE(2)`,`DUP1(3)`,`ISZERO(3)`,`PUSH2(3)`,`JUMPI(10)`,`PUSH1(3)`,`DUP1(3)`,`REVERT(0)`,`JUMPDEST(1)`,`POP(2)`, which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

### Findings
Total:4

[contracts/test-contracts/TestSystemContract.sol#L90](https://github.com/code-423n4/2023-03-zksync/tree/main//contracts/test-contracts/TestSystemContract.sol#L90)
```solidity
90:    function requireOnlySystem() external onlySystemCall {}
```
[contracts/test-contracts/TestSystemContract.sol#L133](https://github.com/code-423n4/2023-03-zksync/tree/main//contracts/test-contracts/TestSystemContract.sol#L133)
```solidity
133:    ) external onlySelf {
```
[contracts/test-contracts/TestSystemContract.sol#L122](https://github.com/code-423n4/2023-03-zksync/tree/main//contracts/test-contracts/TestSystemContract.sol#L122)
```solidity
122:    ) external onlySelf returns (bool) {
```
[contracts/test-contracts/TestSystemContract.sol#L112](https://github.com/code-423n4/2023-03-zksync/tree/main//contracts/test-contracts/TestSystemContract.sol#L112)
```solidity
112:    ) external onlySelf returns (bytes memory) {
```

### Recommendation
Mark the function as payable.

## [G-024] Replace modifier with function

### Impact
Modifiers make code more elegant, but cost more than normal functions.

### Findings
Total:1

[contracts/test-contracts/TestSystemContract.sol#L16](https://github.com/code-423n4/2023-03-zksync/tree/main//contracts/test-contracts/TestSystemContract.sol#L16)
```solidity
16:    modifier onlySelf() {
```

### Recommendation
Same as description

