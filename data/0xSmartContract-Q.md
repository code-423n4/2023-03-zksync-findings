## Summary
### Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]|Signature Malleability of EVM's ecrecover()| 1 |
|[L-02]|Add `deadline` argument to structHash` variable | 1 |
|[L-03]|A single point of failure  | 10 |
|[L-04]|Insufficient coverage| All Contracts |
|[L-05]|Project Upgrade and Stop Scenario should be| All Contracts |
|[L-06]|Use Fuzzing Test for complicated code bases  | All Contracts |
|[L-07]|Add to Event-Emit for critical function| 1 |
|[L-08]|Keccak Constant values should used to immutable rather than constant| 1 |
|[L-09]|The dynamic array `factoryDeps` can become very large after a while| 1 |
|[NC-10] |Omissions in Events|1|
|[NC-11] |NatSpec comments should be increased in contracts |All Contracts|
|[NC-12] |`Function writing` that does not comply with the `Solidity Style Guide`| All Contracts |
|[NC-13] |Use descriptive names for Contracts and Libraries| All Contracts |
|[NC-14] |Use SMTChecker|All Contracts |
|[NC-15] |Constants on the left are better | 16 |
|[NC-16] |For modern and more readable code; update import usages| 55 |
|[NC-17] |Use `require` instead of `assert`| 2 |
|[NC-18] |Assembly Codes Specific – Should Have Comments | 46 |

Total 18 issues



### [L-01] Signature Malleability of EVM's ecrecover()

**Context:**

```solidity
contracts/DefaultAccount.sol:
  161      /// @return EIP1271_SUCCESS_RETURN_VALUE if the signaure is correct. It reverts otherwise.
  162:     function _isValidSignature(bytes32 _hash, bytes memory _signature) internal view returns (bool) {
  163:         require(_signature.length == 65, "Signature length is incorrect");
  164:         uint8 v;
  165:         bytes32 r;
  166:         bytes32 s;
  167:         // Signature loading code
  168:         // we jump 32 (0x20) as the first slot of bytes contains the length
  169:         // we jump 65 (0x41) per signature
  170:         // for v we load 32 bytes ending with v (the first 31 come from s) then apply a mask
  171:         assembly {
  172:             r := mload(add(_signature, 0x20))
  173:             s := mload(add(_signature, 0x40))
  174:             v := and(mload(add(_signature, 0x41)), 0xff)
  175:         }
  176:         require(v == 27 || v == 28, "v is neither 27 nor 28");
  177: 
  178:         // EIP-2 still allows signature malleability for ecrecover(). Remove this possibility and make the signature
  179:         // unique. Appendix F in the Ethereum Yellow paper (https://ethereum.github.io/yellowpaper/paper.pdf), defines
  180:         // the valid range for s in (301): 0 < s < secp256k1n ÷ 2 + 1, and for v in (302): v ∈ {27, 28}. Most
  181:         // signatures from current libraries generate a unique signature with an s-value in the lower half order.
  182:         //
  183:         // If your library generates malleable signatures, such as s-values in the upper range, calculate a new s-value
  184:         // with 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141 - s1 and flip v from 27 to 28 or
  185:         // vice versa. If your library also generates signatures with 0/1 for v instead 27/28, add 27 to v to accept
  186:         // these malleable signatures as well.
  187:         require(uint256(s) <= 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0, "Invalid s");
  188: 
  189:         address recoveredAddress = ecrecover(_hash, v, r, s);
  190: 
  191:         return recoveredAddress == address(this) && recoveredAddress != address(0);
  192:     }


```
**Description:**
Description: The function calls the Solidity ecrecover() function directly to verify the given signatures. However, the ecrecover() EVM opcode allows malleable (non-unique) signatures and thus is susceptible to replay attacks.

Although a replay attack seems not possible for this contract, I recommend using the battle-tested OpenZeppelin's ECDSA library.

*Recommendation:**
Use the ecrecover function from OpenZeppelin's ECDSA library for signature verification. (Ensure using a version > 4.7.3 for there was a critical bug >= 4.1.0 < 4.7.3).



### [L-02] Add `deadline` argument to structHash` variable

**Context:**

