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


**[[6]]** the check for system deployer address in DefaultAccount._validateTransaction() is not correct as it convert DEPLOYER_SYSTEM_CONTRACT to uint256 and compare it to the `_transaction.to`. if `_transaction.to` was equal to `2^180 + DEPLOYER_SYSTEM_CONTRACT` then the check would be bypassed but the target address is in fact DEPLOYER_SYSTEM_CONTRACT. code should convert `_transaction.to` to address and then check it with DEPLOYER_SYSTEM_CONTRACT.
```solidity
        if (_transaction.to == uint256(uint160(address(DEPLOYER_SYSTEM_CONTRACT)))) {
            require(_transaction.data.length >= 4, "Invalid call to ContractDeployer");
        }
```
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L95-L97

**[[7]]** code of AccountCodeStorage.getCodeHash() and AccountCodeStorage.getCodeSize() would return wrong values for inputs that are larger than 2^161-1 because code convert the input to uint160 and then return value for that address so if the input was bigger than max value of the uint160 then it when casting happens the calculated address can belong to another contract address. for example there return value for `2^161 + Contract1_Address` will be the Contract1_Address information while there is no contract in `2^161 + Contract1_Address`. code should make sure that the input uint256 is less than 2^161.
```solidity
    function getCodeSize(uint256 _input) external view override returns (uint256 codeSize) {
        // We consider the account bytecode size of the last 20 bytes of the input, because
        // according to the spec "If EXTCODESIZE of A is X, then EXTCODESIZE of A + 2**160 is X".
        address account = address(uint160(_input));
        bytes32 codeHash = getRawCodeHash(account);
....
....
    function getCodeHash(uint256 _input) external view override returns (bytes32) {
        // We consider the account bytecode hash of the last 20 bytes of the input, because
        // according to the spec "If EXTCODEHASH of A is X, then EXTCODEHASH of A + 2**160 is X".
        address account = address(uint160(_input));
        if (uint160(account) <= CURRENT_MAX_PRECOMPILE_ADDRESS) {
            return EMPTY_STRING_KECCAK;
        }
....
....
```
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L74-L77
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L102-L106

**[[8]]** function L2EthToken.balanceOf(uint256) should check that input value is less than 2^161 because it would return wrong value for inputs that are not in address space(20 byte) and instead of 0 it can return bigger values and other contracts that use this function can be vulnerable if they rely on the return value of this function.
for example for address `2^161 + Address1` the function would return balance of `Address1` instead of 0.
```solidity
    function balanceOf(uint256 _account) external view override returns (uint256) {
        return balance[address(uint160(_account))];
    }
```
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L64-L66

