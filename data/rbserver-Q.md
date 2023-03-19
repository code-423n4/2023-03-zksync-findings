## QA REPORT

| |Issue|
|-|:-|
| [01] | `DefaultAccount._isValidSignature` FUNCTION CAN CONSIDER VALID SIGNATURES, WHICH HAVE `v` BEING 0 OR 1, THAT ARE SIGNED BY USING `web3.eth.sign` AS INVALID |
| [02] | `L2EthToken.withdraw` FUNCTION CAN CHECK IF `_l1Receiver` IS NOT `address(0)` |
| [03] | LACK OF ACCESS CONTROL FOR `L2EthToken.withdraw` FUNCTION |
| [04] | FLOATING PRAGMAS |
| [05] | UNNECESSARY CHECK IN `DefaultAccount._isValidSignature`'S RETURN VALUE |
| [06] | REDUNDANT RETURN STATEMENTS FOR FUNCTIONS WITH NAMED RETURNS CAN BE REMOVED |
| [07] | `require` CAN BE USED INSTEAD OF `assert` |
| [08] | CONSTANTS CAN BE USED INSTEAD OF MAGIC NUMBERS |
| [09] | `ForceDeployment` STRUCT CAN BE MOVED TO `IContractDeployer` INTERFACE |
| [10] | ORDER OF FUNCTIONS DOES NOT FOLLOW OFFICIAL STYLE GUIDE |
| [11] | ORDER OF LAYOUT DOES NOT FOLLOW OFFICIAL STYLE GUIDE |
| [12] | INCOMPLETE NATSPEC COMMENTS |
| [13] | MISSING NATSPEC COMMENTS |

## [01] `DefaultAccount._isValidSignature` FUNCTION CAN CONSIDER VALID SIGNATURES, WHICH HAVE `v` BEING 0 OR 1, THAT ARE SIGNED BY USING `web3.eth.sign` AS INVALID
The following `DefaultAccount._isValidSignature` function checks if `_signature`'s `v` is 27 or 28; if not, calling it will revert. Yet, a valid signature's `v` can be 0 or 1 when it is signed by using `web3.eth.sign`, which can be confirmed by the notes and examples in https://github.com/web3/web3.js/blob/0.20.7/DOCUMENTATION.md#web3ethsign and https://web3js.readthedocs.io/en/v1.8.1/web3-eth.html#sign. Due to the current `v` requirement in the `DefaultAccount._isValidSignature` function, such valid signature would be considered as invalid, which limits the protocol's usability and degrades the user experience.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L162-L192
```solidity
    function _isValidSignature(bytes32 _hash, bytes memory _signature) internal view returns (bool) {
        require(_signature.length == 65, "Signature length is incorrect");
        uint8 v;
        bytes32 r;
        bytes32 s;
        ...
        assembly {
            r := mload(add(_signature, 0x20))
            s := mload(add(_signature, 0x40))
            v := and(mload(add(_signature, 0x41)), 0xff)
        }
        require(v == 27 || v == 28, "v is neither 27 nor 28");

        ...
    }
```

As a mitigation, https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L176 can be updated to the following code.
```solidity
    if (v <= 1) {
        v += 27;
    }
    require(v == 27 || v == 28, "v is neither 27 nor 28");
```

## [02] `L2EthToken.withdraw` FUNCTION CAN CHECK IF `_l1Receiver` IS NOT `address(0)`
If `_l1Receiver` is accidentally set to `address(0)` when calling the following `L2EthToken.withdraw` function, the withdrawn funds can be lost on L1. To prevent this, please consider adding a sanity check that requires `_l1Receiver` to not be `address(0)` in the `L2EthToken.withdraw` function.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L80-L94
```solidity
    function withdraw(address _l1Receiver) external payable override {
        uint256 amount = msg.value;

        ...

        // Send the L2 log, a user could use it as proof of the withdrawal
        bytes memory message = _getL1WithdrawMessage(_l1Receiver, amount);
        L1_MESSENGER_CONTRACT.sendToL1(message);

        emit Withdrawal(msg.sender, _l1Receiver, amount);
    }
```

