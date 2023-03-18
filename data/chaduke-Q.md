QA1. 
The mint() function fails to perform a zero address check for ``_account`` and a zero amount check for ``_amount``:

[https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L72-L76](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L72-L76)

Mitigation: both checks should be performed.


QA2. getBlockHashEVM() fails to consider ``block.number == _block`` in the first case. When, ``block.number == _block``, the function should return bytes32(0) as well. 

[https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L73-L79](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L73-L79)

Mitigation:
```diff
function getBlockHashEVM(uint256 _block) external view returns (bytes32 hash) {
-        if (block.number < _block || block.number - _block > 256) {
+        if (block.number <= _block || block.number - _block > 256) {
            hash = bytes32(0);
        } else {
            hash = blockHash[_block];
        }
    }
```

QA3.  ``setNewBlock()`` should be strictly increasing ``timestamp``.

The function ``setNewBlock()`` allows ``_newTimestamp == currentBlockTimestamp``. This should not be allowed. Timestamp needs to increase strictly from one block to another. 

```javascript
 require(_newTimestamp >= currentBlockTimestamp, "Timestamps should be incremental");
```

Mitigation:
```diff
function setNewBlock(
        bytes32 _prevBlockHash,
        uint256 _newTimestamp,
        uint256 _expectedNewNumber,
        uint256 _baseFee
    ) external onlyBootloader {
        (uint256 currentBlockNumber, uint256 currentBlockTimestamp) = getBlockNumberAndTimestamp();
-        require(_newTimestamp >= currentBlockTimestamp, "Timestamps should be incremental");
+        require(_newTimestamp > currentBlockTimestamp, "Timestamps should be incremental");
        require(currentBlockNumber + 1 == _expectedNewNumber, "The provided block number is not correct");

        blockHash[currentBlockNumber] = _prevBlockHash;

        // Setting new block number and timestamp
        currentBlockInfo = (currentBlockNumber + 1) * BLOCK_INFO_BLOCK_NUMBER_PART + _newTimestamp;

        baseFee = _baseFee;

        // The correctness of this block hash and the timestamp will be checked on L1:
        SystemContractHelper.toL1(false, bytes32(_newTimestamp), _prevBlockHash);
    }
``` 


