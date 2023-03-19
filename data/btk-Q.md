| Total Low issues |
|------------------|

| Risk   | Issues Details                                                                                           | Number        |
|--------|----------------------------------------------------------------------------------------------------------|---------------|
| [L-01] | Low level calls with solidity version 0.8.14 and lower can result in optimiser bug                       | 45            |
| [L-02] | Integer overflow by unsafe casting                                                                       | 5             |
| [L-03] | Using `</<=` without specifying an upper bound is unsafe                                                 | 1             |
| [L-04] | Unused `receive()` Function Will Lock Ether In Contract                                                  | 2             |
| [L-05] | Constants in comparisons should appear on the left side                                                  | 9             |
| [L-06] | Signature Malleability of EVM's `ecrecover()`                                                            | 1             |
| [L-07] | Use `immutable` instead of `constant` for values such as a call to `keccak256()`                         | 2             |
| [L-08] | The #BytecodeCompressor._decodeRawBytecode() function cannot be compiled due to an error                 | 1             |
| [L-09] | Wrong DOMAIN_SEPARATOR                                                                                   | 1             |
  
| Total Non-Critical issues |
|---------------------------|

| Risk    | Issues Details                                                                                             | Number        |
|---------|------------------------------------------------------------------------------------------------------------|---------------|
| [NC-01] | Include return parameters in NatSpec comments                                                              | 32            |
| [NC-02] | Non-usage of specific imports                                                                              | 39            |
| [NC-03] | Lines are too long                                                                                         | 5             |
| [NC-04] | Use `require()` instead of `assert()`                                                                      | 2             |
| [NC-05] | Lack of event emit                                                                                         | 4             |
| [NC-06] | Function writing does not comply with the `Solidity Style Guide`                                           | All Contracts |
| [NC-07] | Use `bytes.concat()` and `string.concat()`                                                                 | 3             |
| [NC-08] | Reusable require statements should be changed to a modifier                                                | 3             |
| [NC-09] | Use a more recent version of solidity                                                                      | All Contracts |
| [NC-10] | Contracts should have full test coverage                                                                   | All Contracts |
| [NC-11] | Need Fuzzing test                                                                                          | All Contracts |
| [NC-12] | Generate perfect code headers every time                                                                   | All Contracts |
| [NC-13] | Lock pragmas to specific compiler version                                                                  | All Contracts |
| [NC-14] | Assembly Codes Specific – Should Have Comments                                                             | 45            |
| [NC-15] | Adding a `return` statement when the function defines a named return variable, is redundant                | 1             |
| [NC-16] | For functions, follow Solidity standard naming conventions                                                 | All Contracts |
| [NC-17] | Events that mark critical parameter changes should contain both the old and the new value                  | 1             |
| [NC-18] | Add NatSpec Mapping comment                                                                                | 5             |
| [NC-19] | Constants should be defined rather than using magic numbers                                                | 6             |
| [NC-20] | Use SMTChecker                                                                                             |               |
| [NC-21] | Use scientific notation rather than exponentiation                                                         | 6             |

## [L-01] Low level calls with solidity version 0.8.14 and lower can result in optimiser bug

#### Description

The protocol is using low level calls with solidity version 0.8.12 which can result in optimizer bug.

> Ref: https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d

#### Lines of code 

```solidity
                assembly {
                    revert(0, 0)
                }
```