## [03] LACK OF ACCESS CONTROL FOR `L2EthToken.withdraw` FUNCTION
The following comment of the `L2EthToken` contract indicates that this contract should be used by the trusted system contracts to perform balance changes. Yet, the `L2EthToken.withdraw` function has no access control and can be called by contracts that are not the trusted system contracts. If a contract can call this function with a `msg.value` that is more than `totalSupply`, then `totalSupply` can underflow to a very large value without reverting; in this case, calling the `L2EthToken.mint` function that executes `totalSupply += _amount` can revert and be DOS'ed. To increase the level of security and reduce the potential attack surface, please consider updating the `L2EthToken.withdraw` function to be only callable by the corresponding trusted system contracts and not execute `balance[address(this)] -= amount; totalSupply -= amount;` within the `unchecked` block.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L13-L17
```solidity
 * @dev It does NOT provide interfaces for personal interaction with tokens like `transfer`, `approve`, and `transferFrom`.
 * Instead, this contract is used by the bootloader and `MsgValueSimulator`/`ContractDeployer` system contracts
 * to perform the balance changes while simulating the `msg.value` Ethereum behavior.
 */
contract L2EthToken is IEthToken {
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L80-L94
```solidity
    function withdraw(address _l1Receiver) external payable override {
        uint256 amount = msg.value;

        // Silent burning of the ether
        unchecked {
            balance[address(this)] -= amount;
            totalSupply -= amount;
        }

        // Send the L2 log, a user could use it as proof of the withdrawal
        bytes memory message = _getL1WithdrawMessage(_l1Receiver, amount);
        L1_MESSENGER_CONTRACT.sendToL1(message);

        emit Withdrawal(msg.sender, _l1Receiver, amount);
    }
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L72-L76
```solidity
    function mint(address _account, uint256 _amount) external override onlyBootloader {
        totalSupply += _amount;
        balance[_account] += _amount;
        emit Mint(_account, _amount);
    }
```

