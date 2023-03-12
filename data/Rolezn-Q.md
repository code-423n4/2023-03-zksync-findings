## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Low Level Calls With Solidity Version 0.8.14 Can Result In Optimiser Bug | 3 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Minting tokens to the zero address should be avoided | 1 |
| [LOW&#x2011;3](#LOW&#x2011;3) | Missing Contract-existence Checks Before Low-level Calls | 3 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Contracts are not using their OZ Upgradeable counterparts | 2 |
| [LOW&#x2011;5](#LOW&#x2011;5) | `require()` should be used instead of `assert()` | 2 |

Total: 5 contexts over 11 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Add a timelock to critical functions | 6 |
| [NC&#x2011;2](#NC&#x2011;2) | Constants in comparisons should appear on the left side | 1 |
| [NC&#x2011;3](#NC&#x2011;3) | Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant` | 4 |
| [NC&#x2011;4](#NC&#x2011;4) | Critical Changes Should Use Two-step Procedure | 6 |
| [NC&#x2011;5](#NC&#x2011;5) | Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function | 3 |
| [NC&#x2011;6](#NC&#x2011;6) | Function creates dirty bits | 6 |
| [NC&#x2011;7](#NC&#x2011;7) | Imports can be grouped together | 4 |
| [NC&#x2011;8](#NC&#x2011;8) | NatSpec return parameters should be included in contracts | 1 |
| [NC&#x2011;9](#NC&#x2011;9) | No need to initialize uints to zero | 1 |
| [NC&#x2011;10](#NC&#x2011;10) | Initial value check is missing in Set Functions | 4 |
| [NC&#x2011;11](#NC&#x2011;11) | Missing event for critical parameter change | 5 |
| [NC&#x2011;12](#NC&#x2011;12) | NatSpec comments should be increased in contracts | 1 |
| [NC&#x2011;13](#NC&#x2011;13) | Non-usage of specific imports | 52 |
| [NC&#x2011;14](#NC&#x2011;14) | Use a more recent version of Solidity | 36 |
| [NC&#x2011;15](#NC&#x2011;15) | Using `>`/`>=` without specifying an upper bound is unsafe | 1 |
| [NC&#x2011;16](#NC&#x2011;16) | Public Functions Not Called By The Contract Should Be Declared External Instead | 6 |
| [NC&#x2011;17](#NC&#x2011;17) | Adding A Return Statement When The Function Defines A Named Return Variable, Is Redundant | 1 |
| [NC&#x2011;18](#NC&#x2011;18) | Stop using `v != 27 && v != 28` or `v == 27 \|\| v == 28` | 4 |
| [NC&#x2011;19](#NC&#x2011;19) | Use `bytes.concat()` | 3 |
| [NC&#x2011;20](#NC&#x2011;20) | Use `` instead of `"0x00"` for empty bytes to avoid triggering extra computation on transfers | 3 |

Total: 148 contexts over 20 issues

## Low Risk Issues

### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Low Level Calls With Solidity Version Before 0.8.14 Can Result In Optimiser Bug

The project contracts in scope are using low level calls with solidity version before 0.8.14 which can result in optimizer bug.
https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d

Simliar findings in Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#5-low-level-calls-with-solidity-version-0814-can-result-in-optimiser-bug

#### <ins>Proof Of Concept</ins>

POC can be found in the above medium reference url.

Functions that execute low level calls in contracts with solidity version under 0.8.14

```solidity
11: assembly {
            // In the first byte we write the encoded length as 0x80 + 0x14 == 0x94.
            mstore(add(encoded, 0x20), 0x9400000000000000000000000000000000000000000000000000000000000000)
            // Write address data without stripping zeros.
            mstore(add(encoded, 0x21), shiftedVal)
        }
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/RLPEncoder.sol#L11

```solidity
34: assembly {
            mstore(add(encoded, 0x21), shiftedVal)
        }
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/RLPEncoder.sol#L34

```solidity
69: assembly {
            mstore(add(encoded, 0x21), shiftedVal)
        }
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/RLPEncoder.sol#L69





#### <ins>Recommended Mitigation Steps</ins>

Consider upgrading to at least solidity v0.8.15.



### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Minting tokens to the zero address should be avoided

The core function `mint` is used by users to mint an option position by providing token1 as collateral and borrowing the max amount of liquidity. `Address(0)` check is missing in both this function and the internal function `_mint`, which is triggered to mint the tokens to the to address. Consider applying a check in the function to ensure tokens aren't minted to the zero address.

#### <ins>Proof Of Concept</ins>


```solidity

function mint(address _account, uint256 _amount) external override onlyBootloader {
        totalSupply += _amount;
        balance[_account] += _amount;
        emit Mint(_account, _amount);
    }

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L72







### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Missing Contract-existence Checks Before Low-level Calls

Low-level calls return success if there is no code present at the specified address. 

#### <ins>Proof Of Concept</ins>


```solidity
36: (bool success, ) = address(ETH_TOKEN_SYSTEM_CONTRACT).call(
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/MsgValueSimulator.sol#L36


#### <ins>Recommended Mitigation Steps</ins>

In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`



### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

```solidity
5: import "../openzeppelin/token/ERC20/IERC20.sol";
6: import "../openzeppelin/token/ERC20/utils/SafeERC20.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L5-L6



#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.
See https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/tree/master/contracts for list of available upgradeable contracts



### <a href="#Summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> `require()` Should Be Used Instead Of `assert()`

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction's available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its <a href="https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require">documentation</a> states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix".

#### <ins>Proof Of Concept</ins>


```solidity
225: assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L225

```solidity
45: assert(_len != 1);

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/RLPEncoder.sol#L45





## Non Critical Issues

### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Add a timelock to critical functions

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user). Consider adding a timelock to the following functions:

#### <ins>Proof Of Concept</ins>


```solidity
33: function setImmutables(address _dest, ImmutableData[] calldata _immutables) external override {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ImmutableSimulator.sol#L33

```solidity
81: function setValueUnderNonce(uint256 _key, uint256 _value) public onlySystemCall {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L81

```solidity
60: function setTxOrigin(address _newOrigin) external onlyBootloader {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L60

```solidity
66: function setGasPrice(uint256 _gasPrice) external onlyBootloader {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L66

```solidity
109: function setNewBlock(
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L109

```solidity
164: function setValueForNextFarCall(uint128 _value) internal returns (bool success) {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L164






### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Constants in comparisons should appear on the left side

Doing so will prevent <a href="https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html">typo bugs</a>

#### <ins>Proof Of Concept</ins>

```solidity
87: if (codeHash == 0x00 && NONCE_HOLDER_SYSTEM_CONTRACT.getRawNonce(account) > 0) {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol#L87






### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

#### <ins>Proof Of Concept</ins>


```solidity
81: bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L81

```solidity
83: bytes32 constant EIP712_TRANSACTION_TYPE_HASH =
        keccak256(
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L83





### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Critical Changes Should Use Two-step Procedure

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

#### <ins>Proof Of Concept</ins>

```solidity
33: function setImmutables(address _dest, ImmutableData[] calldata _immutables) external override {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ImmutableSimulator.sol#L33

```solidity
81: function setValueUnderNonce(uint256 _key, uint256 _value) public onlySystemCall {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L81

```solidity
60: function setTxOrigin(address _newOrigin) external onlyBootloader {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L60

```solidity
66: function setGasPrice(uint256 _gasPrice) external onlyBootloader {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L66

```solidity
109: function setNewBlock(
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L109

```solidity
164: function setValueForNextFarCall(uint128 _value) internal returns (bool success) {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L164



#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.



#### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function

Saves deployment costs

#### <ins>Proof Of Concept</ins>

```solidity
91: require(vInt == 27 || vInt == 28, "Invalid v value");
190: require(vInt == 27 || vInt == 28, "Invalid v value");
285: require(vInt == 27 || vInt == 28, "Invalid v value");

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L91

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L190

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L285











### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Function creates dirty bits

This explanation should be added in the NatSpec comments of this function that sends ether with call;

Note that this code probably isn’t secure or a good use case for assembly because a lot of memory management and security checks are bypassed.
Use with caution! Some functions in this contract knowingly create dirty bits at the destination of the free memory pointer.

#### <ins>Proof Of Concept</ins>


```solidity
131: function rawCall(
        uint256 _gas,
        address _address,
        uint256 _value,
        bytes calldata _data
    ) internal returns (bool success) {
        if (_value == 0) {
            _loadFarCallABIIntoActivePtr(_gas, _data, false, false);

            address callAddr = RAW_FAR_CALL_BY_REF_CALL_ADDRESS;
            assembly {
                success := call(_address, callAddr, 0, 0, 0xFFFF, 0, 0)
            }
        } else {
            _loadFarCallABIIntoActivePtr(_gas, _data, false, true);

            
            address msgValueSimulator = MSG_VALUE_SYSTEM_CONTRACT;
            address callAddr = SYSTEM_CALL_BY_REF_CALL_ADDRESS;
            
            
            uint256 forwardMask = MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT;

            assembly {
                success := call(msgValueSimulator, callAddr, _value, _address, 0xFFFF, forwardMask, 0)
            }
        }
    }
144: function rawCall(
        uint256 _gas,
        address _address,
        uint256 _value,
        bytes calldata _data
    ) internal returns (bool success) {
        if (_value == 0) {
            _loadFarCallABIIntoActivePtr(_gas, _data, false, false);

            address callAddr = RAW_FAR_CALL_BY_REF_CALL_ADDRESS;
            assembly {
                success := call(_address, callAddr, 0, 0, 0xFFFF, 0, 0)
            }
        } else {
            _loadFarCallABIIntoActivePtr(_gas, _data, false, true);

            
            address msgValueSimulator = MSG_VALUE_SYSTEM_CONTRACT;
            address callAddr = SYSTEM_CALL_BY_REF_CALL_ADDRESS;
            
            
            uint256 forwardMask = MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT;

            assembly {
                success := call(msgValueSimulator, callAddr, _value, _address, 0xFFFF, forwardMask, 0)
            }
        }
    }

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/EfficientCall.sol#L131

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/EfficientCall.sol#L144


```solidity
55: function toL1(bool _isService, bytes32 _key, bytes32 _value) internal {
        address callAddr = TO_L1_CALL_ADDRESS;
        assembly {
            
            _isService := and(_isService, 1)
            
            
            let success := call(_isService, callAddr, _key, _value, 0xFFFF, 0, 0)
        }
    }

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L55

```solidity
100: function systemCall(uint32 gasLimit, address to, uint256 value, bytes memory data) internal returns (bool success) {
        address callAddr = SYSTEM_CALL_CALL_ADDRESS;

        uint32 dataStart;
        assembly {
            dataStart := add(data, 0x20)
        }
        uint32 dataLength = uint32(Utils.safeCastToU32(data.length));

        uint256 farCallAbi = SystemContractsCaller.getFarCallABI(
            0,
            0,
            dataStart,
            dataLength,
            gasLimit,
            
            0,
            CalldataForwardingMode.UseHeap,
            false,
            true
        );

        if (value == 0) {
            
            assembly {
                success := call(to, callAddr, 0, 0, farCallAbi, 0, 0)
            }
        } else {
            address msgValueSimulator = MSG_VALUE_SYSTEM_CONTRACT;
            
            
            uint256 forwardMask = MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT;

            assembly {
                success := call(msgValueSimulator, callAddr, value, to, farCallAbi, forwardMask, 0)
            }
        }
    }
109: function systemCall(uint32 gasLimit, address to, uint256 value, bytes memory data) internal returns (bool success) {
        address callAddr = SYSTEM_CALL_CALL_ADDRESS;

        uint32 dataStart;
        assembly {
            dataStart := add(data, 0x20)
        }
        uint32 dataLength = uint32(Utils.safeCastToU32(data.length));

        uint256 farCallAbi = SystemContractsCaller.getFarCallABI(
            0,
            0,
            dataStart,
            dataLength,
            gasLimit,
            
            0,
            CalldataForwardingMode.UseHeap,
            false,
            true
        );

        if (value == 0) {
            
            assembly {
                success := call(to, callAddr, 0, 0, farCallAbi, 0, 0)
            }
        } else {
            address msgValueSimulator = MSG_VALUE_SYSTEM_CONTRACT;
            
            
            uint256 forwardMask = MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT;

            assembly {
                success := call(msgValueSimulator, callAddr, value, to, farCallAbi, forwardMask, 0)
            }
        }
    }

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L100

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L109



```solidity
399: function payToTheBootloader(Transaction calldata _transaction) internal returns (bool success) {
        address bootloaderAddr = BOOTLOADER_FORMAL_ADDRESS;
        uint256 amount = _transaction.maxFeePerGas * _transaction.gasLimit;

        assembly {
            success := call(gas(), bootloaderAddr, amount, 0, 0, 0, 0)
        }
    }

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L399




#### <ins>Recommended Mitigation Steps</ins>
Add this comment to the function:
```
/// @dev Use with caution! Some functions in this contract knowingly create dirty bits at the destination of the free memory pointer. Note that this code probably isn’t secure or a good use case for assembly because a lot of memory management and security checks are bypassed.
```




### <a href="#Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Imports can be grouped together

Consider importing OZ first, then all interfaces, then all utils.

#### <ins>Proof Of Concept</ins>


```solidity
5: import {IEthToken} from "./interfaces/IEthToken.sol";
6: import {MSG_VALUE_SYSTEM_CONTRACT, DEPLOYER_SYSTEM_CONTRACT, BOOTLOADER_FORMAL_ADDRESS, L1_MESSENGER_CONTRACT} from "./Constants.sol";
7: import {SystemContractHelper} from "./libraries/SystemContractHelper.sol";
8: import {IMailbox} from "./interfaces/IMailbox.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L5-L8





### <a href="#Summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> NatSpec return parameters should be included in contracts

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html
#### <ins>Proof Of Concept</ins>


Various in-scope contracts

See `L1Messenger.sendToL1`, `L2EthToken._getL1WithdrawMessage` for some examples

#### <ins>Recommended Mitigation Steps</ins>

Include return parameters in NatSpec comments

Recommendation Code Style: (from Uniswap3)

```solidity
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
```



### <a href="#Summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> No need to initialize uints to zero

There is no need to initialize `uint` variables to zero as their default value is `0`

#### <ins>Proof Of Concept</ins>

```solidity
238: uint256 sumOfValues = 0;

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L238





### <a href="#Summary">[NC&#x2011;10]</a><a name="NC&#x2011;10"> Initial value check is missing in Set Functions

A check regarding whether the current value and the new value are the same should be added

#### <ins>Proof Of Concept</ins>


```solidity
81: function setValueUnderNonce(uint256 _key, uint256 _value) public onlySystemCall {
        IContractDeployer.AccountInfo memory accountInfo = DEPLOYER_SYSTEM_CONTRACT.getAccountInfo(msg.sender);

        require(_value != 0, "Nonce value can not be set to 0");
        
        
        if (accountInfo.nonceOrdering == IContractDeployer.AccountNonceOrdering.Sequential && _key != 0) {
            require(isNonceUsed(msg.sender, _key - 1), "Previous nonce has not been used");
        }

        uint256 addressAsKey = uint256(uint160(msg.sender));

        nonceValues[addressAsKey][_key] = _value;

        emit ValueSetUnderNonce(msg.sender, _key, _value);
    }
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L81

```solidity
60: function setTxOrigin(address _newOrigin) external onlyBootloader {
        origin = _newOrigin;
    }
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L60

```solidity
66: function setGasPrice(uint256 _gasPrice) external onlyBootloader {
        gasPrice = _gasPrice;
    }
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L66

```solidity
109: function setNewBlock(
        bytes32 _prevBlockHash,
        uint256 _newTimestamp,
        uint256 _expectedNewNumber,
        uint256 _baseFee
    ) external onlyBootloader {
        (uint256 currentBlockNumber, uint256 currentBlockTimestamp) = getBlockNumberAndTimestamp();
        require(_newTimestamp >= currentBlockTimestamp, "Timestamps should be incremental");
        require(currentBlockNumber + 1 == _expectedNewNumber, "The provided block number is not correct");

        blockHash[currentBlockNumber] = _prevBlockHash;

        
        currentBlockInfo = (currentBlockNumber + 1) * BLOCK_INFO_BLOCK_NUMBER_PART + _newTimestamp;

        baseFee = _baseFee;

        
        SystemContractHelper.toL1(false, bytes32(_newTimestamp), _prevBlockHash);
    }
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L109





### <a href="#Summary">[NC&#x2011;11]</a><a name="NC&#x2011;11"> Missing event for critical parameter change

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

#### <ins>Proof Of Concept</ins>


```solidity
33: function setImmutables(address _dest, ImmutableData[] calldata _immutables) external override {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ImmutableSimulator.sol#L33

```solidity
60: function setTxOrigin(address _newOrigin) external onlyBootloader {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L60

```solidity
66: function setGasPrice(uint256 _gasPrice) external onlyBootloader {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L66

```solidity
109: function setNewBlock(
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L109

```solidity
164: function setValueForNextFarCall(uint128 _value) internal returns (bool success) {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L164





### <a href="#Summary">[NC&#x2011;12]</a><a name="NC&#x2011;12"> NatSpec comments should be increased in contracts

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability. https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

#### <ins>Proof Of Concept</ins>


Various in-scope contracts

See `L1Messenger.sendToL1` for example

#### <ins>Recommended Mitigation Steps</ins>

NatSpec comments should be increased in contracts



### <a href="#Summary">[NC&#x2011;13]</a><a name="NC&#x2011;13"> Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

A good example:
```solidity
import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {SafeCastLib} from "solmate/utils/SafeCastLib.sol";
import {ERC20} from "solmate/tokens/ERC20.sol";
import {IProducer} from "src/interfaces/IProducer.sol";
import {GlobalState, UserState} from "src/Common.sol";
```

#### <ins>Proof Of Concept</ins>


```solidity
5: import "./interfaces/IAccountCodeStorage.sol";
6: import "./libraries/Utils.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol#L5-L6

```solidity
5: import "./interfaces/IBootloaderUtilities.sol";
6: import "./libraries/TransactionHelper.sol";
7: import "./libraries/RLPEncoder.sol";
8: import "./libraries/EfficientCall.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L5-L8

```solidity
5: import "./interfaces/IBytecodeCompressor.sol";
6: import "./Constants.sol";
7: import "./libraries/Utils.sol";
8: import "./libraries/UnsafeBytesCalldata.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol#L5-L8

```solidity
5: import "./interfaces/IAccountCodeStorage.sol";
6: import "./interfaces/INonceHolder.sol";
7: import "./interfaces/IContractDeployer.sol";
8: import "./interfaces/IKnownCodesStorage.sol";
9: import "./interfaces/IImmutableSimulator.sol";
10: import "./interfaces/IEthToken.sol";
11: import "./interfaces/IL1Messenger.sol";
12: import "./interfaces/ISystemContext.sol";
13: import "./interfaces/IBytecodeCompressor.sol";
14: import "./BootloaderUtilities.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/Constants.sol#L5-L14

```solidity
6: import "./interfaces/IContractDeployer.sol";
9: import "./libraries/Utils.sol";
10: import "./libraries/EfficientCall.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L6

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L9

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L10



```solidity
5: import "./interfaces/IAccount.sol";
6: import "./libraries/TransactionHelper.sol";
7: import "./libraries/SystemContractHelper.sol";
8: import "./libraries/EfficientCall.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L5-L8

```solidity
5: import "./interfaces/IImmutableSimulator.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ImmutableSimulator.sol#L5

```solidity
5: import "./interfaces/IKnownCodesStorage.sol";
6: import "./libraries/Utils.sol";
7: import "./libraries/SystemContractHelper.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol#L5-L7

```solidity
5: import "./interfaces/IL1Messenger.sol";
6: import "./libraries/SystemContractHelper.sol";
7: import "./libraries/EfficientCall.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L1Messenger.sol#L5-L7

```solidity
5: import "./libraries/EfficientCall.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/MsgValueSimulator.sol#L5

```solidity
5: import "./interfaces/INonceHolder.sol";
6: import "./interfaces/IContractDeployer.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L5-L6

```solidity
5: import "../libraries/TransactionHelper.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IAccount.sol#L5

```solidity
5: import "../libraries/TransactionHelper.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IBootloaderUtilities.sol#L5

```solidity
5: import "../libraries/TransactionHelper.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IPaymaster.sol#L5

```solidity
5: import "./SystemContractHelper.sol";
6: import "./Utils.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/EfficientCall.sol#L5-L6

```solidity
7: import "./SystemContractsCaller.sol";
8: import "./Utils.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L7-L8

```solidity
6: import "./Utils.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L6

```solidity
5: import "../openzeppelin/token/ERC20/IERC20.sol";
6: import "../openzeppelin/token/ERC20/utils/SafeERC20.sol";
8: import "../interfaces/IPaymasterFlow.sol";
9: import "../interfaces/IContractDeployer.sol";
11: import "./RLPEncoder.sol";
12: import "./EfficientCall.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L5

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L6

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L8

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L9

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L11

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L12



```solidity
4: import "./EfficientCall.sol";

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/Utils.sol#L4




#### <ins>Recommended Mitigation Steps</ins>

Use specific imports syntax per solidity docs recommendation.



### <a href="#Summary">[NC&#x2011;14]</a><a name="NC&#x2011;14"> Use a more recent version of Solidity

<a href="https://blog.soliditylang.org/2021/04/21/solidity-0.8.4-release-announcement/">0.8.4</a>:
bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)

<a href="https://blog.soliditylang.org/2022/02/16/solidity-0.8.12-release-announcement/">0.8.12</a>: 
string.concat() instead of abi.encodePacked(<str>,<str>)

<a href="https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/">0.8.13</a>: 
Ability to use using for with a list of free functions

<a href="https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/">0.8.14</a>:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.

<a href="https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/">0.8.15</a>:

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

<a href="https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/">0.8.16</a>:

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

<a href="https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/">0.8.17</a>:

Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

<a href="https://blog.soliditylang.org/2023/02/22/solidity-0.8.19-release-announcement/">0.8.19</a>:
SMTChecker: New trusted mode that assumes that any compile-time available code is the actual used code, even in external calls. 
Bug Fixes: 
- Assembler: Avoid duplicating subassembly bytecode where possible.
- Code Generator: Avoid including references to the deployed label of referenced functions if they are called right away.
- ContractLevelChecker: Properly distinguish the case of missing base constructor arguments from having an unimplemented base function.
- SMTChecker: Fix internal error caused by unhandled z3 expressions that come from the solver when bitwise operators are used.
- SMTChecker: Fix internal error when using the custom NatSpec annotation to abstract free functions.
- TypeChecker: Also allow external library functions in using for.

#### <ins>Proof Of Concept</ins>


```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BytecodeCompressor.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/Constants.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/EmptyContract.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ImmutableSimulator.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/KnownCodesStorage.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L1Messenger.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/MsgValueSimulator.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IAccount.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IAccountCodeStorage.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IBootloaderUtilities.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IBytecodeCompressor.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IContractDeployer.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IEthToken.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IImmutableSimulator.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IKnownCodesStorage.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IL1Messenger.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IL2StandardToken.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IMailbox.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/INonceHolder.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IPaymaster.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IPaymasterFlow.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/ISystemContext.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/EfficientCall.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/RLPEncoder.sol#L3

```solidity
pragma solidity ^0.8;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractHelper.sol#L3

```solidity
pragma solidity ^0.8;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/SystemContractsCaller.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/UnsafeBytesCalldata.sol#L3

```solidity
pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/Utils.sol#L2



#### <ins>Recommended Mitigation Steps</ins>

Consider updating to a more recent solidity version.







### <a href="#Summary">[NC&#x2011;15]</a><a name="NC&#x2011;15"> Using `>`/`>=` without specifying an upper bound is unsafe

There will be breaking changes in future versions of solidity, and at that point the code will no longer be compatable. While the project may have the specific version to use in a configuration file, others that include your source files may not.

#### <ins>Proof Of Concept</ins>

```solidity
2: pragma solidity >=0.8.0;

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/Utils.sol#L2





### <a href="#Summary">[NC&#x2011;16]</a><a name="NC&#x2011;16"> Public Functions Not Called By The Contract Should Be Declared External Instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public.

#### <ins>Proof Of Concept</ins>


```solidity
function extendedAccountVersion(address _address) public view returns (AccountAbstractionVersion) {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L38

```solidity
function getRawNonce(address _address) public view returns (uint256) {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L56

```solidity
function increaseMinNonce(uint256 _value) public onlySystemCall returns (uint256 oldMinNonce) {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L64

```solidity
function setValueUnderNonce(uint256 _key, uint256 _value) public onlySystemCall {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L81

```solidity
function getValueUnderNonce(uint256 _key) public view returns (uint256) {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L101

```solidity
function getBlockTimestamp() public view returns (uint256 timestamp) {
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol#L97









### <a href="#Summary">[NC&#x2011;17]</a><a name="NC&#x2011;17"> Adding A Return Statement When The Function Defines A Named Return Variable, Is Redundant

#### <ins>Proof Of Concept</ins>


```solidity
128: return deploymentNonce
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/NonceHolder.sol#L128









### <a href="#Summary">[NC&#x2011;18]</a><a name="NC&#x2011;18"> Stop using `vInt == 27 || vInt == 28`

See <a href="https://twitter.com/alexberegszaszi/status/1534461421454606336?s=20&t=H0Dv3ZT2bicx00hLWJk7Fg">this</a> for reference

#### <ins>Proof Of Concept</ins>


```solidity
91: vInt == 27 || vInt == 28
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L91

```solidity
190: vInt == 27 || vInt == 28
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L190

```solidity
285: vInt == 27 || vInt == 28
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/BootloaderUtilities.sol#L285

```solidity
176: v == 27 || v == 28
```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/DefaultAccount.sol#L176








### <a href="#Summary">[NC&#x2011;19]</a><a name="NC&#x2011;19"> Use `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

#### <ins>Proof Of Concept</ins>


```solidity
98: return abi.encodePacked(IMailbox.finalizeEthWithdrawal.selector, _to, _amount)

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/L2EthToken.sol#L98

```solidity
132: keccak256(abi.encodePacked(_transaction.factoryDeps)),
                EfficientCall.keccak(_transaction.paymasterInput)
            )
        )
141: return keccak256(abi.encodePacked("/x19/x01", domainSeparator, structHash))

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L132

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/libraries/TransactionHelper.sol#L141






#### <ins>Recommended Mitigation Steps</ins>

Use `bytes.concat()` and upgrade to at least Solidity version 0.8.4 if required. 



### <a href="#Summary">[NC&#x2011;20]</a><a name="NC&#x2011;20"> Use `""` instead of `"0x00"` for empty bytes to avoid triggering extra computation on transfers

When tokens with hooks on transfer take in `bytes` arguments, there is often a length check so that 0 length bytes inputs are skipped. By using `"0x00"`, a non-0 `bytes` value is created which is semantically empty but would fail the 0-length check. This would increase the gas cost of processing such transfers.



#### <ins>Proof Of Concept</ins>


```solidity
87: if (codeHash == 0x00 && NONCE_HOLDER_SYSTEM_CONTRACT.getRawNonce(account) > 0) {

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol#L87

```solidity
118: codeHash != 0x00 &&

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/AccountCodeStorage.sol#L118

```solidity
264: require(NONCE_HOLDER_SYSTEM_CONTRACT.getRawNonce(_newAddress) == 0x00, "Account is occupied");

```

https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol#L264



#### <ins>Recommended Mitigation Steps</ins>

Consider changing the value to `""` instead, unless there is some other reason to retain the bytes value as-is.



