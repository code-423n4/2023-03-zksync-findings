# QA Report

## Table of Contents

- [Low-Risk Findings](#low-risk-findings)
  - [\[L-01\] User could overpay for gas when publishing bytecode to L1](#l-01-user-could-overpay-for-gas-when-publishing-bytecode-to-l1)

- [Non-Critical Findings](#non-critical-findings)
  - [\[NC-01\] Un-used `_l1PreimageHash` value in `_sendByteCodeToL1()`](#nc-01-un-used-_l1preimagehash-value-in-_sendbytecodetol1)
 
  - [\[NC-02\] Un-used parameter `_l1PreimageHash` in `markBytecodeAsPublished()` and `_markBytecodeAsPublished()`](#nc-02-un-used-parameter-_l1preimagehash-in-markbytecodeaspublished-and-_markbytecodeaspublished
)

 ## Low-Risk Findings

### [L-01] User could overpay for gas when publishing bytecode to L1

When publishing bytecode to L1, users could overpay for the transaction as `gasToPay` in `_sendBytecodeToL1()`  can be higher than actual gas burned. The reason is that the size of the pubdata to be published to L1 will be in multiples of 32 due to ABI encoding, but the computation of `gasToPay` does not take that into account and uses a fixed overhead based on BYTECODE_PUBLISHING_OVERHEAD.

#### Findings

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/KnownCodesStorage.sol#L97

```solidity
    function _sendBytecodeToL1(bytes32 _bytecodeHash, bytes32 _l1PreimageHash, uint256 _l1PreimageBytesLen) internal {
        // Burn gas to cover the cost of publishing pubdata on L1

        // Get the cost of 1 pubdata byte in gas
        uint256 meta = SystemContractHelper.getZkSyncMetaBytes();
        uint256 pricePerPubdataByteInGas = SystemContractHelper.getGasPerPubdataByteFromMeta(meta);

        uint256 gasToPay = (_l1PreimageBytesLen + BYTECODE_PUBLISHING_OVERHEAD) * pricePerPubdataByteInGas;
        _burnGas(Utils.safeCastToU32(gasToPay));

        // Send a log to L1 that bytecode should be known.
        // L1 smart contract will check the availability of bytecodeHash preimage.
        SystemContractHelper.toL1(true, _bytecodeHash, _l1PreimageHash);
    }
```
#### Recommended mitigation steps

Consider adjusting the gasToPay computation based on multiple of 32 instead of using a fixed overhead amount of `BYTECODE_PUBLISHING_OVERHEAD`.


## Non-Critical Findings




### [NC-01] Un-used `_l1PreimageHash` value in `_sendByteCodeToL1()`

Even though `_l1PreimageHash` is sent to L1 in `_sendByteCodeToL1()`, it is actually not required as it is not consumed in the L1 `Executor.sol`.


#### Findings

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/KnownCodesStorage.sol#L102

    function _sendBytecodeToL1(bytes32 _bytecodeHash, bytes32 _l1PreimageHash, uint256 _l1PreimageBytesLen) internal {
        // Burn gas to cover the cost of publishing pubdata on L1

        // Get the cost of 1 pubdata byte in gas
        uint256 meta = SystemContractHelper.getZkSyncMetaBytes();
        uint256 pricePerPubdataByteInGas = SystemContractHelper.getGasPerPubdataByteFromMeta(meta);

        uint256 gasToPay = (_l1PreimageBytesLen + BYTECODE_PUBLISHING_OVERHEAD) * pricePerPubdataByteInGas;
        _burnGas(Utils.safeCastToU32(gasToPay));

        // Send a log to L1 that bytecode should be known.
        // L1 smart contract will check the availability of bytecodeHash preimage.
        SystemContractHelper.toL1(true, _bytecodeHash, _l1PreimageHash);
    }


https://github.com/matter-labs/era-contracts/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L128-L132

````````
  } else if (logSender == L2_KNOWN_CODE_STORAGE_ADDRESS) {
      (bytes32 bytecodeHash, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + 24);
      require(bytecodeHash == L2ContractHelper.hashL2Bytecode(factoryDeps[currentBytecode]), "k3");

      currentBytecode = currentBytecode.uncheckedInc();
  }
````````

#### Recommended mitigation steps

Consider removing the unused `_l1PreimageHash` and not send it to L1.



### [NC-02] Un-used parameter `_l1PreimageHash` in `markBytecodeAsPublished()` and `_markBytecodeAsPublished()`

The parameter `_l1PreimageHash` is not necessary for `markBytecodeAsPublished()` and `_markBytecodeAsPublished()` as they are either set to 0 or not used for `_sendBytecodeToL1()`.

#### Findings

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/KnownCodesStorage.sol#L48

    function markBytecodeAsPublished(
        bytes32 _bytecodeHash,
        bytes32 _l1PreimageHash,
        uint256 _l1PreimageBytesLen
    ) external onlyBytecodeCompressor {
        _markBytecodeAsPublished(_bytecodeHash, _l1PreimageHash, _l1PreimageBytesLen, false);
    }

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/KnownCodesStorage.sol#L61

    function _markBytecodeAsPublished(
        bytes32 _bytecodeHash,
        bytes32 _l1PreimageHash,
        uint256 _l1PreimageBytesLen,
        bool _shouldSendToL1
    ) internal {


https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/KnownCodesStorage.sol#L36

    function markFactoryDeps(bool _shouldSendToL1, bytes32[] calldata _hashes) external onlyBootloader {
        unchecked {
            uint256 hashesLen = _hashes.length;
            for (uint256 i = 0; i < hashesLen; ++i) {
                uint256 codeLengthInBytes = Utils.bytecodeLenInBytes(_hashes[i]);
                _markBytecodeAsPublished(_hashes[i], 0, codeLengthInBytes, _shouldSendToL1);
            }
        }
    }

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/KnownCodesStorage.sol#L51

    function markBytecodeAsPublished(
        bytes32 _bytecodeHash,
        bytes32 _l1PreimageHash,
        uint256 _l1PreimageBytesLen
    ) external onlyBytecodeCompressor {
        _markBytecodeAsPublished(_bytecodeHash, _l1PreimageHash, _l1PreimageBytesLen, false);
    }

#### Recommended mitigation steps

Consider removing the parameter `_l1PreimageHash` in `markBytecodeAsPublished()` and `_markBytecodeAsPublished()`.


