# LOW FINDINGS

##

### [L-1] Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from other types

FILE : 2023-03-zksync/contracts/ImmutableSimulator.sol

      27: return immutableDataStorage[uint256(uint160(_dest))][_index];

[ImmutableSimulator.sol#L27](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ImmutableSimulator.sol#L27)

     40: immutableDataStorage[uint256(uint160(_dest))][index] = value;

[ImmutableSimulator.sol#L40](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ImmutableSimulator.sol#L40)

FILE : 2023-03-zksync/contracts/L1Messenger.sol

     49:  SystemContractHelper.toL1(true, bytes32(uint256(uint160(msg.sender))), hash);

[L1Messenger.sol#L49](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L49)

FILE: 2023-03-zksync/contracts/MsgValueSimulator.sol

    29: to = address(uint160(addressAsUint));

[MsgValueSimulator.sol#L29](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/MsgValueSimulator.sol#L29)

   60 : SystemContractHelper.setValueForNextFarCall(uint128(value));

[MsgValueSimulator.sol#L60](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/MsgValueSimulator.sol#L60)

FILE : 2023-03-zksync/contracts/BytecodeCompressor.sol

   50:  uint256 indexOfEncodedChunk = uint256(encodedData.readUint16(encodedDataPointer)) * 8;

[BytecodeCompressor.sol#L50](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L50)

    80: uint256 dictionaryLen = uint256(_rawCompressedData.readUint16(0));

[BytecodeCompressor.sol#L80](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L80)

FILE : 2023-03-zksync/contracts/AccountCodeStorage.sol

   38: uint256 addressAsKey = uint256(uint160(_address));

[AccountCodeStorage.sol#L38](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L38)

   54: uint256 addressAsKey = uint256(uint160(_address));

[AccountCodeStorage.sol#L54](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L54)

   64: uint256 addressAsKey = uint256(uint160(_address));

[AccountCodeStorage.sol#L64](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L64)

      77: address account = address(uint160(_input));
      78: if (uint160(account) <= CURRENT_MAX_PRECOMPILE_ADDRESS) {

[AccountCodeStorage.sol#L77-L78](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L77-L78)
   
    105:  address account = address(uint160(_input));

[AccountCodeStorage.sol#L105](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L105)

    117: uint160(account) > CURRENT_MAX_PRECOMPILE_ADDRESS &&

[AccountCodeStorage.sol#L117](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L117)

FILE : 2023-03-zksync/contracts/NonceHolder.sol

   57:  uint256 addressAsKey = uint256(uint160(_address));

[NonceHolder.sol#L57](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L57)

   67:  uint256 addressAsKey = uint256(uint160(msg.sender));

[NonceHolder.sol#L67](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L67)

   102: uint256 addressAsKey = uint256(uint160(msg.sender));

[NonceHolder.sol#L102](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L102)

   110:  uint256 addressAsKey = uint256(uint160(msg.sender));

[NonceHolder.sol#L110](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L110)

   125:   uint256 addressAsKey = uint256(uint160(_address));
   136:   uint256 addressAsKey = uint256(uint160(_address));
   147:   uint256 addressAsKey = uint256(uint160(_address));
   

##

### [L-2] LOSS OF PRECISION DUE TO ROUNDING

FILE : 2023-03-zksync/contracts/L1Messenger.sol

   37: pubdataLen = ((_message.length + 31) / 32) * 32 + 64;

[L1Messenger.sol#L37](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L37)

FILE : 2023-03-zksync/contracts/SystemContext.sol

   85: blockNumber = blockInfo / BLOCK_INFO_BLOCK_NUMBER_PART;

[SystemContext.sol#L85](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L85)

FILE : 2023-03-zksync/contracts/NonceHolder.sol

  172: deploymentNonce = _rawMinNonce / DEPLOY_NONCE_MULTIPLIER;

[NonceHolder.sol#L172](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L172)


##

### [L-3] REQUIRE MESSAGES ARE TOO SHORT AND UNCLEAR

FILE : 2023-03-zksync/contracts/L1Messenger.sol
 
   47: require(precompileCallSuccess);

[L1Messenger.sol#L47](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L47)

##

### [L-4] A single point of failure

The onlyDeployer role has a single point of failure and onlyDeployer can use critical a few functions.

Even if protocol admins/developers are not malicious there is still a chance for deployer keys to be stolen. In such a case, the attacker can cause serious damage to the project due to important functions. In such a case, users who have invested in project will suffer high financial losses

onlyDeployer function:

FILE : 2023-03-zksync/contracts/AccountCodeStorage.sol

   34: function storeAccountConstructingCodeHash(address _address, bytes32 _hash) external override onlyDeployer {

[AccountCodeStorage.sol#L34](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L34)

onlyBootloader() functions:

The onlyBootloader role has a single point of failure and onlyBootloader can use critical a few functions.

FILE : 2023-03-zksync/contracts/SystemContext.sol

    60 :  function setTxOrigin(address _newOrigin) external onlyBootloader {

    66:   function setGasPrice(uint256 _gasPrice) external onlyBootloader {

    109:  function setNewBlock(
        bytes32 _prevBlockHash,
        uint256 _newTimestamp,
        uint256 _expectedNewNumber,
        uint256 _baseFee
    ) external onlyBootloader {

   132: function unsafeOverrideBlock(uint256 _newTimestamp, uint256 number, uint256 _baseFee) external 
        onlyBootloader {

FILE : 2023-03-zksync/contracts/KnownCodesStorage.sol

    31: function markFactoryDeps(bool _shouldSendToL1, bytes32[] calldata _hashes) external onlyBootloader {

[KnownCodesStorage.sol#L31](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L31)

 onlyBytecodeCompressor() functions:

   function markBytecodeAsPublished(
        bytes32 _bytecodeHash,
        bytes32 _l1PreimageHash,
        uint256 _l1PreimageBytesLen
    ) external onlyBytecodeCompressor {

[KnownCodesStorage.sol#L46-L50](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L46-L50)    

  

This increases the risk of A single point of failure

Recommended Mitigation Steps

Add a time lock to critical functions. Admin-only functions that change critical parameters should emit events and have timelocks.

Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services.

Also detail them in documentation and NatSpec comments.

##

### [L-5] Hardcode the address causes no future updates

FILE : 2023-03-zksync/contracts/AccountCodeStorage.sol

  22:    bytes32 constant EMPTY_STRING_KECCAK = 0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470;

[AccountCodeStorage.sol#L22](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L22)

##

### [L-6] Lack of input validations

The _amount is not checked with non zero value

FILE : 2023-03-zksync/contracts/L2EthToken.sol

If we mint 0 amount this is waste of transaction 

    function mint(address _account, uint256 _amount) external override onlyBootloader {
        totalSupply += _amount;
        balance[_account] += _amount;
        emit Mint(_account, _amount);
     }

[L2EthToken.sol#L72-L76](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L72-L76)

### [L-7] The event emit with wrong address

The amount is reduced from address(this).So the withdraw event from address should be address(this) instead of msg.sender 

FILE : 2023-03-zksync/contracts/L2EthToken.sol

   93:   emit Withdrawal(msg.sender, _l1Receiver, amount);

[L2EthToken.sol#L93](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L93)

Recommended Mitigation:

 emit Withdrawal(address(this), _l1Receiver, amount);

##

### [L-8] _account can be address(0)

Can possible to mint wrongly to zero address 

FILE : 2023-03-zksync/contracts/L2EthToken.sol

   function mint(address _account, uint256 _amount) external override onlyBootloader {
        totalSupply += _amount;
        balance[_account] += _amount;
        emit Mint(_account, _amount);
    }

[L2EthToken.sol#L72-L76](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L72-L76)

Recommended Mitigation:

Add address(0) require check before mint amount to _account address 

  

# NON CRITICAL FINDINGS 

##

### [NC-1] USE A MORE RECENT VERSION OF SOLIDITY

Latest solidity version is 0.8.19 

FILE : 2023-03-zksync/contracts/ImmutableSimulator.sol

   3: pragma solidity ^0.8.0;

[ImmutableSimulator.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ImmutableSimulator.sol#L3)

FILE : 2023-03-zksync/contracts/L1Messenger.sol

   3: pragma solidity ^0.8.0;

[L1Messenger.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L3)

FILE: 2023-03-zksync/contracts/MsgValueSimulator.sol

   3: pragma solidity ^0.8.0;

[MsgValueSimulator.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/MsgValueSimulator.sol#L3)

FILE : 2023-03-zksync/contracts/BytecodeCompressor.sol

   3: pragma solidity ^0.8.0;

[BytecodeCompressor.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L3)

FILE : 2023-03-zksync/contracts/BytecodeCompressor.sol

   3: pragma solidity ^0.8.0;

[BytecodeCompressor.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L3)

FILE : 2023-03-zksync/contracts/AccountCodeStorage.sol

   3: pragma solidity ^0.8.0;

[AccountCodeStorage.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L3) 

FILE : 2023-03-zksync/contracts/L2EthToken.sol

     3: pragma solidity ^0.8.0;

[contracts/L2EthToken.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L3)

FILE : 2023-03-zksync/contracts/KnownCodesStorage.sol

   3: pragma solidity ^0.8.0;

[KnownCodesStorage.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L3)

Recommended Mitigation :

Consider using latest solidity version at least 0.8.17

##

### [NC-2] Named imports can be used

It’s possible to name the imports to improve code readability. E.g. import "@openzeppelin/contracts/token/ERC20/IERC20.sol"; can be rewritten as import {IERC20} from “import “@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol”

FILE : 2023-03-zksync/contracts/ImmutableSimulator.sol

   5: import "./interfaces/IImmutableSimulator.sol";


[ImmutableSimulator.sol#L5](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ImmutableSimulator.sol#L5)

FILE : 2023-03-zksync/contracts/L1Messenger.sol

     5: import "./interfaces/IL1Messenger.sol";
     6: import "./libraries/SystemContractHelper.sol";
     7: import "./libraries/EfficientCall.sol";

[L1Messenger.sol#L5-L7](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L5-L7)

FILE : 2023-03-zksync/contracts/BytecodeCompressor.sol

    5: import "./interfaces/IBytecodeCompressor.sol";
    6: import "./Constants.sol";
    7: import "./libraries/Utils.sol";
    8: import "./libraries/UnsafeBytesCalldata.sol";

[BytecodeCompressor.sol#L5-L8](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L5-L8)

FILE : 2023-03-zksync/contracts/BytecodeCompressor.sol

   5: import "./interfaces/IBytecodeCompressor.sol";
   6: import "./Constants.sol";
   7: import "./libraries/Utils.sol";
   8: import "./libraries/UnsafeBytesCalldata.sol";

[BytecodeCompressor.sol#L5-L8](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L5-L8)

FILE : 2023-03-zksync/contracts/KnownCodesStorage.sol

   5: import "./interfaces/IKnownCodesStorage.sol";
   6: import "./libraries/Utils.sol";
   7: import "./libraries/SystemContractHelper.sol";
   8: import {BOOTLOADER_FORMAL_ADDRESS, BYTECODE_PUBLISHING_OVERHEAD, BYTECODE_COMPRESSOR_CONTRACT} from 
      "./Constants.sol";

[KnownCodesStorage.sol#L5-L8](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L5-L8)

##

### [N-3] NOT USING THE NAMED RETURN VARIABLES ANYWHERE IN THE FUNCTION IS CONFUSING

Consider changing the variable to be an unnamed one.

FILE : 2023-03-zksync/contracts/L1Messenger.sol

     22: function sendToL1(bytes calldata _message) external override returns (bytes32 hash) {

[L1Messenger.sol#L22](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L22)

FILE: 2023-03-zksync/contracts/MsgValueSimulator.sol

    22: function _getAbiParams() internal view returns (uint256 value, bool isSystemCall, address to) {

[MsgValueSimulator.sol#L22](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/MsgValueSimulator.sol#L22)

[BytecodeCompressor.sol#L35-L38](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L35-L38)

(https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L75-L77)

(https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L63)
(https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L83)
(https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L91)
(https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L97)
[NonceHolder.sol#L124](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L124)
[NonceHolder.sol#L134](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L134)
[DefaultAccount.sol#L57-L70](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L57-L70)


##

### [NC-4] Pragma float

All the contracts in scope are floating the pragma version.

Recommendation
Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.

Note that pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or a package

##

### [NC-5] CONSTANTS SHOULD BE DEFINED RATHER THAN USING MAGIC NUMBERS

  It is bad practice to use numbers directly in code without explanation

FILE : 2023-03-zksync/contracts/L1Messenger.sol

   37: pubdataLen = ((_message.length + 31) / 32) * 32 + 64;

[L1Messenger.sol#L37](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L37)

FILE : 2023-03-zksync/contracts/BytecodeCompressor.sol

     42: require(dictionary.length % 8 == 0, "Dictionary length should be a multiple of 8");
     43: require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big");
     44: require(
                encodedData.length * 4 == _bytecode.length,
                "Encoded data length should be 4 times shorter than the original bytecode"
            );

[BytecodeCompressor.sol#L42-L47](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L42-L47)

##

### [NC-6] Need Fuzzing test

When using the "unchecked" keyword in Solidity, developers are essentially telling the compiler to skip certain checks that are normally performed during compilation, such as integer overflow/underflow checks. This can increase the risk of vulnerabilities and bugs in the resulting smart contract

2023-03-zksync/contracts/BytecodeCompressor.sol

   39:   unchecked {

[BytecodeCompressor.sol#L39](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L39)

   78: unchecked {

[BytecodeCompressor.sol#L78](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L78)

FILE : 2023-03-zksync/contracts/KnownCodesStorage.sol

   32: unchecked {

[KnownCodesStorage.sol#L32](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L32)

FILE : 2023-03-zksync/contracts/NonceHolder.sol

   70: unchecked {

[NonceHolder.sol#L70](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L70)

   116:  unchecked {

[NonceHolder.sol#L116](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L116)

  139: unchecked {

[NonceHolder.sol#L139](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L139)

##

### [NC-7] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does.

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Assembly removes several important security features of Solidity, which can make the code more insecure and more error-prone

FILE : 2023-03-zksync/contracts/AccountCodeStorage.sol

  39:   assembly {

[AccountCodeStorage.sol#L39](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L39)

  55:   assembly {

[AccountCodeStorage.sol#L55](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L55)

   66:  assembly {

[AccountCodeStorage.sol#L66](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L66)

FILE : 2023-03-zksync/contracts/KnownCodesStorage.sol

   73:   assembly {

[KnownCodesStorage.sol#L73](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L73)

   118:   assembly {

[KnownCodesStorage.sol#L118](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L118)

##

### [NC-8] Empty blocks should be removed or Emit something

2023-03-zksync/contracts/EmptyContract.sol

   11: fallback() external payable {}

   13: receive() external payable {}

[EmptyContract.sol#L11-L13](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/EmptyContract.sol#L11-L13)

##

### [NC-9] CONSTANTS SHOULD BE DEFINED RATHER THAN USING MAGIC NUMBERS 

Calculating the expression output every time is waste of work when the values are static. Instead of calculating every time we can simply use output value of this expression is the better way 

FILE : 2023-03-zksync/contracts/BytecodeCompressor.sol

   43:  require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big");

[BytecodeCompressor.sol#L43](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L43)

FILE : 2023-03-zksync/contracts/NonceHolder.sol

   27: uint256 constant DEPLOY_NONCE_MULTIPLIER = 2 ** 128;

   30: uint256 constant MAXIMAL_MIN_NONCE_INCREMENT = 2 ** 32;

[NonceHolder.sol#L27-L30](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L27-L30)

##

### [NC-10] INCLUDE RETURN PARAMETERS IN NATSPEC COMMENTS

[BytecodeCompressor.sol#L20-L38](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L20-L38)

[BytecodeCompressor.sol#L70-L77](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L70-L77)

[SystemContext.sol#L70-L73](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L70-L73)

[KnownCodesStorage.sol#L115-L117](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L115-L117)

##

### [NC-11] Imports can be grouped together

Consider importing Constants first, then all interfaces, then all libraries.

FILE : 2023-03-zksync/contracts/L2EthToken.sol

    5: import {IEthToken} from "./interfaces/IEthToken.sol";
    6: import {MSG_VALUE_SYSTEM_CONTRACT, DEPLOYER_SYSTEM_CONTRACT, BOOTLOADER_FORMAL_ADDRESS, 
     L1_MESSENGER_CONTRACT}  from "./Constants.sol";
    8: import {SystemContractHelper} from "./libraries/SystemContractHelper.sol";
    9: import {IMailbox} from "./interfaces/IMailbox.sol";

[L2EthToken.sol#L5-L8](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L5-L8)

##

### [NC-12] Large multiples of ten should use scientific notation

Using scientific notation for large multiples of ten will improve code readability

2023-03-zksync/contracts/SystemContext.sol

    40:   uint256 public difficulty = 2500000000000000;

[SystemContext.sol#L40](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L40)

##

### [NC-13] PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED EXTERNAL INSTEAD

2023-03-zksync/contracts/SystemContext.sol

   91: function getBlockNumber() public view returns (uint256 blockNumber) {

[SystemContext.sol#L97](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L97)

   97: function getBlockTimestamp() public view returns (uint256 timestamp) {

[SystemContext.sol#L97](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L97)

##

### [NC-14] INCLUDE @param IN NATSPEC COMMENTS

The parameter explanation is missing . Use @param to explain function parameters 

FILE : 2023-03-zksync/contracts/SystemContext.sol

    /// @notice The method that emulates `blockhash` opcode in EVM.
    /// @dev Just like the blockhash in the EVM, it returns bytes32(0), when
    /// when queried about hashes that are older than 256 blocks ago.
    function getBlockHashEVM(uint256 _block) external view returns (bytes32 hash) {

[SystemContext.sol#L70-L73](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L70-L73)

>  @param _baseFee is missing please add this 

    /// @notice Increments the current block number and sets the new timestamp
    /// @dev Called by the bootloader at the start of the block.
    /// @param _prevBlockHash The hash of the previous block.
    /// @param _newTimestamp The timestamp of the new block.
    /// @param _expectedNewNumber The new block's number
 +  /// @param _baseFee the baseFee amount 
    /// @dev Whie _expectedNewNumber can be derived as prevBlockNumber + 1, we still
    /// manually supply it here for consistency checks.
    /// @dev The correctness of the _prevBlockHash and _newTimestamp should be enforced on L1.
    function setNewBlock(
        bytes32 _prevBlockHash,
        uint256 _newTimestamp,
        uint256 _expectedNewNumber,
        uint256 _baseFee
    ) external onlyBootloader {

[SystemContext.sol#L101-L109](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L101-L109)

Recommended Mitigation:

    /// @notice The method that emulates `blockhash` opcode in EVM.
 +  /// @param _block the block number value
    /// @dev Just like the blockhash in the EVM, it returns bytes32(0), when
    /// when queried about hashes that are older than 256 blocks ago.
    function getBlockHashEVM(uint256 _block) external view returns (bytes32 hash) {

[KnownCodesStorage.sol#L123-L126](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L123-L126)

[KnownCodesStorage.sol#L115-L117](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L115-L117)

##

### [NC-15] The NATSPEC @notice function definition is inappropriate

FILE : 2023-03-zksync/contracts/SystemContext.sol

The setGasPrice() function definition is inappropriate

    /// @notice Set the current tx origin.
    /// @param _gasPrice The new tx gasPrice.
    function setGasPrice(uint256 _gasPrice) external onlyBootloader {

Recommended Mitigation:

   
 +  /// @notice Set the current gas price
    /// @param _gasPrice The new tx gasPrice.
    function setGasPrice(uint256 _gasPrice) external onlyBootloader {

##

### [NC-16] Function writing that does not comply with the Solidity Style Guide

Description
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

[Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html)

Functions should be grouped according to their visibility and ordered:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

FILE : 2023-03-zksync/contracts/KnownCodesStorage.sol

Write public functions top of the internal functions. Now the public function is written bellow the internal functions 

[KnownCodesStorage.sol#L90-L130](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L90-L130)


   


NC-1	Missing checks for address(0) when assigning values to address state variables	1
NC-2	require() / revert() statements should have descriptive reason strings	3
NC-3	TODO Left in the code	1
NC-4	Event is missing indexed fields	11
NC-5	Functions not used internally could be marked external	7

L-1	abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()	2
L-2	Do not use deprecated library functions	3
L-3	Empty Function Body - Consider commenting why	2
L-4	Initializers could be front-run	2
L-5	Unspecific compiler version pragma	1

    