## [04] FLOATING PRAGMAS
( Please note that the following instances are not found in https://gist.github.com/Picodes/4fda93fac0db99cf9f2e260a073b38e4#l-5-unspecific-compiler-version-pragma. )

It is a best practice to lock pragmas instead of using floating pragmas to ensure that contracts are tested and deployed with the intended compiler version. Accidentally deploying contracts with different compiler versions can lead to unexpected risks and undiscovered bugs. Please consider locking pragmas for the following files.

```solidity
contracts\AccountCodeStorage.sol
  3: pragma solidity ^0.8.0; 

contracts\BootloaderUtilities.sol
  3: pragma solidity ^0.8.0; 

contracts\BytecodeCompressor.sol
  3: pragma solidity ^0.8.0; 

contracts\ContractDeployer.sol
  3: pragma solidity ^0.8.0; 

contracts\DefaultAccount.sol
  3: pragma solidity ^0.8.0; 

contracts\EmptyContract.sol
  3: pragma solidity ^0.8.0; 

contracts\ImmutableSimulator.sol
  3: pragma solidity ^0.8.0; 

contracts\KnownCodesStorage.sol
  3: pragma solidity ^0.8.0; 

contracts\L1Messenger.sol
  3: pragma solidity ^0.8.0; 

contracts\L2EthToken.sol
  3: pragma solidity ^0.8.0; 

contracts\MsgValueSimulator.sol
  3: pragma solidity ^0.8.0; 

contracts\NonceHolder.sol
  3: pragma solidity ^0.8.0; 

contracts\SystemContext.sol
  3: pragma solidity ^0.8.0; 
```

## [05] UNNECESSARY CHECK IN `DefaultAccount._isValidSignature`'S RETURN VALUE
For the following `DefaultAccount._isValidSignature` function, if `recoveredAddress == address(this)` is true, then `recoveredAddress != address(0)` is also true; If `recoveredAddress == address(this)` is false, then `recoveredAddress == address(this) && recoveredAddress != address(0)` is also false. Thus, checking `recoveredAddress != address(0)` is unnecessary in this function's return value. For a better code efficiency, please consider updating this function to just return `recoveredAddress == address(this)`.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L162-L192
```solidity
    function _isValidSignature(bytes32 _hash, bytes memory _signature) internal view returns (bool) {
        ...

        address recoveredAddress = ecrecover(_hash, v, r, s);

        return recoveredAddress == address(this) && recoveredAddress != address(0);
    }
```

## [06] REDUNDANT RETURN STATEMENTS FOR FUNCTIONS WITH NAMED RETURNS CAN BE REMOVED
When a function has a named return and a return statement, this return statement becomes redundant. To improve readability and maintainability, the named return variables can be set and used in and the return statements can be removed from the following functions.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L32-L34
```solidity
    function getAccountInfo(address _address) external view returns (AccountInfo memory info) {
        return _accountInfo[_address];
    }
```

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L124-L129
```solidity
    function getDeploymentNonce(address _address) external view returns (uint256 deploymentNonce) {
        uint256 addressAsKey = uint256(uint160(_address));
        (deploymentNonce, ) = _splitRawNonce(rawNonces[addressAsKey]);

        return deploymentNonce;
    }
```

## [07] `require` CAN BE USED INSTEAD OF `assert`
`assert` should be used for testing invariants and internal bugs while `require` should be used for checking function call returns, function inputs, valid conditions, etc. Hence, `assert` in the following `fallback` function can be changed to `require`.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L223-L228
```solidity
    fallback() external {
        // fallback of default account shouldn't be called by bootloader under no circumstances
        assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);

        // If the contract is called directly, behave like an EOA
    }
```

## [08] CONSTANTS CAN BE USED INSTEAD OF MAGIC NUMBERS
To improve readability and maintainability, a constant can be used instead of the magic number. Please consider replacing the magic numbers, such as `8`, used in the following code with constants.

```solidity
contracts\BytecodeCompressor.sol
  42: require(dictionary.length % 8 == 0, "Dictionary length should be a multiple of 8"); 
  43: require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big"); 
  45: encodedData.length * 4 == _bytecode.length, 
  50: uint256 indexOfEncodedChunk = uint256(encodedData.readUint16(encodedDataPointer)) * 8; 
  54: uint64 realChunk = _bytecode.readUint64(encodedDataPointer * 4); 
  81: dictionary = _rawCompressedData[2:2 + dictionaryLen * 8]; 
  82: encodedData = _rawCompressedData[2 + dictionaryLen * 8:]; 

contracts\L1Messenger.sol
  37: pubdataLen = ((_message.length + 31) / 32) * 32 + 64;   
```

## [09] `ForceDeployment` STRUCT CAN BE MOVED TO `IContractDeployer` INTERFACE
Because all other enum and struct, which are used in the `ContractDeployer` contract, are included in the `IContractDeployer` interface, the following `ForceDeployment` struct can also be moved from the `ContractDeployer` contract to the `IContractDeployer` interface for better code organization.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L195-L207
```solidity
    /// @notice A struct that describes a forced deployment on an address
    struct ForceDeployment {
        // The bytecode hash to put on an address
        bytes32 bytecodeHash;
        // The address on which to deploy the bytecodehash to
        address newAddress;
        // Whether to run the constructor on the force deployment
        bool callConstructor;
        // The value with which to initialize a contract
        uint256 value;
        // The constructor calldata
        bytes input;
    }
```

## [10] ORDER OF FUNCTIONS DOES NOT FOLLOW OFFICIAL STYLE GUIDE
https://docs.soliditylang.org/en/v0.8.19/style-guide.html#order-of-functions suggests that functions in a contract should be grouped and ordered as follows with the `view` and `pure` functions being placed last within each group:
1. constructor
2. receive function (if exists)
3. fallback function (if exists)
4. external
5. public
6. internal
7. private

The orders of functions in the following contracts do not follow the official style guide. Please consider updating these orders of functions accordingly.

```solidity
contracts\AccountCodeStorage.sol
  21: contract AccountCodeStorage is IAccountCodeStorage {   

contracts\ContractDeployer.sol
  21: contract ContractDeployer is IContractDeployer, ISystemContract {   

contracts\DefaultAccount.sol
  18: contract DefaultAccount is IAccount {   

contracts\EmptyContract.sol
  10: contract EmptyContract {   

contracts\ImmutableSimulator.sol
  17: contract ImmutableSimulator is IImmutableSimulator {   

contracts\KnownCodesStorage.sol
  17: contract KnownCodesStorage is IKnownCodesStorage {   

contracts\L2EthToken.sol
  17: contract L2EthToken is IEthToken {   

contracts\MsgValueSimulator.sol
  16: contract MsgValueSimulator is ISystemContract {   

contracts\NonceHolder.sol
  26: contract NonceHolder is INonceHolder, ISystemContract {   

contracts\SystemContext.sol
  14: contract SystemContext is ISystemContext {   
```

## [11] ORDER OF LAYOUT DOES NOT FOLLOW OFFICIAL STYLE GUIDE
https://docs.soliditylang.org/en/v0.8.19/style-guide.html#order-of-layout suggests that the following order should be used in an interface:
1. Type declarations
2. State variables
3. Events
4. Errors
5. Modifiers
6. Functions

Events are placed after functions in the following `IEthToken` interface. To follow the official style guide, please consider placing these events before all functions in this interface.

https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IEthToken.sol#L5-L27
```solidity
interface IEthToken {
    function balanceOf(uint256) external view returns (uint256);

    function transferFromTo(address _from, address _to, uint256 _amount) external;

    function totalSupply() external view returns (uint256);

    function name() external pure returns (string memory);

    function symbol() external pure returns (string memory);

    function decimals() external pure returns (uint8);

    function mint(address _account, uint256 _amount) external;

    function withdraw(address _l1Receiver) external payable;

    event Mint(address indexed account, uint256 amount);

    event Transfer(address indexed from, address indexed to, uint256 value);

    event Withdrawal(address indexed _l2Sender, address indexed _l1Receiver, uint256 _amount);
}
```

## [12] INCOMPLETE NATSPEC COMMENTS
NatSpec comments provide rich code documentation. The following functions miss the `@param` and/or `@return` comments. Please consider completing the NatSpec comments for these functions.

```solidity
contracts\BytecodeCompressor.sol
  35: function publishCompressedBytecode( 
  75: function _decodeRawBytecode(    

contracts\ContractDeployer.sol
  32: function getAccountInfo(address _address) external view returns (AccountInfo memory info) { 
  38: function extendedAccountVersion(address _address) public view returns (AccountAbstractionVersion) { 
  53: function _storeAccountInfo(address _address, AccountInfo memory _newInfo) internal { 
  110: function getNewAddressCreate( 
  128: function create2( 
  144: function create( 
  160: function create2Account( 
  181: function createAccount( 
  232: function forceDeployOnAddresses(ForceDeployment[] calldata _deployments) external payable { 
  294: function _ensureBytecodeIsKnown(bytes32 _bytecodeHash) internal view { 

contracts\DefaultAccount.sol
  66: function validateTransaction(   
  77: function _validateTransaction(   

contracts\KnownCodesStorage.sol
  117: function getMarker(bytes32 _hash) public view override returns (uint256 marker) {   
  126: function _validateBytecode(bytes32 _bytecodeHash) internal pure {   

contracts\L2EthToken.sol
  64: function balanceOf(uint256 _account) external view override returns (uint256) { 
  97: function _getL1WithdrawMessage(address _to, uint256 _amount) internal pure returns (bytes memory) { 
  103: function name() external pure override returns (string memory) {    
  109: function symbol() external pure override returns (string memory) {    
  115: function decimals() external pure override returns (uint8) {    

contracts\MsgValueSimulator.sol
  22: function _getAbiParams() internal view returns (uint256 value, bool isSystemCall, address to) { 

contracts\SystemContext.sol
  73: function getBlockHashEVM(uint256 _block) external view returns (bytes32 hash) { 
  109: function setNewBlock(   
  132: function unsafeOverrideBlock(uint256 _newTimestamp, uint256 number, uint256 _baseFee) external onlyBootloader { 
```

## [13] MISSING NATSPEC COMMENTS
NatSpec comments provide rich code documentation. The following functions miss NatSpec comments. Please consider adding NatSpec comments for these functions.

```solidity
contracts\ContractDeployer.sol
  249: function _nonSystemDeployOnAddress(

contracts\L1Messenger.sol
  22: function sendToL1(bytes calldata _message) external override returns (bytes32 hash) {  

contracts\MsgValueSimulator.sol
  32: fallback(bytes calldata _data) external payable onlySystemCall returns (bytes memory) {

contracts\NonceHolder.sol
  146: function isNonceUsed(address _address, uint256 _nonce) public view returns (bool) {
```