- [MsgValueSimulator.sol#L42-L44](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L42-L44)

```solidity
            assembly {
                return(0, 0)
            }
```

- [MsgValueSimulator.sol#L54-L56](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L54-L56)

```solidity
        assembly {
            sstore(addressAsKey, _hash)
        }
```

- [AccountCodeStorage.sol#L39-L41](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol#L39-L41)

```solidity
        assembly {
            sstore(addressAsKey, constructedBytecodeHash)
        }
```

- [AccountCodeStorage.sol#L55-L57](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol#L55-L57)

```solidity
        assembly {
            codeHash := sload(addressAsKey)
        }
```

- [AccountCodeStorage.sol#L66-L68](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol#L66-L68)

```solidity
            assembly {
                sstore(_bytecodeHash, 1)
            }
```

- [KnownCodesStorage.sol#L73-L75](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/KnownCodesStorage.sol#L73-L75)

```solidity
        assembly {
            marker := sload(_hash)
        }
```

- [KnownCodesStorage.sol#L118-L120](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/KnownCodesStorage.sol#L118-L120)

```solidity
            assembly {
                return(0, 0)
            }
```

- [DefaultAccount.sol#L30-L32](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L30-L32)

```solidity
            assembly {
                return(0, 0)
            }
```

- [DefaultAccount.sol#L48-L50](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L48-L50)

```solidity
        assembly {
            r := mload(add(_signature, 0x20))
            s := mload(add(_signature, 0x40))
            v := and(mload(add(_signature, 0x41)), 0xff)
        }
```

- [DefaultAccount.sol#L171-L175](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L171-L175)

```solidity
        assembly {
            // Ensuring that the type is bool
            _isService := and(_isService, 1)
            // This `success` is always 0, but the method always succeeds
            // (except for the cases when there is not enough gas)
            let success := call(_isService, callAddr, _key, _value, 0xFFFF, 0, 0)
        }
```

- [SystemContractHelper.sol#L50-L56](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L50-L56)

```solidity
        assembly {
            addr := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)
        }
```

- [SystemContractHelper.sol#L65-L67](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L65-L67)

```solidity
        assembly {
            pop(staticcall(0, callAddr, 0, 0xFFFF, 0, 0))
        }
```

- [SystemContractHelper.sol#L76-L78](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L76-L78)

```solidity
        assembly {
            pop(staticcall(_farCallAbi, callAddr, 0, 0xFFFF, 0, 0))
        }
```

- [SystemContractHelper.sol#L87-L89](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L87-L89)

```solidity
        assembly {
            // Clearing input params as they are not cleaned by Solidity by default
            _value := and(_value, cleanupMask)
            pop(staticcall(_value, callAddr, 0, 0xFFFF, 0, 0))
        }
```

- [SystemContractHelper.sol#L97-L101](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L97-L101)

```solidity
        assembly {
            // Clearing input params as they are not cleaned by Solidity by default
            _shrink := and(_shrink, cleanupMask)
            pop(staticcall(_shrink, callAddr, 0, 0xFFFF, 0, 0))
        }
```

- [SystemContractHelper.sol#L109-L113](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L109-L113)

```solidity
        assembly {
            // Clearing input params as they are not cleaned by Solidity by default
            _gasToBurn := and(_gasToBurn, cleanupMask)
            success := staticcall(_rawParams, callAddr, _gasToBurn, 0xFFFF, 0, 0)
        }
```

- [SystemContractHelper.sol#L154-L158](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L154-L158)

```solidity
        assembly {
            // Clearing input params as they are not cleaned by Solidity by default
            _value := and(_value, cleanupMask)
            success := call(0, callAddr, _value, 0, 0xFFFF, 0, 0)
        }

```

- [SystemContractHelper.sol#L167-L171](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L167-L171)

```solidity
        assembly {
            pop(call(initializer, callAddr, value1, 0, 0xFFFF, 0, 0))
        }
```

- [SystemContractHelper.sol#L179-L181](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L179-L181)

```solidity
        assembly {
            pop(call(value1, callAddr, value2, 0, 0xFFFF, 0, 0))
        }
```

- [SystemContractHelper.sol#L189-L191](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L189-L191)

```solidity
        assembly {
            meta := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)
        }
```

- [SystemContractHelper.sol#L200-L202](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L200-L202)

```solidity
        assembly {
            callFlags := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)
        }
```

- [SystemContractHelper.sol#L289-L291](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L289-L291)

```solidity
        assembly {
            ptr := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)
        }
```

- [SystemContractHelper.sol#L300-L302](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L300-L302)

```solidity
        assembly {
            extraAbiData := staticcall(index, callAddr, 0, 0xFFFF, 0, 0)
        }
```

- [SystemContractHelper.sol#L313-L315](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L313-L315)

```solidity
        assembly {
            let offset := sub(_bytes.offset, 30)
            result := calldataload(add(offset, _start))
        }
```

- [UnsafeBytesCalldata.sol#L19-L22](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/UnsafeBytesCalldata.sol#L19-L22)

```solidity
        assembly {
            let offset := sub(_bytes.offset, 24)
            result := calldataload(add(offset, _start))
        }
```

- [UnsafeBytesCalldata.sol#L26-L29](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/UnsafeBytesCalldata.sol#L26-L29)

```solidity
        assembly {
            // In the first byte we write the encoded length as 0x80 + 0x14 == 0x94.
            mstore(add(encoded, 0x20), 0x9400000000000000000000000000000000000000000000000000000000000000)
            // Write address data without stripping zeros.
            mstore(add(encoded, 0x21), shiftedVal)
        }
```

- [RLPEncoder.sol#L11-L16](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/RLPEncoder.sol#L11-L16)

```solidity
                assembly {
                    mstore(add(encoded, 0x21), shiftedVal)
                }
```

- [RLPEncoder.sol#L34-L36](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/RLPEncoder.sol#L34-L36)

```solidity
                assembly {
                    mstore(add(encoded, 0x21), shiftedVal)
                }
```

- [RLPEncoder.sol#L69-L71](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/RLPEncoder.sol#L69-L71)

```solidity
        assembly {
            dataStart := add(data, 0x20)
        }
```

- [SystemContractsCaller.sol#L79-L81](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractsCaller.sol#L79-L81)

```solidity
            assembly {
                success := call(to, callAddr, 0, 0, farCallAbi, 0, 0)
            }
```

- [SystemContractsCaller.sol#L99-L101](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractsCaller.sol#L99-L101)

```solidity
            assembly {
                success := call(msgValueSimulator, callAddr, value, to, farCallAbi, forwardMask, 0)
            }
```

- [SystemContractsCaller.sol#L108-L110](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractsCaller.sol#L108-L110)

```solidity
        assembly {
            size := returndatasize()
        }
```

- [SystemContractsCaller.sol#L131-L133](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractsCaller.sol#L131-L133)

```solidity
        assembly {
            returndatacopy(add(returnData, 0x20), 0, size)
        }
```

- [SystemContractsCaller.sol#L136-L138](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractsCaller.sol#L136-L138)

```solidity
            assembly {
                let size := mload(returnData)
                revert(add(returnData, 0x20), size)
            }
```

- [SystemContractsCaller.sol#L159-L162](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractsCaller.sol#L159-L162)

```solidity
            assembly {
                success := call(_address, callAddr, 0, 0, 0xFFFF, 0, 0)
            }
```

- [EfficientCall.sol#L130](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L130)

```solidity
            assembly {
                success := call(msgValueSimulator, callAddr, _value, _address, 0xFFFF, forwardMask, 0)
            }
```

- [EfficientCall.sol#L143-L145](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L143-L145)

```solidity
        assembly {
            success := staticcall(_address, callAddr, 0, 0xFFFF, 0, 0)
        }
```

- [EfficientCall.sol#L158-L160](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L158-L160)

```solidity
        assembly {
            success := delegatecall(_address, callAddr, 0, 0xFFFF, 0, 0)
        }
```

- [EfficientCall.sol#L172-L174](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L172-L174)

```solidity
        assembly {
            // Clearing values before usage in assembly, since Solidity
            // doesn't do it by default
            _whoToMimic := and(_whoToMimic, cleanupMask)

            success := call(_address, callAddr, 0, 0, _whoToMimic, 0, 0)
        }
```

- [EfficientCall.sol#L198-L204](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L198-L204)

```solidity
            assembly {
                size := returndatasize()
            }
```

- [EfficientCall.sol#L213-L215](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L213-L215)

```solidity
            assembly {
                returndatacopy(add(returnData, 0x20), 0, size)
            }
```

- [EfficientCall.sol#L218-L220](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L218-L220)

```solidity
        assembly {
            let size := returndatasize()
            returndatacopy(0, 0, size)
            revert(0, size)
        }
```

- [EfficientCall.sol#L228-L232](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L228-L232)

```solidity
            assembly {
                dataOffset := _data.offset
            }
```

- [EfficientCall.sol#L255-L257](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L255-L257)

```solidity
        assembly {
            success := call(gas(), bootloaderAddr, amount, 0, 0, 0, 0)
        }
```

- [TransactionHelper.sol#L398-L400](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L398-L400)

#### Recommended Mitigation Steps

Consider upgrading to solidity 0.8.17

## [L-02] Integer overflow by unsafe casting

#### Description

Keep in mind that the version of solidity used, despite being greater than 0.8, does not prevent integer overflows during casting, it only does so in mathematical operations.

It is necessary to safely convert between the different numeric types.

#### Lines of code 

```solidity
            uint32(gasleft()),
```

- [DefaultAccount.sol#L83](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L83)

```solidity
            SystemContractHelper.ptrShrinkIntoActive(uint32(msg.data.length));
```

- [EfficientCall.sol#L252](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L252)

```solidity
            SystemContractHelper.ptrAddIntoActive(uint32(dataOffset));

```

- [EfficientCall.sol#L260](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L260)

```solidity
            uint32 shrinkTo = uint32(msg.data.length - (_data.length + dataOffset));
```

- [EfficientCall.sol#L262](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L262)

```solidity
        SystemContractHelper.setValueForNextFarCall(uint128(value));
```

- [MsgValueSimulator.sol#L60](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L60)

#### Recommended Mitigation Steps

Use OpenZeppelin [safeCast](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast) library.

## [L-03] Using `</<=` without specifying an upper bound is unsafe

#### Description

There will be breaking changes in future versions of solidity, and at that point your code will no longer be compatable. While you may have the specific version to use in a configuration file, others that include your source files may not.

#### Lines of code 

```solidity
pragma solidity >=0.8.0;
```

- [Utils.sol#L2](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/Utils.sol#L2)

#### Recommended Mitigation Steps

Use a fixed version of solidity.

## [L-04] Unused `receive()` Function Will Lock Ether In Contract

#### Description

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert.

#### Lines of code 

```solidity
    receive() external payable {}
```

- [EmptyContract.sol#L13](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/EmptyContract.sol#L13)

```solidity
    receive() external payable {
```

- [DefaultAccount.sol#L230](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L230)

#### Recommended Mitigation Steps

The function should call another function, otherwise it should revert.

## [L-05] Constants in comparisons should appear on the left side

#### Description

Constants in comparisons should appear on the left side, doing so will prevent typo [bug](https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html).

```solidity
        if (_data.length == 0) {
```

#### Lines of code 

```solidity
            require(dictionary.length % 8 == 0, "Dictionary length should be a multiple of 8");
```

- [BytecodeCompressor.sol#L42](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BytecodeCompressor.sol#L42)

```solidity
        if (ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.getRawCodeHash(_address) == 0) {
```

- [ContractDeployer.sol#L45](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L45)

```solidity
        if (_value == 0) {
```

- [EfficientCall.sol#L126](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L126)

```solidity
        if (_data.length == 0) {
```

- [EfficientCall.sol#L250](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L250)

```solidity
        if (getMarker(_bytecodeHash) == 0) {
```

- [KnownCodesStorage.sol#L65](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/KnownCodesStorage.sol#L65)

```solidity
                encoded[0] = (_val == 0) ? bytes1(uint8(128)) : bytes1(uint8(_val));
```

- [RLPEncoder.sol#L24](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/RLPEncoder.sol#L24)

```solidity
        if (value == 0) {
```

- [SystemContractsCaller.sol#L97](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractsCaller.sol#L97)

```solidity
        return _addr == uint256(uint160(address(ETH_TOKEN_SYSTEM_CONTRACT))) || _addr == 0;
```

- [TransactionHelper.sol#L94](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L94)

```solidity
        require(_bytecode.length % 32 == 0, "po");
```

- [Utils.sol#L78](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/Utils.sol#L78)

#### Recommended Mitigation Steps

Constants should appear on the left side:

```solidity
        if (0 == _data.length) {
```

## [L-06] Signature Malleability of EVM's `ecrecover()`

#### Description

The function calls the Solidity `ecrecover()` function directly to verify the given signatures. However, the `ecrecover()` EVM opcode allows malleable (non-unique) signatures and thus is susceptible to replay attacks.

Although a replay attack seems not possible for this contract, I recommend using the battle-tested OpenZeppelin's ECDSA library.

#### Lines of code 

```solidity
        address recoveredAddress = ecrecover(_hash, v, r, s);
```

- [DefaultAccount.sol#L189](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L189)

#### Recommended Mitigation Steps

Use the ecrecover function from OpenZeppelin's ECDSA library for signature verification. (Ensure using a version > 4.7.3 for there was a critical bug >= 4.1.0 < 4.7.3).

## [L-07] Use `immutable` instead of `constant` for values such as a call to `keccak256()`

#### Description

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

#### Lines of code 

```solidity
    bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");
```

- [TransactionHelper.sol#L81](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L81)

```solidity
    bytes32 constant EIP712_TRANSACTION_TYPE_HASH =
        keccak256(
            "Transaction(uint256 txType,uint256 from,uint256 to,uint256 gasLimit,uint256 gasPerPubdataByteLimit,uint256 maxFeePerGas,uint256 maxPriorityFeePerGas,uint256 paymaster,uint256 nonce,uint256 value,bytes data,bytes32[] factoryDeps,bytes paymasterInput)"
        );
```

- [TransactionHelper.sol#L83-L86](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L83-L86)

#### Recommended Mitigation Steps

Use `immutable` instead of `constants` 

## [L-08] The #BytecodeCompressor._decodeRawBytecode() function has an error

#### Description

There is an error in the function [`_decodeRawBytecode()`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BytecodeCompressor.sol#L82) has and extra colon at the end which will cause the compiler to issue warnings.

#### Lines of code 

```solidity
            encodedData = _rawCompressedData[2 + dictionaryLen * 8:];
```

- [BytecodeCompressor.sol#L82](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BytecodeCompressor.sol#L82)

#### Recommended Mitigation Steps

```solidity
            encodedData = _rawCompressedData[2 + dictionaryLen * 8];
```

## [L-09] Wrong DOMAIN_SEPARATOR

#### Description

The protocol is using EIP-712 to protect his users from replay and frontrunning attacks, but as we can see on this line:

```solidity
    bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");
```
The domaine seperator is missing two parameters (verifyingContract, salt) which will make it more vulnerable to the attacks mentioned above and may lead to unexpected behavior.

#### Lines of code 

```solidity
    bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");
```

- [TransactionHelper.sol#L81](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L81)

#### Recommended Mitigation Steps

Follow the [EIP-712](https://eips.ethereum.org/EIPS/eip-712#definition-of-domainseparator) to make the protocol more robust.

## [NC-01] Include return parameters in NatSpec comments

#### Description

If Return parameters are declared, you must prefix them with `/// @return`.
Some code analysis programs do analysis by reading [NatSpec](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html) details, if they can't see the `@return` tag, they do incomplete analysis.

#### Lines of code 

```solidity
    ) external payable returns (bytes32 bytecodeHash) {
```

- [BytecodeCompressor.sol#L38](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BytecodeCompressor.sol#L38)

```solidity
    ) internal pure returns (bytes calldata dictionary, bytes calldata encodedData) {
```

- [BytecodeCompressor.sol#L77](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BytecodeCompressor.sol#L77)

```solidity
    function getAccountInfo(address _address) external view returns (AccountInfo memory info) {
```

- [ContractDeployer.sol#L32](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L32)

```solidity
    function extendedAccountVersion(address _address) public view returns (AccountAbstractionVersion) {
```

- [ContractDeployer.sol#L38](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L38)

```solidity
    ) public pure override returns (address newAddress) {
```

- [ContractDeployer.sol#L113](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L113)

```solidity
    ) external payable override returns (address) {
```

- [ContractDeployer.sol#L132](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L132)

```solidity
    ) external payable override returns (address) {
```

- [ContractDeployer.sol#L148](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L148)

```solidity
    ) public payable override onlySystemCall returns (address) {
```

- [ContractDeployer.sol#L165](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L165)

```solidity
    ) public payable override onlySystemCall returns (address) {
```

- [ContractDeployer.sol#L186](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L186)

```solidity
    ) external payable override ignoreNonBootloader ignoreInDelegateCall returns (bytes4 magic) {
```

- [DefaultAccount.sol#L70](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L70)

```solidity
    ) internal returns (bytes4 magic) {
```

- [DefaultAccount.sol#L80](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L80)

```solidity
    function getMarker(bytes32 _hash) public view override returns (uint256 marker) {
```

- [KnownCodesStorage.sol#L117](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/KnownCodesStorage.sol#L117)

```solidity
    function sendToL1(bytes calldata _message) external override returns (bytes32 hash) {
```

- [L1Messenger.sol#L22](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L1Messenger.sol#L22)

```solidity
    function balanceOf(uint256 _account) external view override returns (uint256) {
```

- [L2EthToken.sol#L64](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L2EthToken.sol#L64)

```solidity
    function _getL1WithdrawMessage(address _to, uint256 _amount) internal pure returns (bytes memory) {
```

- [L2EthToken.sol#L97](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L2EthToken.sol#L97)

```solidity
    function name() external pure override returns (string memory) {
```

- [L2EthToken.sol#L103](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L2EthToken.sol#L103)

```solidity
    function symbol() external pure override returns (string memory) {
```

- [L2EthToken.sol#L109](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L2EthToken.sol#L109)

```solidity
    function decimals() external pure override returns (uint8) {
```

- [L2EthToken.sol#L115](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L2EthToken.sol#L115)

```solidity
    function _getAbiParams() internal view returns (uint256 value, bool isSystemCall, address to) {
```

- [MsgValueSimulator.sol#L22](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L22)

```solidity
    fallback(bytes calldata _data) external payable onlySystemCall returns (bytes memory) {
```

- [MsgValueSimulator.sol#L32](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L32)

```solidity
    function isNonceUsed(address _address, uint256 _nonce) public view returns (bool) {
```

- [NonceHolder.sol#L146](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L146)

```solidity
    function encodeAddress(address _val) internal pure returns (bytes memory encoded) {
```

- [RLPEncoder.sol#L6](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/RLPEncoder.sol#L6)

```solidity
    function encodeUint256(uint256 _val) internal pure returns (bytes memory encoded) {
```

- [RLPEncoder.sol#L19](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/RLPEncoder.sol#L19)

```solidity
    function encodeNonSingleBytesLen(uint64 _len) internal pure returns (bytes memory) {
```

- [RLPEncoder.sol#L44](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/RLPEncoder.sol#L44)

```solidity
    function encodeListLen(uint64 _len) internal pure returns (bytes memory) {
```

- [RLPEncoder.sol#L51](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/RLPEncoder.sol#L51)

```solidity
    function _encodeLength(uint64 _len, uint256 _offset) private pure returns (bytes memory encoded) {
```

- [RLPEncoder.sol#L55](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/RLPEncoder.sol#L55)

```solidity
    function _highestByteSet(uint256 _number) private pure returns (uint256 hbs) {
```

- [RLPEncoder.sol#L79](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/RLPEncoder.sol#L79)

```solidity
    function getBlockHashEVM(uint256 _block) external view returns (bytes32 hash) {
```

- [SystemContext.sol#L73](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L73)

```solidity
    function encodeHash(Transaction calldata _transaction) internal view returns (bytes32 resultHash) {
```

- [TransactionHelper.sol#L99](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L99)

```solidity
    function totalRequiredBalance(Transaction calldata _transaction) internal pure returns (uint256 requiredBalance) {
```

- [TransactionHelper.sol#L404](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L404)

```solidity
    function readUint16(bytes calldata _bytes, uint256 _start) internal pure returns (uint16 result) {
```

- [UnsafeBytesCalldata.sol#L18](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/UnsafeBytesCalldata.sol#L18)

```solidity
    function readUint64(bytes calldata _bytes, uint256 _start) internal pure returns (uint64 result) {
```

- [UnsafeBytesCalldata.sol#L25](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/UnsafeBytesCalldata.sol#L25)

#### Recommended Mitigation Steps

Include `@return` parameters in NatSpec comments

## [NC-02] Non-usage of specific imports

#### Description

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace. Instead, the Solidity docs recommend specifying imported symbols explicitly. 

- https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

#### Lines of code 

```solidity
import "./interfaces/IImmutableSimulator.sol";
```

- [ImmutableSimulator.sol#L5](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ImmutableSimulator.sol#L5)

```solidity
import "./interfaces/IL1Messenger.sol";
```

- [L1Messenger.sol#L5](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L1Messenger.sol#L5)

```solidity
import "./libraries/SystemContractHelper.sol";
```

- [L1Messenger.sol#L6](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L1Messenger.sol#L6)

```solidity
import "./libraries/EfficientCall.sol";
```

- [L1Messenger.sol#L7](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L1Messenger.sol#L7)

```solidity
import "./libraries/EfficientCall.sol";
```

- [MsgValueSimulator.sol#L5](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L5)

```solidity
import "./interfaces/IBytecodeCompressor.sol";
```

- [BytecodeCompressor.sol#L5](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BytecodeCompressor.sol#L5)

```solidity
import "./Constants.sol";
```

- [BytecodeCompressor.sol#L6](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BytecodeCompressor.sol#L6)

```solidity
import "./libraries/Utils.sol";
```

- [BytecodeCompressor.sol#L7](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BytecodeCompressor.sol#L7)

```solidity
import "./libraries/UnsafeBytesCalldata.sol";
```

- [BytecodeCompressor.sol#L8](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BytecodeCompressor.sol#L8)

```solidity
import "./interfaces/IAccountCodeStorage.sol";
```

- [AccountCodeStorage.sol#L5](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol#L5)

```solidity
import "./libraries/Utils.sol";
```

- [AccountCodeStorage.sol#L6](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol#L6)

```solidity
import "./interfaces/IKnownCodesStorage.sol";
```

- [KnownCodesStorage.sol#L5](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/KnownCodesStorage.sol#L5)

```solidity
import "./libraries/Utils.sol";
```

- [KnownCodesStorage.sol#L6](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/KnownCodesStorage.sol#L6)

```solidity
import "./libraries/SystemContractHelper.sol";
```

- [KnownCodesStorage.sol#L7](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/KnownCodesStorage.sol#L7)

```solidity
import "./interfaces/INonceHolder.sol";
```

- [NonceHolder.sol#L5](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L5)

```solidity
import "./interfaces/IContractDeployer.sol";
```

- [NonceHolder.sol#L6](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L6)

```solidity
import "./interfaces/IAccount.sol";
```

- [DefaultAccount.sol#L5](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L5)

```solidity
import "./libraries/TransactionHelper.sol";
```

- [DefaultAccount.sol#L6](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L6)

```solidity
import "./libraries/SystemContractHelper.sol";
```

- [DefaultAccount.sol#L7](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L7)

```solidity
import "./libraries/EfficientCall.sol";
```

- [DefaultAccount.sol#L8](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L8)

```solidity
import "./interfaces/IContractDeployer.sol";
```

- [ContractDeployer.sol#L6](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L6)

```solidity
import "./libraries/Utils.sol";
```

- [ContractDeployer.sol#L9](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L9)

```solidity
import "./libraries/EfficientCall.sol";
```

- [ContractDeployer.sol#L10](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L10)

```solidity
import "./interfaces/IBootloaderUtilities.sol";
```

- [BootloaderUtilities.sol#L5](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BootloaderUtilities.sol#L5)

```solidity
import "./libraries/TransactionHelper.sol";
```

- [BootloaderUtilities.sol#L6](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BootloaderUtilities.sol#L6)

```solidity
import "./libraries/RLPEncoder.sol";
```

- [BootloaderUtilities.sol#L7](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BootloaderUtilities.sol#L7)

```solidity
import "./libraries/EfficientCall.sol";
```

- [BootloaderUtilities.sol#L8](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BootloaderUtilities.sol#L8)

```solidity
import "./SystemContractsCaller.sol";
```

- [SystemContractHelper.sol#L7](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L7)

```solidity
import "./Utils.sol";
```

- [SystemContractHelper.sol#L8](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L8)

```solidity
import "./EfficientCall.sol";
```

- [Utils.sol#L4](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/Utils.sol#L4)

```solidity
import "./Utils.sol";
```

- [SystemContractsCaller.sol#L6](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractsCaller.sol#L6)

```solidity
import "./SystemContractHelper.sol";
```

- [EfficientCall.sol#L5](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L5)

```solidity
import "./Utils.sol";
```

- [EfficientCall.sol#L6](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L6)

```solidity
import "../openzeppelin/token/ERC20/IERC20.sol";
```

- [TransactionHelper.sol#L5](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L5)

```solidity
import "../openzeppelin/token/ERC20/utils/SafeERC20.sol";
```

- [TransactionHelper.sol#L6](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L6)

```solidity
import "../interfaces/IPaymasterFlow.sol";
```

- [TransactionHelper.sol#L8](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L8)

```solidity
import "../interfaces/IContractDeployer.sol";
```

- [TransactionHelper.sol#L9](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L9)

```solidity
import "./RLPEncoder.sol";
```

- [TransactionHelper.sol#L11](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L11)

```solidity
import "./EfficientCall.sol";
```

- [TransactionHelper.sol#L12](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L12)

#### Recommended Mitigation Steps

Use specific imports syntax per solidity docs recommendation.

## [NC-03] Lines are too long

#### Description

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length.

> Ref: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length

#### Lines of code

```solidity
 * @dev The behavior of some of the methods depends on the nonce ordering of the account. Nonce ordering is a mere suggestion and all the checks that are present
```

- [NonceHolder.sol#L23](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L23)

```solidity
import {CREATE2_PREFIX, CREATE_PREFIX, NONCE_HOLDER_SYSTEM_CONTRACT, ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT, FORCE_DEPLOYER, MAX_SYSTEM_CONTRACT_ADDRESS, KNOWN_CODE_STORAGE_CONTRACT, ETH_TOKEN_SYSTEM_CONTRACT, IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT} from "./Constants.sol";
```

- [ContractDeployer.sol#L7](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L7)

```solidity
 * 3. `ptr.pack` - Do the concatenation between the lowest 128 bits of the pointer itself and the highest 128 bits of `_value`. It is typically used to prepare the ABI for external calls.
```

- [EfficientCall.sol#L24](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L24)

```solidity
     * use https://solidity.readthedocs.io/en/latest/units-and-global-variables.html?highlight=abi.decode#abi-encoding-and-decoding-functions[`abi.decode`].
```

- [Address.sol#L82](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/utils/Address.sol#L82)

```solidity
            "Transaction(uint256 txType,uint256 from,uint256 to,uint256 gasLimit,uint256 gasPerPubdataByteLimit,uint256 maxFeePerGas,uint256 maxPriorityFeePerGas,uint256 paymaster,uint256 nonce,uint256 value,bytes data,bytes32[] factoryDeps,bytes paymasterInput)"
```

- [TransactionHelper.sol#L85](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L85)

## [NC-04] Use `require()` instead of `assert()`

#### Description

Assert should not be used except for tests, require should be used

Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process's available gas instead of returning it, as `request()/revert()` did.

The big difference between the two is that the `assert()` function when false, uses up all the remaining gas and reverts all the changes made.

Meanwhile, a `require()` statement when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay. This is the most common Solidity function used by developers for debugging and error handling.

Assertion should be avoided even after solidity version 0.8.0, because its documentation states "The Assert function generates an error of type `Panic(uint256)`. Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there's a mistake".

#### Lines of code 

```solidity
        assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);
```

- [DefaultAccount.sol#L225](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L225)

```solidity
        assert(_len != 1);
```

- [RLPEncoder.sol#L45](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/RLPEncoder.sol#L45)

#### Recommended Mitigation Steps

Use `require()` instead of `assert()`

## [NC-05] Lack of event emit

#### Description

The below methods do not emit an event when the state changes, something that it's very important for dApps and users.

#### Lines of code 

```solidity
    function setImmutables(address _dest, ImmutableData[] calldata _immutables) external override {
        require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");
        unchecked {
            uint256 immutablesLength = _immutables.length;
            for (uint256 i = 0; i < immutablesLength; ++i) {
                uint256 index = _immutables[i].index;
                bytes32 value = _immutables[i].value;
                immutableDataStorage[uint256(uint160(_dest))][index] = value;
            }
        }
    }
```

- [ImmutableSimulator.sol#L33-L43](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ImmutableSimulator.sol#L33-L43)

```solidity
    function setTxOrigin(address _newOrigin) external onlyBootloader {
        origin = _newOrigin;
    }
```

- [SystemContext.sol#L60-L62](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L60-L62)

```solidity
    function setGasPrice(uint256 _gasPrice) external onlyBootloader {
        gasPrice = _gasPrice;
    }
```

- [SystemContext.sol#L66-L68](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L66-L68)

```solidity
    function setNewBlock(
        bytes32 _prevBlockHash,
        uint256 _newTimestamp,
        uint256 _expectedNewNumber,
        uint256 _baseFee
    ) external onlyBootloader {
        (uint256 currentBlockNumber, uint256 currentBlockTimestamp) = getBlockNumberAndTimestamp();
        require(_newTimestamp >= currentBlockTimestamp, "Timestamps should be incremental");
        require(currentBlockNumber + 1 == _expectedNewNumber, "The provided block number is not correct");

        blockHash[currentBlockNumber] = _prevBlockHash;

        // Setting new block number and timestamp
        currentBlockInfo = (currentBlockNumber + 1) * BLOCK_INFO_BLOCK_NUMBER_PART + _newTimestamp;

        baseFee = _baseFee;

        // The correctness of this block hash and the timestamp will be checked on L1:
        SystemContractHelper.toL1(false, bytes32(_newTimestamp), _prevBlockHash);
    }
```

- [SystemContext.sol#L109-L128](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L109-L128)

#### Recommended Mitigation Steps

Emit event.

## [NC-06] Function writing does not comply with the `Solidity Style Guide`

#### Description

Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

Functions should be grouped according to their visibility and ordered:

- `constructor()`
- `receive()`  
- `fallback()`  
- `external / public / internal / private`
- `view / pure`

#### Lines of code 

- [All Contracts](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts)

#### Recommended Mitigation Steps

Follow Solidity Style Guide.

## [NC-07] Use `bytes.concat()` and `string.concat()`

#### Description

Solidity version 0.8.4 introduces: 

- `bytes.concat()` vs `abi.encodePacked(<bytes>,<bytes>)`
- `string.concat()` vs `abi.encodePacked(<string>,<string>)`

https://docs.soliditylang.org/en/v0.8.17/types.html?highlight=bytes.concat#the-functions-bytes-concat-and-string-concat

#### Lines of code 

```solidity
        return abi.encodePacked(IMailbox.finalizeEthWithdrawal.selector, _to, _amount);
```

- [L2EthToken.sol#L98](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L2EthToken.sol#L98)

```solidity
                keccak256(abi.encodePacked(_transaction.factoryDeps)),
```

- [TransactionHelper.sol#L132](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L132)

```solidity
        return keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));
```

- [TransactionHelper.sol#L141](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L141)

#### Recommended Mitigation Steps

Use `bytes.concat()` and `string.concat()`

## [NC-08] Reusable require statements should be changed to a modifier

#### Description

Reusable require statements should be changed to a modifier.

#### Lines of code 

```solidity
            require(vInt == 27 || vInt == 28, "Invalid v value");
```

- [BootloaderUtilities.sol#L91](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BootloaderUtilities.sol#L91)

```solidity
            require(vInt == 27 || vInt == 28, "Invalid v value");
```

- [BootloaderUtilities.sol#L190](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BootloaderUtilities.sol#L190)

```solidity
            require(vInt == 27 || vInt == 28, "Invalid v value");
```

- [BootloaderUtilities.sol#L285](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BootloaderUtilities.sol#L285)

## [NC-09] Use a more recent version of solidity

#### Description

For security, it is best practice to use the [latest Solidity version](https://github.com/ethereum/solidity/blob/develop/Changelog.md).

#### Lines of code 

- [All Contracts](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts)

#### Recommended Mitigation Steps

Old version of Solidity is used `(^0.8.0)`, newer version can be used `(0.8.17)`.

## [NC-10] Contracts should have full test coverage

#### Description

While 100% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit.

#### Lines of code 

- [All Contracts](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts)

#### Recommended Mitigation Steps

Line coverage percentage should be 100%.

## [NC-11] Need Fuzzing test

#### Description

In total 10 contracts, 20 `unchecked{}` are used, the functions used are critical. For this reason, there must be fuzzing tests in the tests of the project. Not seen in tests.

#### Lines of code 

- [All Contracts](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts)

#### Recommended Mitigation Steps

Use should fuzzing test like Echidna. As Alberto Cuesta Canada said: Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now.

Ref: https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05

## [NC-12] Generate perfect code headers every time

#### Description

I recommend using header for Solidity code layout and readability

> Ref: https://github.com/transmissions11/headers

#### Lines of code 

- [All Contracts](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts)

#### Recommended Mitigation Steps

```
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```

## [NC-13] Lock pragmas to specific compiler version

#### Description

Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally. 

> Ref: https://swcregistry.io/docs/SWC-103

#### Lines of code 

```solidity
pragma solidity ^0.8.0;
```

- [All Contracts](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts)

#### Recommended Mitigation Steps

[Ethereum Smart Contract Best Practices](https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/): Lock pragmas to specific compiler version. 

## [NC-14] Assembly Codes Specific – Should Have Comments  

#### Description

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Aseembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.

#### Lines of code 

```solidity
                assembly {
                    revert(0, 0)
                }
```

- [MsgValueSimulator.sol#L42-L44](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L42-L44)

```solidity
            assembly {
                return(0, 0)
            }
```

- [MsgValueSimulator.sol#L54-L56](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L54-L56)

```solidity
        assembly {
            sstore(addressAsKey, _hash)
        }
```

- [AccountCodeStorage.sol#L39-L41](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol#L39-L41)

```solidity
        assembly {
            sstore(addressAsKey, constructedBytecodeHash)
        }
```

- [AccountCodeStorage.sol#L55-L57](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol#L55-L57)

```solidity
        assembly {
            codeHash := sload(addressAsKey)
        }
```

- [AccountCodeStorage.sol#L66-L68](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol#L66-L68)

```solidity
            assembly {
                sstore(_bytecodeHash, 1)
            }
```

- [KnownCodesStorage.sol#L73-L75](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/KnownCodesStorage.sol#L73-L75)

```solidity
        assembly {
            marker := sload(_hash)
        }
```

- [KnownCodesStorage.sol#L118-L120](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/KnownCodesStorage.sol#L118-L120)

```solidity
            assembly {
                return(0, 0)
            }
```

- [DefaultAccount.sol#L30-L32](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L30-L32)

```solidity
            assembly {
                return(0, 0)
            }
```

- [DefaultAccount.sol#L48-L50](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L48-L50)

```solidity
        assembly {
            r := mload(add(_signature, 0x20))
            s := mload(add(_signature, 0x40))
            v := and(mload(add(_signature, 0x41)), 0xff)
        }
```

- [DefaultAccount.sol#L171-L175](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L171-L175)

```solidity
        assembly {
            // Ensuring that the type is bool
            _isService := and(_isService, 1)
            // This `success` is always 0, but the method always succeeds
            // (except for the cases when there is not enough gas)
            let success := call(_isService, callAddr, _key, _value, 0xFFFF, 0, 0)
        }
```

- [SystemContractHelper.sol#L50-L56](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L50-L56)

```solidity
        assembly {
            addr := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)
        }
```

- [SystemContractHelper.sol#L65-L67](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L65-L67)

```solidity
        assembly {
            pop(staticcall(0, callAddr, 0, 0xFFFF, 0, 0))
        }
```

- [SystemContractHelper.sol#L76-L78](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L76-L78)

```solidity
        assembly {
            pop(staticcall(_farCallAbi, callAddr, 0, 0xFFFF, 0, 0))
        }
```

- [SystemContractHelper.sol#L87-L89](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L87-L89)

```solidity
        assembly {
            // Clearing input params as they are not cleaned by Solidity by default
            _value := and(_value, cleanupMask)
            pop(staticcall(_value, callAddr, 0, 0xFFFF, 0, 0))
        }
```

- [SystemContractHelper.sol#L97-L101](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L97-L101)

```solidity
        assembly {
            // Clearing input params as they are not cleaned by Solidity by default
            _shrink := and(_shrink, cleanupMask)
            pop(staticcall(_shrink, callAddr, 0, 0xFFFF, 0, 0))
        }
```

- [SystemContractHelper.sol#L109-L113](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L109-L113)

```solidity
        assembly {
            // Clearing input params as they are not cleaned by Solidity by default
            _gasToBurn := and(_gasToBurn, cleanupMask)
            success := staticcall(_rawParams, callAddr, _gasToBurn, 0xFFFF, 0, 0)
        }
```

- [SystemContractHelper.sol#L154-L158](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L154-L158)

```solidity
        assembly {
            // Clearing input params as they are not cleaned by Solidity by default
            _value := and(_value, cleanupMask)
            success := call(0, callAddr, _value, 0, 0xFFFF, 0, 0)
        }

```

- [SystemContractHelper.sol#L167-L171](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L167-L171)

```solidity
        assembly {
            pop(call(initializer, callAddr, value1, 0, 0xFFFF, 0, 0))
        }
```

- [SystemContractHelper.sol#L179-L181](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L179-L181)

```solidity
        assembly {
            pop(call(value1, callAddr, value2, 0, 0xFFFF, 0, 0))
        }
```

- [SystemContractHelper.sol#L189-L191](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L189-L191)

```solidity
        assembly {
            meta := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)
        }
```

- [SystemContractHelper.sol#L200-L202](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L200-L202)

```solidity
        assembly {
            callFlags := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)
        }
```

- [SystemContractHelper.sol#L289-L291](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L289-L291)

```solidity
        assembly {
            ptr := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)
        }
```

- [SystemContractHelper.sol#L300-L302](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L300-L302)

```solidity
        assembly {
            extraAbiData := staticcall(index, callAddr, 0, 0xFFFF, 0, 0)
        }
```

- [SystemContractHelper.sol#L313-L315](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractHelper.sol#L313-L315)

```solidity
        assembly {
            let offset := sub(_bytes.offset, 30)
            result := calldataload(add(offset, _start))
        }
```

- [UnsafeBytesCalldata.sol#L19-L22](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/UnsafeBytesCalldata.sol#L19-L22)

```solidity
        assembly {
            let offset := sub(_bytes.offset, 24)
            result := calldataload(add(offset, _start))
        }
```

- [UnsafeBytesCalldata.sol#L26-L29](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/UnsafeBytesCalldata.sol#L26-L29)

```solidity
        assembly {
            // In the first byte we write the encoded length as 0x80 + 0x14 == 0x94.
            mstore(add(encoded, 0x20), 0x9400000000000000000000000000000000000000000000000000000000000000)
            // Write address data without stripping zeros.
            mstore(add(encoded, 0x21), shiftedVal)
        }
```

- [RLPEncoder.sol#L11-L16](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/RLPEncoder.sol#L11-L16)

```solidity
                assembly {
                    mstore(add(encoded, 0x21), shiftedVal)
                }
```

- [RLPEncoder.sol#L34-L36](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/RLPEncoder.sol#L34-L36)

```solidity
                assembly {
                    mstore(add(encoded, 0x21), shiftedVal)
                }
```

- [RLPEncoder.sol#L69-L71](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/RLPEncoder.sol#L69-L71)

```solidity
        assembly {
            dataStart := add(data, 0x20)
        }
```

- [SystemContractsCaller.sol#L79-L81](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractsCaller.sol#L79-L81)

```solidity
            assembly {
                success := call(to, callAddr, 0, 0, farCallAbi, 0, 0)
            }
```

- [SystemContractsCaller.sol#L99-L101](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractsCaller.sol#L99-L101)

```solidity
            assembly {
                success := call(msgValueSimulator, callAddr, value, to, farCallAbi, forwardMask, 0)
            }
```

- [SystemContractsCaller.sol#L108-L110](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractsCaller.sol#L108-L110)

```solidity
        assembly {
            size := returndatasize()
        }
```

- [SystemContractsCaller.sol#L131-L133](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractsCaller.sol#L131-L133)

```solidity
        assembly {
            returndatacopy(add(returnData, 0x20), 0, size)
        }
```

- [SystemContractsCaller.sol#L136-L138](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractsCaller.sol#L136-L138)

```solidity
            assembly {
                let size := mload(returnData)
                revert(add(returnData, 0x20), size)
            }
```

- [SystemContractsCaller.sol#L159-L162](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/SystemContractsCaller.sol#L159-L162)

```solidity
            assembly {
                success := call(_address, callAddr, 0, 0, 0xFFFF, 0, 0)
            }
```

- [EfficientCall.sol#L130](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L130)

```solidity
            assembly {
                success := call(msgValueSimulator, callAddr, _value, _address, 0xFFFF, forwardMask, 0)
            }
```

- [EfficientCall.sol#L143-L145](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L143-L145)

```solidity
        assembly {
            success := staticcall(_address, callAddr, 0, 0xFFFF, 0, 0)
        }
```

- [EfficientCall.sol#L158-L160](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L158-L160)

```solidity
        assembly {
            success := delegatecall(_address, callAddr, 0, 0xFFFF, 0, 0)
        }
```

- [EfficientCall.sol#L172-L174](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L172-L174)

```solidity
        assembly {
            // Clearing values before usage in assembly, since Solidity
            // doesn't do it by default
            _whoToMimic := and(_whoToMimic, cleanupMask)

            success := call(_address, callAddr, 0, 0, _whoToMimic, 0, 0)
        }
```

- [EfficientCall.sol#L198-L204](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L198-L204)

```solidity
            assembly {
                size := returndatasize()
            }
```

- [EfficientCall.sol#L213-L215](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L213-L215)

```solidity
            assembly {
                returndatacopy(add(returnData, 0x20), 0, size)
            }
```

- [EfficientCall.sol#L218-L220](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L218-L220)

```solidity
        assembly {
            let size := returndatasize()
            returndatacopy(0, 0, size)
            revert(0, size)
        }
```

- [EfficientCall.sol#L228-L232](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L228-L232)

```solidity
            assembly {
                dataOffset := _data.offset
            }
```

- [EfficientCall.sol#L255-L257](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L255-L257)

```solidity
        assembly {
            success := call(gas(), bootloaderAddr, amount, 0, 0, 0, 0)
        }
```

- [TransactionHelper.sol#L398-L400](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L398-L400)

#### Recommended Mitigation Steps

Include NatSpec in assembly codes.

## [NC-15] Adding a `return` statement when the function defines a named return variable, is redundant

#### Description

Adding a `return` statement when the function defines a named return variable, is redundant.

#### Lines of code

```solidity
    function getDeploymentNonce(address _address) external view returns (uint256 deploymentNonce) {
        uint256 addressAsKey = uint256(uint160(_address));
        (deploymentNonce, ) = _splitRawNonce(rawNonces[addressAsKey]);

        return deploymentNonce;
    }
```

- [NonceHolder.sol#L124-L129](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L124-L129)

#### Recommended Mitigation Steps

```solidity
    function getDeploymentNonce(address _address) external view returns (uint256 deploymentNonce) {
        uint256 addressAsKey = uint256(uint160(_address));
        (deploymentNonce, ) = _splitRawNonce(rawNonces[addressAsKey]);
    }
```

## [NC-16] For functions, follow Solidity standard naming conventions

#### Description

The protocol don't follow solidity standard naming convention.

> Ref: https://docs.soliditylang.org/en/v0.8.17/style-guide.html#naming-conventions

#### Lines of code 

- [All Contracts](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts)

#### Recommended Mitigation Steps

Follow solidity standard [naming convention](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#naming-conventions).

## [NC-17] Events that mark critical parameter changes should contain both the old and the new value

#### Description

Events that mark critical parameter changes should contain both the old and the new value.

#### Lines of code 

```solidity
    function updateAccountVersion(AccountAbstractionVersion _version) external onlySystemCall {
        _accountInfo[msg.sender].supportedAAVersion = _version;

        emit AccountVersionUpdated(msg.sender, _version);
    }
```

- [ContractDeployer.sol#L60-L64](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L60-L64)

#### Recommended Mitigation Steps

Add the old value to the event:

```solidity
    event AccountVersionUpdated(address indexed accountAddress,AccountAbstractionVersion oldAaVersion, AccountAbstractionVersion newAaVersion);
    
        function updateAccountVersion(AccountAbstractionVersion _version) external onlySystemCall {
        AccountAbstractionVersion oldVersion = _accountInfo[msg.sender].supportedAAVersion;
        _accountInfo[msg.sender].supportedAAVersion = _version;

        emit AccountVersionUpdated(msg.sender, oldVersion, _version);
    }

```

## [NC-18] Add NatSpec Mapping comment  

#### Description

Add NatSpec comments describing mapping keys and values.

```solidity
    mapping(address => uint256) balance;
```

#### Lines of code 

```solidity
    mapping(address => AccountInfo) internal _accountInfo;
```

- [ContractDeployer.sol#L24](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L24)

```solidity
    mapping(address => uint256) balance;
```

- [L2EthToken.sol#L19](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L2EthToken.sol#L19)

```solidity
    mapping(uint256 => uint256) internal rawNonces;
```

- [NonceHolder.sol#L35](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L35)

```solidity
    mapping(uint256 => mapping(uint256 => uint256)) internal nonceValues;
```

- [NonceHolder.sol#L40](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L40)

```solidity
    mapping(uint256 => bytes32) public blockHash;
```

- [SystemContext.sol#L56](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L56)

#### Recommended Mitigation Steps

```solidity
/// @dev address(user) => uint256(balance)
    mapping(address => uint256) balance;
```

## [NC-19] Constants should be defined rather than using magic numbers 

#### Description

Constants should be defined rather than using magic numbers.

#### Lines of code 

```solidity
        require(returnData.length == 32, "keccak256 returned invalid data");
```

- [EfficientCall.sol#L37](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L37)

```solidity
        require(returnData.length == 32, "sha returned invalid data");
```

- [EfficientCall.sol#L46](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L46)

```solidity
            pubdataLen = ((_message.length + 31) / 32) * 32 + 64;
```

- [L1Messenger.sol#L37](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L1Messenger.sol#L37)

```solidity
        require(_bytecode.length % 32 == 0, "po");
```

- [Utils.sol#L78](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/Utils.sol#L78)

```solidity
        uint256 bytecodeLenInWords = _bytecode.length / 32;
```

- [Utils.sol#L80](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/Utils.sol#L80)

#### Recommended Mitigation Steps

Define a constant.

## [NC-20] Use SMTChecker

#### Description

The highest tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification.

> Ref: https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19     

## [NC-21] Use scientific notation rather than exponentiation

#### Description

While the compiler knows to optimize away the exponentiation, it's still better coding practice to use idioms that do not require compiler optimization, if they exist.

#### Lines of code 

```solidity
            require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big");
```

- [BytecodeCompressor.sol#L43](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BytecodeCompressor.sol#L43)

```solidity
uint256 constant MAX_MSG_VALUE = 2 ** 128 - 1;
```

- [Constants.sol#L74](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/Constants.sol#L74)

```solidity
    uint256 constant DEPLOY_NONCE_MULTIPLIER = 2 ** 128;
```

- [NonceHolder.sol#L27](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L27)

```solidity
    uint256 constant MAXIMAL_MIN_NONCE_INCREMENT = 2 ** 32;
```

- [NonceHolder.sol#L30](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L30)

```solidity
    uint256 constant BLOCK_INFO_BLOCK_NUMBER_PART = 2 ** 128;
```

- [SystemContext.sol#L48](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L48)

```solidity
        require(bytecodeLenInWords < 2 ** 16, "pp"); // bytecode length must be less than 2^16 words
```

- [Utils.sol#L81](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/Utils.sol#L81)

#### Recommended Mitigation Steps

Use scientific notation rather than exponentiation.
