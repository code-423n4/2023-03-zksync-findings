# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1  | Potential risk of underflow in the `withdraw` function | Low | 1 |
| 2  | `require` should be used instead of `assert` | NC | 2 |
| 3  | Avoid floating pragma where possible | NC |  |
| 4  | Related data should be grouped in a struct | NC | 2 |
| 5  | `public` functions not called by the contract should be declared `external` instead  | NC | 7 |
| 6  | Missing natspec/comments | NC | 2 |

## Findings

### 1- Potential risk of underflow in the `withdraw` function :

#### Risk : Low

In the `withdraw` function from the L2EthToken contract we can see the following :

File: L2EthToken.sol [Line 225](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L81-L87)
```
uint256 amount = msg.value;

// Silent burning of the ether
unchecked {
    balance[address(this)] -= amount;
    totalSupply -= amount;
}
```

In the code above the value of `amount` is not verified before the substraction operations and as those operations are marked as `unchecked` this can lead to an underflow of the values of `balance[address(this)]` and `totalSupply`.

#### Mitigation
Check the value of `amount` is less than `balance[address(this)]`

### 2- `require` should be used instead of `assert` :

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as `require()/revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.

#### Risk : Non Critical

#### Proof of Concept
Instances include:

File: DefaultAccount.sol [Line 225](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L225)
```
assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);
```

File: libraries/RLPEncoder.sol [Line 45](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/RLPEncoder.sol#L45)
```
assert(_len != 1);
```

#### Mitigation
Replace the `assert` checks aforementioned with `require` statements.

### 3- Avoid floating pragma where possible :

#### Risk : Non critical

Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.

All the main contracts of the protocol use a floating solidity version `pragma solidity ^0.8.0` which should be fixed to agiven version (preferably to a recent one) to avoid any issues in the future.

Note that pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or a package.


### 4- Related data should be grouped in a struct :

When there are mappings that use the same key value, having separate fields is error prone, for instance in case of deletion or with future new fields.

#### Risk : Non critical

#### Proof of Concept

Instances include:

File: NonceHolder.sol [Line 35-40](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L35-L40)

```
35      mapping(uint256 => uint256) internal rawNonces;
40      mapping(uint256 => mapping(uint256 => uint256)) internal nonceValues;
```

#### Mitigation

Group the related data in a struct and use one mapping :

```
struct Nonce {
    uint256 raw;
    mapping(uint256 => uint256) values;
}
```

And it would be used as a state variable :

```
mapping(uint256 => Nonce) internal _nonces;
```

### 5- `public` functions not called by the contrat should be declared `external` instead  :

#### Risk : Non critical

#### Proof of Concept
Instances include:

File: NonceHolder.sol

[Line 38](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L38)

```
function extendedAccountVersion(address _address) public view returns (AccountAbstractionVersion)
```

File: NonceHolder.sol

[Line 56](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L56)

```
function getRawNonce(address _address) public view returns (uint256)
```

[Line 64](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L64)

```
function increaseMinNonce(uint256 _value) public
```

[Line 81](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L81)

```
function setValueUnderNonce(uint256 _key, uint256 _value) public
```

[Line 101](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L101)

```
function getValueUnderNonce(uint256 _key) public view returns (uint256)
```

File: SystemContext.sol

[Line 91](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol#L91)

```
function getBlockNumber() public view returns (uint256 blockNumber)
```

[Line 97](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol#L97)

```
function getBlockTimestamp() public view returns (uint256 timestamp)
```

#### Mitigation
Declare the functions aforementioned external.


### 6- Missing natspec/comments :

#### Risk : Non critical

Consider adding natspec/comments on all functions to improve code documentation and readability.

Instances include :

File: ContractDeployer.sol [Line 249-267](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L249-L267)

File: NonceHolder.sol [Line 146](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L146)