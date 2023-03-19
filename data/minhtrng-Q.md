## [01] Inconsistent overhead costs for publishing code

When publishing new code, an ABI encoded string will be sent to L1. Besides the bytecode this will require an additional 64 bytes for offset and length, as described [here](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L29-L39):

```js
// Calculate how many bytes of calldata will need to be transferred to L1.
// We published the data as ABI-encoded `bytes`, so we pay for:
// - message length in bytes, rounded up to a multiple of 32
// - 32 bytes of encoded offset
// - 32 bytes of encoded length

uint256 pubdataLen;
unchecked {
    pubdataLen = ((_message.length + 31) / 32) * 32 + 64;
}
uint256 gasToPay = pubdataLen * gasPerPubdataBytes;
```

However, the process for non-compressed bytecode uses another value (entrypoint is `KnownCodesStorage.markFactoryDeps`):


[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L90-L103)
```js
//  function _sendBytecodeToL1
uint256 gasToPay = (_l1PreimageBytesLen + BYTECODE_PUBLISHING_OVERHEAD) * pricePerPubdataByteInGas;
_burnGas(Utils.safeCastToU32(gasToPay));
```

`BYTECODE_PUBLISHING_OVERHEAD` is a constant with value `100`. After clarification with the sponsor this serves as a buffer to make sure that all costs are covered, and 64 bytes as above could be sufficient as well.

The suggestion of this QA is to standardize the overhead costs and use `BYTECODE_PUBLISHING_OVERHEAD` in both places, which also gets rid of magic numbers. 

For clarity of code it should also be documented at its definition how the value of `BYTECODE_PUBLISHING_OVERHEAD` has been chosen. In case of value `100` this would be 64 bytes for offset+length and additional buffer which is currently not clear

## [02] Can simplify call to precompile

The function `L1Messenger.sendToL1` performs the following operations to burn gas:

[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L41-L46)

```js
uint256 precompileParams = SystemContractHelper.packPrecompileParams(0, 0, 0, 0, 0);
bool precompileCallSuccess = SystemContractHelper.precompileCall(
    precompileParams,
    Utils.safeCastToU32(gasToPay)
);
```

The function `KnownCodesStorage._burnGas` does it like this:

[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L108-L111):

```js
bool precompileCallSuccess = SystemContractHelper.precompileCall(
    0, // The precompile parameters are formal ones. We only need the precompile call to burn gas.
    _gasToPay
);
```

The first approach can be replaced with the second one for brevity. It is suggested to have code comment in both cases for clarity or adjust the doc string from `precompileCall`

## [03] slight inconsistency in error handling for burning gas

When burning gas in `KnownCodesStorage._burnGas` the revert in a failure case contains an error message:

[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L108-L111):

```js
require(precompileCallSuccess, "Failed to charge gas");
```

When doing the same in `L1Messenger.sendToL1`, there is no error message in case of failure:

[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L41-L46)

```js
require(precompileCallSuccess);
```

It is recommended to add an error message in this case too, for ease of finding the root cause in case of failure. 

## [04]  Usage of magic numbers in UnsafeBytesCalldata

The library `UnsafeBytesCalldata` currently uses magic numbers that are correct, but seem odd at first glance:

[Link to code](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/UnsafeBytesCalldata.sol#L17-L31)

```js
function readUint16(bytes calldata _bytes, uint256 _start) internal pure returns (uint16 result) {
    assembly {
        let offset := sub(_bytes.offset, 30)
        result := calldataload(add(offset, _start))
    }
}

function readUint64(bytes calldata _bytes, uint256 _start) internal pure returns (uint64 result) {
    assembly {
        let offset := sub(_bytes.offset, 24)
        result := calldataload(add(offset, _start))
    }
}
```

Because their usage is unique, it is not necessary to extract constants, but it should be considered to add comments explaining why these are correct.