```diff
contracts/libraries/TransactionHelper.sol:
  116      /// @return keccak256 hash of the EIP-712 encoded representation of transaction
- 117:     function _encodeHashEIP712Transaction(Transaction calldata _transaction) private view returns (bytes32) {
+             function _encodeHashEIP712Transaction(Transaction calldata _transaction, uint256 deadline) private view returns (bytes32) {
+              require(block.timestamp <= deadline, "expired deadline");
  118:         bytes32 structHash = keccak256(
  119:             abi.encode(
  120:                 EIP712_TRANSACTION_TYPE_HASH,
  121:                 _transaction.txType,
  122:                 _transaction.from,
  123:                 _transaction.to,
  124:                 _transaction.gasLimit,
  125:                 _transaction.gasPerPubdataByteLimit,
  126:                 _transaction.maxFeePerGas,
  127:                 _transaction.maxPriorityFeePerGas,
  128:                 _transaction.paymaster,
  129:                 _transaction.nonce,
  130:                 _transaction.value,
+                         deadline
  131:                 EfficientCall.keccak(_transaction.data),
  132:                 keccak256(abi.encodePacked(_transaction.factoryDeps)),
  133:                 EfficientCall.keccak(_transaction.paymasterInput)
  134:             )
  135:         );


```
**Description:**
For security reasons, the `deadline` argument is used for transactions to occur in the same block, so it is recommended to use it with nonce

### [L-03]  A single point of failure 


## Impact

The `onlySystemCall` role has a single point of failure and `onlySystemCall` can use critical a few functions.


Even if protocol admins/developers are not malicious there is still a chance for Owner keys to be stolen. In such a case, the attacker can cause serious damage to the project due to important functions. In such a case, users who have invested in project will suffer high financial losses.



`onlySystemCall`  functions;
```js

10 results - 4 files

contracts/ContractDeployer.sol:
   60:     function updateAccountVersion(AccountAbstractionVersion _version) external onlySystemCall {
   69:     function updateNonceOrdering(AccountNonceOrdering _nonceOrdering) external onlySystemCall {
  160:     function create2Account(bytes32 _salt,bytes32 _bytecodeHash,bytes calldata _input,AccountAbstractionVersion _aaVersion) public payable override onlySystemCall returns (address) {
  176:     function createAccount(bytes32, bytes32 _bytecodeHash,bytes calldata _input,AccountAbstractionVersion _aaVersion) public payable override onlySystemCall returns (address) {

contracts/EventWriter.yul:
  101:             function onlySystemCall() {

contracts/MsgValueSimulator.sol:
  32:     fallback(bytes calldata _data) external payable onlySystemCall returns (bytes memory) {

contracts/NonceHolder.sol:
   64:     function increaseMinNonce(uint256 _value) public onlySystemCall returns (uint256 oldMinNonce) {
   81:     function setValueUnderNonce(uint256 _key, uint256 _value) public onlySystemCall {
  109:     function incrementMinNonceIfEquals(uint256 _expectedNonce) external onlySystemCall {
  134:     function incrementDeploymentNonce(address _address) external onlySystemCall returns (uint256 prevDeploymentNonce) {


```

This increases the risk of ```A single point of failure```




## Tools Used
Manuel Code Review


## Recommended Mitigation Steps
Add a time lock to  critical functions. Admin-only functions that change critical parameters should emit events and have timelocks. 

Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services.

Also detail them in documentation and NatSpec comments

### [L-04] Insufficient coverage

**Description:**
The test coverage rate of the project is unknown. Testing all functions is best practice in terms of security criteria.
Due to its capacity, test coverage is expected to be 100%.


```js
README.md:
  727: - What is the overall line coverage percentage provided by your tests?:  -
```


### [L-05] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation.
This can also be called an " EMERGENCY STOP (CIRCUIT BREAKER) PATTERN ".

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol


### [L-06]  Use Fuzzing Test for complicated code bases 


I recommend fuzzing testing in complex code structures, especially zkSync Era, where there is an innovative code base and a lot of computation.

**Recommendation:**

Use should fuzzing test like Echidna.

As Alberto Cuesta Canada said:

_Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now._



https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05


### [L-07] Add to Event-Emit for critical function


```solidity
contracts/ContractDeployer.sol:
  143      /// that is checked in the `createAccount` by `onlySystemCall` modifier.
  144:     function create(
  145:         bytes32 _salt,
  146:         bytes32 _bytecodeHash,
  147:         bytes calldata _input
  148:     ) external payable override returns (address) {
  149:         return createAccount(_salt, _bytecodeHash, _input, AccountAbstractionVersion.None);
  150:     }

```

### [L-08] Keccak Constant values should used to immutable rather than constant

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand.



```solidity

contracts/libraries/TransactionHelper.sol:
  81:     bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");

```

### [L-09] The dynamic array `factoryDeps` can become very large after a while


