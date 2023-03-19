- ### `_storeConstructingByteCodeHashOnAddress` function comment is not clear
    
    “Ensures that the _newAddress and assigns a new contract hash to it” - It is unclear what is ensured about _newAddress. Actually the _newAddress value is not checked.
    
    ```solidity
    /// @notice Ensures that the _newAddress and assigns a new contract hash to it
    /// @param _newAddress The address of the deployed contract
    /// @param _bytecodeHash The correctly formatted hash of the bytecode.
    function _storeConstructingByteCodeHashOnAddress(address _newAddress, bytes32 _bytecodeHash) internal {
        // Set the "isConstructor" flag to the bytecode hash
        bytes32 constructingBytecodeHash = Utils.constructingBytecodeHash(_bytecodeHash);
        ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructingCodeHash(_newAddress, constructingBytecodeHash);
    }
    ```
    [link](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol/#L299)
    
- ### TODO in `codeL2EthToken` and `bootloader.yul`
    
    Todo-s should not exist in production code
    
    - [L2EthToken](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol/#L25)
    
    ```solidity
    /// NOTE: The deprecated from the previous upgrade storage variable.
    // TODO: Remove this variable with the new upgrade.
    address __DEPRECATED_l2Bridge = address(0);
    ```
    
    - [bootloader.yul](https://github.com/code-423n4/2023-03-zksync/blob/main/bootloader/bootloader.yul/#L1838)
    
    ```solidity
    // We use "ceil" here for formal reasons to allow easier approach for calculating the overhead in O(1) for L1
    // calculation.
    // TODO: possibly pay for pubdata overhead
    ```
    
- ### `ContractDeployer.forceDeployOnAddress` is external, but it has onlySelf modifier.
    
    `function forceDeployOnAddress(ForceDeployment calldata _deployment, address _sender) external payable onlySelf {`
    
    Since this function can only be called by self(onlySelf modifier), then it’s can visibility can be set to internal
    
    [source](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol/#L212)