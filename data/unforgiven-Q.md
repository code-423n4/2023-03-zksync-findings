**[[1]]** function DefaultAccount._validateTransaction() shouln't check `trx.value` for required balance, maybe user wanted the transaction to fail. also maybe paymaster is going to transfer required balance later.
```
        // The fact there is are enough balance for the account
        // should be checked explicitly to prevent user paying for fee for a
        // transaction that wouldn't be included on Ethereum.
        uint256 totalRequiredBalance = _transaction.totalRequiredBalance();
        require(totalRequiredBalance <= address(this).balance, "Not enough balance for fee + value");
```
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L102-L103



**[[2]]** function SystemContext.setGasPrice() comments are wrong
```solidity
    /// @notice Set the current tx origin.
    /// @param _gasPrice The new tx gasPrice.
    function setGasPrice(uint256 _gasPrice) external onlyBootloader {
        gasPrice = _gasPrice;
    }
```
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L64-L68


**[[3]]** In functions unsafeOverrideBlock() and setNewBlock() of SystemContext, code should check that timestamp  is less than BLOCK_INFO_BLOCK_NUMBER_PART, otherwise it can overflow and change the block number when combining them to calculate block info.
```solidity
        // Setting new block number and timestamp
        currentBlockInfo = (currentBlockNumber + 1) * BLOCK_INFO_BLOCK_NUMBER_PART + _newTimestamp;


        currentBlockInfo = (number) * BLOCK_INFO_BLOCK_NUMBER_PART + _newTimestamp;
```
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L109-L128
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L132-L135



**[[4]]** function BytecodeCompressor.publishCompressedBytecode() shouldn't be payable as it doesn't have any logic for transferred ETH. if users send eth by mistake their funds would be lost.
```solidity
    function publishCompressedBytecode(
        bytes calldata _bytecode,
        bytes calldata _rawCompressedData
    ) external payable returns (bytes32 bytecodeHash) {
```
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L35-L38


**[[5]]** function BytecodeCompressor.publishCompressedBytecode() should check that the bytecode doesn't published before, it's possible to publish multiple compressed format for single bytecodes which can create issue for indexers and 3rd parties. if a bytecode has published before it's not necessary to publish it again. also user or other protocol contract may lose funds if it calls this publish multiple times by mistake.
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L35-L68