```solidity
1 result - 1 file

contracts/libraries/TransactionHelper.sol:
  65:     bytes32[] factoryDeps;

```

Recommendation
Set a limit to the length of the Array


### [N-10] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

The events should include the new value and old value where possible:

```diff

contracts/ContractDeployer.sol:
  59      /// @dev Note that it allows changes from account to non-account and vice versa.
 - 60:     function updateAccountVersion(AccountAbstractionVersion _version) external onlySystemCall {
+ 60:     function updateAccountVersion(AccountAbstractionVersion _newversion) external onlySystemCall {

+ 61:         uint256 _oldversion = _accountInfo[msg.sender].supportedAAVersion ;
- 61:         _accountInfo[msg.sender].supportedAAVersion = version;
+ 61:         _accountInfo[msg.sender].supportedAAVersion = _newversion;        
-  63:         emit AccountVersionUpdated(msg.sender, _version);
+ 63:         emit AccountVersionUpdated(msg.sender, _oldversion, _newversion);

  64:     }


```

### [N-11] NatSpec comments should be increased in contracts

**Context:**
All project have 282 functions but they have only 210 NatSpec’s

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
NatSpec comments should be increased in contracts


### [N-12] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
All Contracts

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

 constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

### [N-13] Use descriptive names for Contracts and Libraries


This codebase will be difficult to navigate, as there are no descriptive naming conventions that specify which files should contain meaningful logic.

Prefixes should be added like this by filing:

Interface I_
absctract contracts Abs_
Libraries Lib_

We recommend that you implement this or a similar agreement.

### [N-14] Use SMTChecker

The *highest* tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification.

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

### [N-15] Constants on the left are better

If you use the constant first you support structures that veil programming errors. And one should only produce code either to add functionality, fix an programming error or trying to support structures to avoid programming errors (like design patterns). 

https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html

```solidity

16 results - 11 files

contracts/AccountCodeStorage.sol:
  87:         if (codeHash == 0x00 && NONCE_HOLDER_SYSTEM_CONTRACT.getRawNonce(account) > 0) {

contracts/BytecodeCompressor.sol:
  42:             require(dictionary.length % 8 == 0, "Dictionary length should be a multiple of 8");

contracts/ContractDeployer.sol:
   45:         if (ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.getRawCodeHash(_address) == 0) {
  250:             ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.getCodeHash(uint256(uint160(_newAddress))) == 0x0,
  254:         require(NONCE_HOLDER_SYSTEM_CONTRACT.getRawNonce(_newAddress) == 0x00, "Account is occupied");

contracts/KnownCodesStorage.sol:
  65:         if (getMarker(_bytecodeHash) == 0) {

contracts/libraries/EfficientCall.sol:
  126:         if (_value == 0) {
  250:         if (_data.length == 0) {

contracts/libraries/RLPEncoder.sol:
  24:                 encoded[0] = (_val == 0) ? bytes1(uint8(128)) : bytes1(uint8(_val));

contracts/libraries/SystemContractsCaller.sol:
  97:         if (value == 0) {

contracts/libraries/TransactionHelper.sol:
  94:         return _addr == uint256(uint160(address(ETH_TOKEN_SYSTEM_CONTRACT))) || _addr == 0;

contracts/libraries/Utils.sol:
  51:         return _bytecodeHash[1] == 0x01;
  78:         require(_bytecode.length % 32 == 0, "po");

contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol:
  61:             (value == 0) || (token.allowance(address(this), spender) == 0),

contracts/openzeppelin/utils/Address.sol:
  263:             if (returndata.length == 0) {
```


### [N-16] For modern and more readable code; update import usages

**Context:**
```solidity

55 results - 20 files

contracts/AccountCodeStorage.sol:
  4  
  5: import "./interfaces/IAccountCodeStorage.sol";
  6: import "./libraries/Utils.sol";
  7  import {DEPLOYER_SYSTEM_CONTRACT, NONCE_HOLDER_SYSTEM_CONTRACT, CURRENT_MAX_PRECOMPILE_ADDRESS} from "./Constants.sol";

contracts/BootloaderUtilities.sol:
  4  
  5: import "./interfaces/IBootloaderUtilities.sol";
  6: import "./libraries/TransactionHelper.sol";
  7: import "./libraries/RLPEncoder.sol";
  8: import "./libraries/EfficientCall.sol";
  9  

contracts/BytecodeCompressor.sol:
  4  
  5: import "./interfaces/IBytecodeCompressor.sol";
  6: import "./Constants.sol";
  7: import "./libraries/Utils.sol";
  8: import "./libraries/UnsafeBytesCalldata.sol";
  9  

contracts/Constants.sol:
   4  
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
  15  

contracts/ContractDeployer.sol:
   5  import {ImmutableData} from "./interfaces/IImmutableSimulator.sol";
   6: import "./interfaces/IContractDeployer.sol";
   7  import {CREATE2_PREFIX, CREATE_PREFIX, NONCE_HOLDER_SYSTEM_CONTRACT, ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT, FORCE_DEPLOYER, MAX_SYSTEM_CONTRACT_ADDRESS, KNOWN_CODE_STORAGE_CONTRACT, ETH_TOKEN_SYSTEM_CONTRACT, IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT} from "./Constants.sol";
   8  
   9: import "./libraries/Utils.sol";
  10: import "./libraries/EfficientCall.sol";
  11  import {SystemContractHelper, ISystemContract} from "./libraries/SystemContractHelper.sol";

contracts/DefaultAccount.sol:
  4  
  5: import "./interfaces/IAccount.sol";
  6: import "./libraries/TransactionHelper.sol";
  7: import "./libraries/SystemContractHelper.sol";
  8: import "./libraries/EfficientCall.sol";
  9  import {BOOTLOADER_FORMAL_ADDRESS, NONCE_HOLDER_SYSTEM_CONTRACT, DEPLOYER_SYSTEM_CONTRACT, INonceHolder} from "./Constants.sol";

contracts/ImmutableSimulator.sol:
  4  
  5: import "./interfaces/IImmutableSimulator.sol";
  6  import {DEPLOYER_SYSTEM_CONTRACT} from "./Constants.sol";

contracts/KnownCodesStorage.sol:
  4  
  5: import "./interfaces/IKnownCodesStorage.sol";
  6: import "./libraries/Utils.sol";
  7: import "./libraries/SystemContractHelper.sol";
  8  import {BOOTLOADER_FORMAL_ADDRESS, BYTECODE_PUBLISHING_OVERHEAD, BYTECODE_COMPRESSOR_CONTRACT} from "./Constants.sol";

contracts/L1Messenger.sol:
  4  
  5: import "./interfaces/IL1Messenger.sol";
  6: import "./libraries/SystemContractHelper.sol";
  7: import "./libraries/EfficientCall.sol";
  8  

contracts/MsgValueSimulator.sol:
  4  
  5: import "./libraries/EfficientCall.sol";
  6  import {SystemContractHelper, ISystemContract} from "./libraries/SystemContractHelper.sol";

contracts/NonceHolder.sol:
  4  
  5: import "./interfaces/INonceHolder.sol";
  6: import "./interfaces/IContractDeployer.sol";
  7  import {ISystemContract} from "./libraries/SystemContractHelper.sol";

contracts/interfaces/IAccount.sol:
  4  
  5: import "../libraries/TransactionHelper.sol";
  6  

contracts/interfaces/IBootloaderUtilities.sol:
  4  
  5: import "../libraries/TransactionHelper.sol";
  6  

contracts/interfaces/IPaymaster.sol:
  4  
  5: import "../libraries/TransactionHelper.sol";
  6  

contracts/libraries/EfficientCall.sol:
  4  
  5: import "./SystemContractHelper.sol";
  6: import "./Utils.sol";
  7  import {SHA256_SYSTEM_CONTRACT, KECCAK256_SYSTEM_CONTRACT} from "../Constants.sol";

contracts/libraries/SystemContractHelper.sol:
  6  
  7: import "./SystemContractsCaller.sol";
  8: import "./Utils.sol";
  9  

contracts/libraries/SystemContractsCaller.sol:
  5  import {MSG_VALUE_SYSTEM_CONTRACT, MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT} from "../Constants.sol";
  6: import "./Utils.sol";
  7  

contracts/libraries/TransactionHelper.sol:
   4  
   5: import "../openzeppelin/token/ERC20/IERC20.sol";
   6: import "../openzeppelin/token/ERC20/utils/SafeERC20.sol";
   7  
   8: import "../interfaces/IPaymasterFlow.sol";
   9: import "../interfaces/IContractDeployer.sol";
  10  import {ETH_TOKEN_SYSTEM_CONTRACT, BOOTLOADER_FORMAL_ADDRESS} from "../Constants.sol";
  11: import "./RLPEncoder.sol";
  12: import "./EfficientCall.sol";
  13  

contracts/libraries/Utils.sol:
  3  
  4: import "./EfficientCall.sol";
  5  

contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol:
  5  
  6: import "../IERC20.sol";
  7: import "../extensions/IERC20Permit.sol";
  8: import "../../../utils/Address.sol";

```
**Description:**
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. 
This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

**Recommendation:**
`import {contract1 , contract2} from "filename.sol";`

A good example from the ArtGobblers project;
```js
import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";
```

### [N-17] Use `require` instead of `assert`

```solidity

2 results - 2 files

contracts/DefaultAccount.sol:
  224          // fallback of default account shouldn't be called by bootloader under no circumstances
  225:         assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);
  226  

contracts/libraries/RLPEncoder.sol:
  44      function encodeNonSingleBytesLen(uint64 _len) internal pure returns (bytes memory) {
  45:         assert(_len != 1);
  46          return _encodeLength(_len, 0x80);
```

**Description:**
Assert should not be used except for tests, `require` should be used

Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process's available gas instead of returning it, as request()/revert() did.

assert() and ruqire();
The big difference between the two is that the `assert()`function when false, uses up all the remaining gas and reverts all the changes made.
Meanwhile, a  `require()` function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay.
This is the most common Solidity function used by developers for debugging and error handling.

Assertion() should be avoided even after solidity version 0.8.0, because its documentation states "The Assert function generates an error of type Panic(uint256).
Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there's a mistake".

### [N-18] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does


This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Aseembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.


```solidity

46 results - 11 files

contracts/AccountCodeStorage.sol:
  38          uint256 addressAsKey = uint256(uint160(_address));
  39:         assembly {
  40:             sstore(addressAsKey, _hash)
  41          }

  54          uint256 addressAsKey = uint256(uint160(_address));
  55:         assembly {
  56:             sstore(addressAsKey, constructedBytecodeHash)
  57          }

  65  
  66:         assembly {
  67:             codeHash := sload(addressAsKey)
  68          }

contracts/DefaultAccount.sol:
   29              // If function was called outside of the bootloader, behave like an EOA.
   30:             assembly {
   31: 

   31                  return(0, 0)

   47              // If the function was delegate called, behave like an EOA.
   48:             assembly {
   49: 

   49                  return(0, 0)

  170          // for v we load 32 bytes ending with v (the first 31 come from s) then apply a mask
  171:         assembly {
  172: 
  172              r := mload(add(_signature, 0x20))

contracts/KnownCodesStorage.sol:
   72              // Save as known, to not resend the log to L1
   73:             assembly {
   74: 

   74                  sstore(_bytecodeHash, 1)

  117      function getMarker(bytes32 _hash) public view override returns (uint256 marker) {
  118:         assembly {
  119: 
  119              marker := sload(_hash)

contracts/MsgValueSimulator.sol:
  41              if (!success) {
  42:                 assembly {
  43: 

  43                      revert(0, 0)

  53              // the EVM's one, i.e. returndata should be empty.
  54:             assembly {
  55: 
  55                  return(0, 0)

contracts/libraries/EfficientCall.sol:
  129              address callAddr = RAW_FAR_CALL_BY_REF_CALL_ADDRESS;
  130:             assembly {
  131: 

  131                  success := call(_address, callAddr, 0, 0, 0xFFFF, 0, 0)

  142  
  143:             assembly {
  144: 

  144                  success := call(msgValueSimulator, callAddr, _value, _address, 0xFFFF, forwardMask, 0)

  157          address callAddr = RAW_FAR_CALL_BY_REF_CALL_ADDRESS;
  158:         assembly {
  159: 

  159              success := staticcall(_address, callAddr, 0, 0xFFFF, 0, 0)

  171          address callAddr = RAW_FAR_CALL_BY_REF_CALL_ADDRESS;
  172:         assembly {
  173: 

  173              success := delegatecall(_address, callAddr, 0, 0xFFFF, 0, 0)

  197          uint256 cleanupMask = ADDRESS_MASK;
  198:         assembly {
  199: 

  199              // Clearing values before usage in assembly, since Solidity

  212              uint256 size;
  213:             assembly {
  214: 

  214                  size := returndatasize()

  217              returnData = new bytes(size);
  218:             assembly {
  219: 

  219                  returndatacopy(add(returnData, 0x20), 0, size)

  227      function propagateRevert() internal pure {
  228:         assembly {
  229: 

  229              let size := returndatasize()

  254              uint256 dataOffset;
  255:             assembly {
  256: 
  256                  dataOffset := _data.offset

contracts/libraries/RLPEncoder.sol:
  10          bytes20 shiftedVal = bytes20(_val);
  11:         assembly {
  12: 

  12              // In the first byte we write the encoded length as 0x80 + 0x14 == 0x94.

  33  
  34:                 assembly {
  35: 

  35                      mstore(add(encoded, 0x21), shiftedVal)

  68  
  69:                 assembly {
  70: 
  70                      mstore(add(encoded, 0x21), shiftedVal)

contracts/libraries/SystemContractHelper.sol:
   49          address callAddr = TO_L1_CALL_ADDRESS;
   50:         assembly {
   51: 

   51              // Ensuring that the type is bool

   64          address callAddr = CODE_ADDRESS_CALL_ADDRESS;
   65:         assembly {
   66: 

   66              addr := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)

   75          address callAddr = LOAD_CALLDATA_INTO_ACTIVE_PTR_CALL_ADDRESS;
   76:         assembly {
   77: 

   77              pop(staticcall(0, callAddr, 0, 0xFFFF, 0, 0))

   86          address callAddr = PTR_PACK_INTO_ACTIVE_CALL_ADDRESS;
   87:         assembly {
   88: 

   88              pop(staticcall(_farCallAbi, callAddr, 0, 0xFFFF, 0, 0))

   96          uint256 cleanupMask = UINT32_MASK;
   97:         assembly {
   98: 

   98              // Clearing input params as they are not cleaned by Solidity by default

  108          uint256 cleanupMask = UINT32_MASK;
  109:         assembly {
  110: 

  110              // Clearing input params as they are not cleaned by Solidity by default

  153          uint256 cleanupMask = UINT32_MASK;
  154:         assembly {
  155: 

  155              // Clearing input params as they are not cleaned by Solidity by default

  166          address callAddr = SET_CONTEXT_VALUE_CALL_ADDRESS;
  167:         assembly {
  168: 

  168              // Clearing input params as they are not cleaned by Solidity by default

  178          address callAddr = EVENT_INITIALIZE_ADDRESS;
  179:         assembly {
  180: 

  180              pop(call(initializer, callAddr, value1, 0, 0xFFFF, 0, 0))

  188          address callAddr = EVENT_WRITE_ADDRESS;
  189:         assembly {
  190: 

  190              pop(call(value1, callAddr, value2, 0, 0xFFFF, 0, 0))

  199          address callAddr = META_CALL_ADDRESS;
  200:         assembly {
  201: 

  201              meta := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)

  288          address callAddr = CALLFLAGS_CALL_ADDRESS;
  289:         assembly {
  290: 

  290              callFlags := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)

  299          address callAddr = PTR_CALLDATA_CALL_ADDRESS;
  300:         assembly {
  301: 

  301              ptr := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)

  312          address callAddr = GET_EXTRA_ABI_DATA_ADDRESS;
  313:         assembly {
  314: 
  314              extraAbiData := staticcall(index, callAddr, 0, 0xFFFF, 0, 0)

contracts/libraries/SystemContractsCaller.sol:
   78          uint32 dataStart;
   79:         assembly {
   80: 

   80              dataStart := add(data, 0x20)

   98              // Doing the system call directly
   99:             assembly {
  100: 

  100                  success := call(to, callAddr, 0, 0, farCallAbi, 0, 0)

  107  
  108:             assembly {
  109: 

  109                  success := call(msgValueSimulator, callAddr, value, to, farCallAbi, forwardMask, 0)

  130          uint256 size;
  131:         assembly {
  132: 

  132              size := returndatasize()

  135          returnData = new bytes(size);
  136:         assembly {
  137: 

  137              returndatacopy(add(returnData, 0x20), 0, size)

  158          if (!success) {
  159:             assembly {
  160: 
  160                  let size := mload(returnData)

contracts/libraries/TransactionHelper.sol:
  397  
  398:         assembly {
  399: 
  399              success := call(gas(), bootloaderAddr, amount, 0, 0, 0, 0)

contracts/libraries/UnsafeBytesCalldata.sol:
  18      function readUint16(bytes calldata _bytes, uint256 _start) internal pure returns (uint16 result) {
  19:         assembly {
  20: 

  20              let offset := sub(_bytes.offset, 30)

  25      function readUint64(bytes calldata _bytes, uint256 _start) internal pure returns (uint64 result) {
  26:         assembly {

contracts/openzeppelin/utils/Address.sol:
  299              /// @solidity memory-safe-assembly
  300:             assembly {

```
